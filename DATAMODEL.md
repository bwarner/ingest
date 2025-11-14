```markdown
# Data Model

## Overview

This document defines the data structures for the Message Automation Hub. The system uses a code-generation approach where users define rules in Markdown, which are then compiled into executable Node.js projects.

**Storage Strategy**: DynamoDB for all operational data, S3 for binary content and archives.

## Core Entities

### User

Represents a system user who creates and manages automation rules.

```typescript
interface User {
  id: string;                    // UUID (PK)
  email: string;                 // User's email address
  displayName: string;           // User's display name
  createdAt: string;            // ISO 8601 timestamp
  updatedAt: string;            // ISO 8601 timestamp
  status: UserStatus;           // active | suspended | deleted
}

enum UserStatus {
  ACTIVE = 'active',
  SUSPENDED = 'suspended',
  DELETED = 'deleted'
}
```

**DynamoDB Table: `Users`**
```
PK: USER#<id>
SK: METADATA
GSI1PK: EMAIL#<email>
GSI1SK: USER#<id>
```

### Rule

Represents an automation rule defined in Markdown format. Rules are parsed and compiled into executable code.

```typescript
interface Rule {
  id: string;                    // UUID (PK)
  userId: string;                // Foreign key to User
  name: string;                  // Human-readable rule name
  description?: string;          // Optional rule description
  markdownSource: string;        // Original markdown definition
  generatedCode: string;         // Compiled Node.js code
  codeVersion: number;           // Version of generated code
  status: RuleStatus;           // draft | active | paused | error
  createdAt: string;            // ISO 8601 timestamp
  updatedAt: string;            // ISO 8601 timestamp
  lastExecutedAt?: string;      // ISO 8601 timestamp
  executionCount: number;       // Total number of executions
  errorCount: number;           // Total number of errors
  metadata: RuleMetadata;       // Additional rule information
}

enum RuleStatus {
  DRAFT = 'draft',               // Being edited, not yet active
  ACTIVE = 'active',             // Running and processing messages
  PAUSED = 'paused',             // Temporarily disabled
  ERROR = 'error'                // Failed compilation or runtime error
}

interface RuleMetadata {
  triggers: TriggerConfig[];     // What activates this rule
  actions: ActionConfig[];       // What this rule does
  dependencies: string[];        // NPM packages required
  estimatedCost?: number;        // Estimated cost per execution
}
```

**DynamoDB Table: `Rules`**
```
PK: RULE#<id>
SK: METADATA
GSI1PK: USER#<userId>
GSI1SK: STATUS#<status>#<updatedAt>
GSI2PK: USER#<userId>
GSI2SK: UPDATED#<updatedAt>
```

### Channel

Represents a connected communication channel (email, SMS, etc.) that can trigger rules.

```typescript
interface Channel {
  id: string;                    // UUID (PK)
  userId: string;                // Foreign key to User
  type: ChannelType;            // email | sms | webhook | slack
  name: string;                  // User-defined channel name
  config: ChannelConfig;        // Channel-specific configuration
  credentials?: ChannelCredentials; // Encrypted auth credentials
  status: ChannelStatus;        // connected | disconnected | error
  createdAt: string;            // ISO 8601 timestamp
  updatedAt: string;            // ISO 8601 timestamp
  lastMessageAt?: string;       // ISO 8601 timestamp
  messageCount: number;         // Total messages received
}

enum ChannelType {
  EMAIL = 'email',
  SMS = 'sms',
  WEBHOOK = 'webhook',
  SLACK = 'slack'
}

enum ChannelStatus {
  CONNECTED = 'connected',
  DISCONNECTED = 'disconnected',
  ERROR = 'error'
}

interface ChannelConfig {
  // Email-specific
  emailProvider?: 'gmail' | 'outlook' | 'imap';
  emailAddress?: string;
  imapHost?: string;
  imapPort?: number;
  folders?: string[];           // Which folders to monitor
  
  // SMS-specific
  phoneNumber?: string;
  smsProvider?: 'twilio' | 'messagebird';
  
  // Webhook-specific
  webhookUrl?: string;
  webhookSecret?: string;
  
  // Slack-specific
  workspaceId?: string;
  channelIds?: string[];
}

interface ChannelCredentials {
  // Stored encrypted
  accessToken?: string;
  refreshToken?: string;
  apiKey?: string;
  apiSecret?: string;
}
```

**DynamoDB Table: `Channels`**
```
PK: CHANNEL#<id>
SK: METADATA
GSI1PK: USER#<userId>
GSI1SK: TYPE#<type>#<createdAt>
```

### TriggerConfig

Defines what activates a rule. Part of Rule metadata.

```typescript
interface TriggerConfig {
  channelId: string;            // Which channel to monitor
  conditions: TriggerCondition[]; // What to look for
}

interface TriggerCondition {
  field: string;                // 'subject' | 'body' | 'from' | 'to'
  operator: ConditionOperator;  // contains | equals | matches | startsWith
  value: string;                // Pattern to match
  caseSensitive?: boolean;      // Default: false
}

enum ConditionOperator {
  CONTAINS = 'contains',
  EQUALS = 'equals',
  MATCHES = 'matches',          // Regex
  STARTS_WITH = 'startsWith',
  ENDS_WITH = 'endsWith'
}
```

### ActionConfig

Defines what a rule does when triggered. Part of Rule metadata.

```typescript
interface ActionConfig {
  type: ActionType;
  config: Record<string, any>;  // Action-specific configuration
  order: number;                // Execution order (0-indexed)
  continueOnError: boolean;     // Whether to continue if this action fails
}

enum ActionType {
  GOOGLE_SHEETS = 'google_sheets',
  WEBHOOK = 'webhook',
  EMAIL = 'email',
  SLACK = 'slack',
  CHATGPT = 'chatgpt',
  DATABASE = 'database'
}

// Example action configs:

interface GoogleSheetsActionConfig {
  spreadsheetId: string;
  sheetName: string;
  operation: 'append' | 'update' | 'find_and_update';
  mapping: Record<string, string>; // column -> extracted field
}

interface WebhookActionConfig {
  url: string;
  method: 'POST' | 'PUT' | 'PATCH';
  headers?: Record<string, string>;
  bodyTemplate: string;         // JSON template with placeholders
}

interface ChatGPTActionConfig {
  prompt: string;               // Prompt template with placeholders
  model: string;                // gpt-4, gpt-3.5-turbo, etc.
  temperature?: number;
  maxTokens?: number;
  outputField: string;          // Where to store the response
}
```

### Message

Represents an incoming message that may trigger rules.

```typescript
interface Message {
  id: string;                    // UUID (PK)
  channelId: string;             // Foreign key to Channel
  externalId?: string;           // ID from source system
  receivedAt: string;           // ISO 8601 timestamp
  processedAt?: string;         // ISO 8601 timestamp
  status: MessageStatus;        // pending | processed | failed | ignored
  rawContent: string;           // Original message content (or S3 key if large)
  parsedContent: ParsedMessage; // Structured message data
  matchedRuleIds: string[];     // Rules that matched this message
  metadata: MessageMetadata;    // Additional message information
  ttl?: number;                 // Unix timestamp for DynamoDB TTL
}

enum MessageStatus {
  PENDING = 'pending',
  PROCESSED = 'processed',
  FAILED = 'failed',
  IGNORED = 'ignored'
}

interface ParsedMessage {
  from: string;
  to?: string;
  subject?: string;
  body: string;
  timestamp: string;
  attachments?: Attachment[];
  headers?: Record<string, string>;
}

interface Attachment {
  filename: string;
  contentType: string;
  size: number;
  s3Key: string;                // S3 object key
  s3Bucket: string;             // S3 bucket name
}

interface MessageMetadata {
  labels?: string[];
  folder?: string;
  priority?: 'high' | 'normal' | 'low';
  isRead?: boolean;
}
```

**DynamoDB Table: `Messages`**
```
PK: MESSAGE#<id>
SK: METADATA
GSI1PK: CHANNEL#<channelId>
GSI1SK: RECEIVED#<receivedAt>
GSI2PK: STATUS#<status>
GSI2SK: RECEIVED#<receivedAt>
TTL: ttl (auto-delete after 90 days)
```

### Execution

Represents a single execution of a rule against a message.

```typescript
interface Execution {
  id: string;                    // UUID (PK)
  ruleId: string;                // Foreign key to Rule
  messageId: string;             // Foreign key to Message
  startedAt: string;            // ISO 8601 timestamp
  completedAt?: string;         // ISO 8601 timestamp
  status: ExecutionStatus;      // running | success | partial | failed
  extractedData: Record<string, any>; // Data extracted from message
  actionResults: ActionResult[]; // Results of each action
  error?: ExecutionError;       // Error details if failed
  logs: string[];               // Execution logs (or S3 key if large)
  durationMs: number;           // Execution duration
  costEstimate?: number;        // Estimated cost of execution
  ttl?: number;                 // Unix timestamp for DynamoDB TTL
}

enum ExecutionStatus {
  RUNNING = 'running',
  SUCCESS = 'success',
  PARTIAL = 'partial',          // Some actions succeeded
  FAILED = 'failed'
}

interface ActionResult {
  actionType: ActionType;
  order: number;
  status: 'success' | 'failed';
  startedAt: string;
  completedAt: string;
  output?: any;                 // Action-specific output
  error?: string;               // Error message if failed
}

interface ExecutionError {
  code: string;                 // Error code
  message: string;              // Error message
  stack?: string;               // Stack trace
  recoverable: boolean;         // Whether retry might succeed
}
```

**DynamoDB Table: `Executions`**
```
PK: EXECUTION#<id>
SK: METADATA
GSI1PK: RULE#<ruleId>
GSI1SK: STARTED#<startedAt>
GSI2PK: MESSAGE#<messageId>
GSI2SK: STARTED#<startedAt>
GSI3PK: STATUS#<status>
GSI3SK: STARTED#<startedAt>
TTL: ttl (auto-delete after 90 days)
```

### RuleProject

Represents the generated Node.js project for a rule.

```typescript
interface RuleProject {
  id: string;                    // UUID (matches Rule.id) (PK)
  ruleId: string;                // Foreign key to Rule
  version: number;              // Project version number
  packageJson: object;          // package.json content
  dockerfileContent: string;    // Generated Dockerfile
  sourceFiles: SourceFile[];    // Generated source files
  buildStatus: BuildStatus;     // pending | building | success | failed
  buildError?: string;          // Build error message
  dockerImageTag?: string;      // Docker image tag if built
  s3ArtifactKey?: string;       // S3 key for project archive
  createdAt: string;            // ISO 8601 timestamp
  builtAt?: string;             // ISO 8601 timestamp
}

enum BuildStatus {
  PENDING = 'pending',
  BUILDING = 'building',
  SUCCESS = 'success',
  FAILED = 'failed'
}

interface SourceFile {
  path: string;                 // File path relative to project root
  content: string;              // File content (or S3 key if large)
  language: 'typescript' | 'javascript' | 'json' | 'dockerfile';
}
```

**DynamoDB Table: `RuleProjects`**
```
PK: PROJECT#<ruleId>
SK: VERSION#<version>
GSI1PK: RULE#<ruleId>
GSI1SK: CREATED#<createdAt>
```

### Integration

Represents a connected third-party service integration (Google, OpenAI, etc.).

```typescript
interface Integration {
  id: string;                    // UUID (PK)
  userId: string;                // Foreign key to User
  service: IntegrationService;  // Which service
  displayName: string;          // User-defined name
  credentials: object;          // Encrypted credentials
  scopes: string[];             // OAuth scopes granted
  status: IntegrationStatus;    // connected | expired | error
  createdAt: string;            // ISO 8601 timestamp
  updatedAt: string;            // ISO 8601 timestamp
  expiresAt?: string;           // ISO 8601 timestamp for token expiration
  lastUsedAt?: string;          // ISO 8601 timestamp
  metadata: IntegrationMetadata;
}

enum IntegrationService {
  GOOGLE = 'google',
  OPENAI = 'openai',
  SLACK = 'slack',
  TWILIO = 'twilio',
  STRIPE = 'stripe',
  MAILGUN = 'mailgun'
}

enum IntegrationStatus {
  CONNECTED = 'connected',
  EXPIRED = 'expired',
  ERROR = 'error',
  REVOKED = 'revoked'
}

interface IntegrationMetadata {
  accountEmail?: string;
  accountId?: string;
  permissions?: string[];
}
```

**DynamoDB Table: `Integrations`**
```
PK: INTEGRATION#<id>
SK: METADATA
GSI1PK: USER#<userId>
GSI1SK: SERVICE#<service>#<createdAt>
```

## DynamoDB Table Design

### Single Table Design Option

For maximum efficiency, you could use a single table with the following access patterns:

**Table: `MessageAutomationHub`**

| PK | SK | GSI1PK | GSI1SK | GSI2PK | GSI2SK | Entity Type |
|----|----|---------|---------|---------|---------| ------------|
| USER#123 | METADATA | EMAIL#user@example.com | USER#123 | - | - | User |
| RULE#456 | METADATA | USER#123 | STATUS#active#2025-11-14 | USER#123 | UPDATED#2025-11-14 | Rule |
| CHANNEL#789 | METADATA | USER#123 | TYPE#email#2025-11-14 | - | - | Channel |
| MESSAGE#111 | METADATA | CHANNEL#789 | RECEIVED#2025-11-14 | STATUS#pending | RECEIVED#2025-11-14 | Message |
| EXECUTION#222 | METADATA | RULE#456 | STARTED#2025-11-14 | MESSAGE#111 | STARTED#2025-11-14 | Execution |
| PROJECT#456 | VERSION#1 | RULE#456 | CREATED#2025-11-14 | - | - | RuleProject |
| INTEGRATION#333 | METADATA | USER#123 | SERVICE#google#2025-11-14 | - | - | Integration |

### Multi-Table Design Option

Alternatively, use separate tables (as shown in entity definitions above) for clearer separation and easier management.

**Recommendation**: Start with multi-table for MVP, migrate to single-table if you need better cost optimization or performance.

## Access Patterns

### User Operations
1. Get user by ID: `Query Users where PK = USER#<id>`
2. Get user by email: `Query Users.GSI1 where GSI1PK = EMAIL#<email>`

### Rule Operations
1. Get rule by ID: `Query Rules where PK = RULE#<id>`
2. List user's rules: `Query Rules.GSI1 where GSI1PK = USER#<userId>`
3. List active rules for user: `Query Rules.GSI1 where GSI1PK = USER#<userId> and begins_with(GSI1SK, 'STATUS#active')`
4. List recently updated rules: `Query Rules.GSI2 where GSI2PK = USER#<userId>, sort by GSI2SK desc`

### Channel Operations
1. Get channel by ID: `Query Channels where PK = CHANNEL#<id>`
2. List user's channels: `Query Channels.GSI1 where GSI1PK = USER#<userId>`
3. List user's email channels: `Query Channels.GSI1 where GSI1PK = USER#<userId> and begins_with(GSI1SK, 'TYPE#email')`

### Message Operations
1. Get message by ID: `Query Messages where PK = MESSAGE#<id>`
2. List channel messages: `Query Messages.GSI1 where GSI1PK = CHANNEL#<channelId>, sort by GSI1SK desc`
3. List pending messages: `Query Messages.GSI2 where GSI2PK = STATUS#pending, sort by GSI2SK desc`

### Execution Operations
1. Get execution by ID: `Query Executions where PK = EXECUTION#<id>`
2. List rule executions: `Query Executions.GSI1 where GSI1PK = RULE#<ruleId>, sort by GSI1SK desc`
3. List message executions: `Query Executions.GSI2 where GSI2PK = MESSAGE#<messageId>`
4. List failed executions: `Query Executions.GSI3 where GSI3PK = STATUS#failed, sort by GSI3SK desc`

### RuleProject Operations
1. Get latest project version: `Query RuleProjects where PK = PROJECT#<ruleId>, sort by SK desc, limit 1`
2. Get specific version: `Query RuleProjects where PK = PROJECT#<ruleId> and SK = VERSION#<version>`
3. List all versions: `Query RuleProjects where PK = PROJECT#<ruleId>`

### Integration Operations
1. Get integration by ID: `Query Integrations where PK = INTEGRATION#<id>`
2. List user integrations: `Query Integrations.GSI1 where GSI1PK = USER#<userId>`
3. Get user's Google integration: `Query Integrations.GSI1 where GSI1PK = USER#<userId> and begins_with(GSI1SK, 'SERVICE#google')`

## Data Retention & TTL

DynamoDB TTL is set on items to automatically delete old data:

- **Messages**: 90 days (set `ttl` to `receivedAt + 90 days`)
- **Executions**: 90 days (set `ttl` to `startedAt + 90 days`)
- **Old rule versions**: Keep latest 3 versions, delete older ones via Lambda
- **Users/Rules/Channels/Integrations**: No TTL (permanent)

Before deletion, archive to S3 via DynamoDB Streams:
```
DynamoDB → DynamoDB Streams → Lambda → S3 (Glacier)
```

## S3 Storage Structure

```
message-automation-hub/
├── users/
│   └── {userId}/
│       ├── messages/
│       │   └── {messageId}/
│       │       ├── raw.txt
│       │       └── attachments/
│       │           └── {filename}
│       ├── executions/
│       │   └── {executionId}/
│       │       └── logs.txt
│       └── projects/
│           └── {ruleId}/
│               └── {version}/
│                   └── project.tar.gz
└── archives/
    ├── messages/
    │   └── year={year}/month={month}/day={day}/
    └── executions/
        └── year={year}/month={month}/day={day}/
```

## Capacity Planning

### DynamoDB Provisioning

**On-Demand Mode** (recommended for MVP):
- No capacity planning required
- Pay per request
- Auto-scales to handle traffic

**Provisioned Mode** (if you have predictable traffic):
- Users table: 5 RCU / 5 WCU
- Rules table: 25 RCU / 10 WCU
- Messages table: 100 RCU / 50 WCU
- Executions table: 100 RCU / 50 WCU
- Channels table: 10 RCU / 5 WCU
- Integrations table: 10 RCU / 5 WCU
- RuleProjects table: 10 RCU / 10 WCU

### Item Size Estimates

- User: ~500 bytes
- Rule: ~10 KB (with generated code)
- Channel: ~2 KB
- Message: ~5 KB (raw content in S3 if >5KB)
- Execution: ~3 KB (logs in S3 if >3KB)
- RuleProject: ~2 KB (source files in S3)
- Integration: ~2 KB

### Cost Estimates (1000 users, 10K messages/day)

**DynamoDB (On-Demand)**:
- Writes: ~12M/month × $1.25/million = $15
- Reads: ~30M/month × $0.25/million = $7.50
- Storage: 50GB × $0.25/GB = $12.50
- **Total: ~$35/month**

**S3**:
- Storage (Standard): 100GB × $0.023/GB = $2.30
- Storage (Glacier): 1TB × $0.004/GB = $4.00
- Requests: ~1M × $0.005/1000 = $5.00
- **Total: ~$11.30/month**

## Security

### Encryption
- **At Rest**: DynamoDB encryption enabled (AWS-managed KMS keys)
- **In Transit**: All API calls over HTTPS/TLS 1.2+
- **Credentials**: Encrypted using AWS KMS before storing in DynamoDB
- **S3 Objects**: Server-side encryption (SSE-S3 or SSE-KMS)

### IAM Policies
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:UpdateItem",
        "dynamodb:DeleteItem",
        "dynamodb:Query"
      ],
      "Resource": "arn:aws:dynamodb:*:*:table/MessageAutomationHub*",
      "Condition": {
        "ForAllValues:StringEquals": {
          "dynamodb:LeadingKeys": ["USER#${aws:userid}"]
        }
      }
    }
  ]
}
```

### Sensitive Data
Fields containing sensitive data (encrypted with KMS):
- `Channel.credentials`
- `Integration.credentials`
- User email addresses (indexed but encrypted)
- OAuth tokens and API keys

## Monitoring

### CloudWatch Metrics
- DynamoDB consumed read/write capacity
- DynamoDB throttled requests
- Lambda execution duration and errors
- S3 request count and data transfer
- TTL delete metrics

### CloudWatch Alarms
- Throttled requests > 10/minute
- Failed executions > 5% of total
- Message processing latency > 5 minutes
- Storage approaching limits

### DynamoDB Streams
Enable streams on all tables for:
- Real-time processing triggers
- Data replication to analytics store
- Audit logging
- Archive to S3 before TTL deletion
```

Save this as `DATAMODEL.md` in your repository.
