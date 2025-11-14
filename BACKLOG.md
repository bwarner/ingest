```markdown
# Product Backlog

## Overview

This backlog tracks all work items for the Message Automation Hub project. Items are organized by epic and prioritized within each epic.

**For Cursor IDE Users**: This document is optimized for AI-assisted development. Each task includes context and can be used as a prompt for implementation guidance.

**Priority Levels:**
- P0: Critical - MVP blocker
- P1: High - Needed for launch
- P2: Medium - Important but can wait
- P3: Low - Nice to have

**Status:**
- 🔴 Not Started
- 🟡 In Progress
- 🟢 Complete
- 🔵 Blocked

**Quick Reference**:
- See `PRD.md` for product requirements
- See `DATAMODEL.md` for data structures
- See `SPRINT-PLAN.md` for execution timeline

---

## How to Use This Backlog with Cursor

1. **Select a task** from the backlog below
2. **Copy the task ID and description** (e.g., "INFRA-001: Initialize Git repository")
3. **In Cursor, prompt**: "Help me implement [task ID]. Reference PRD.md and DATAMODEL.md for context."
4. **Review generated code** and iterate as needed
5. **Update task status** when complete

---

## Epic 1: Project Setup & Infrastructure

**Context**: Set up AWS serverless infrastructure using CDK, following patterns from ScansafeGuard project.

### Infrastructure Setup

**INFRA-001**: Initialize Git repository with proper .gitignore (P0) 🔴
```
Create .gitignore for Node.js/TypeScript project with Nx monorepo.
Exclude: node_modules, dist, .env, cdk.out, *.js.map, .DS_Store
Include CDK and AWS-specific exclusions.
```

**INFRA-002**: Set up Nx monorepo structure (P0) 🔴
```
Initialize Nx workspace for message-automation-hub.
Create apps: api, frontend, rule-compiler
Create libs: shared, db-client, aws-constructs
Configure tsconfig paths and build targets.
```

**INFRA-003**: Configure TypeScript for all packages (P0) 🔴
```
Set up strict TypeScript configuration.
Enable: strict, noImplicitAny, strictNullChecks
Configure path aliases for cleaner imports.
Add shared types package.
```

**INFRA-004**: Set up ESLint and Prettier (P1) 🔴
```
Install and configure ESLint with TypeScript support.
Add Prettier with consistent formatting rules.
Configure pre-commit hooks with husky and lint-staged.
Add npm scripts for linting and formatting.
```

**INFRA-005**: Create CDK app structure for infrastructure (P0) 🔴
```
Initialize CDK TypeScript app in infra/ directory.
Create stacks: NetworkStack, DataStack, ComputeStack, ApiStack
Set up CDK context for multi-environment deployment.
Reference: Use CDK patterns from ScansafeGuard project.
```

**INFRA-006**: Configure multiple environments (dev, staging, prod) (P1) 🔴
```
Create environment-specific config files.
Set up CDK context for dev/staging/prod.
Configure environment variables per environment.
Add deploy scripts for each environment.
```

**INFRA-007**: Set up AWS account structure (deployer + target accounts) (P1) 🔴
```
Configure CDK for cross-account deployment.
Create deployer account setup with assume role.
Set up target accounts with proper IAM roles.
Document account setup in DEPLOYMENT.md.
```

**INFRA-008**: Configure GitHub Actions for CI/CD (P1) 🔴
```
Create .github/workflows/ci.yml for tests and linting.
Create .github/workflows/deploy-dev.yml for auto-deploy to dev.
Create .github/workflows/deploy-prod.yml for manual prod deploy.
Add secrets for AWS credentials and environment configs.
```

### DynamoDB Tables

**Context**: Create DynamoDB tables following single-table design patterns. Reference DATAMODEL.md for schemas.

**DB-001**: Create Users table with CDK (P0) 🔴
```
Implement Users table with:
- PK: USER#<id>, SK: METADATA
- GSI1: EMAIL#<email> → USER#<id>
- Enable encryption, point-in-time recovery
Reference: DATAMODEL.md User entity schema
```

**DB-002**: Create Rules table with GSIs (P0) 🔴
```
Implement Rules table with:
- PK: RULE#<id>, SK: METADATA
- GSI1: USER#<userId> → STATUS#<status>#<updatedAt>
- GSI2: USER#<userId> → UPDATED#<updatedAt>
Reference: DATAMODEL.md Rule entity schema
```

**DB-003**: Create Channels table with GSIs (P0) 🔴
```
Implement Channels table with:
- PK: CHANNEL#<id>, SK: METADATA
- GSI1: USER#<userId> → TYPE#<type>#<createdAt>
Reference: DATAMODEL.md Channel entity schema
```

**DB-004**: Create Messages table with GSIs and TTL (P0) 🔴
```
Implement Messages table with:
- PK: MESSAGE#<id>, SK: METADATA
- GSI1: CHANNEL#<channelId> → RECEIVED#<receivedAt>
- GSI2: STATUS#<status> → RECEIVED#<receivedAt>
- TTL attribute: ttl (90 days)
Reference: DATAMODEL.md Message entity schema
```

**DB-005**: Create Executions table with GSIs and TTL (P0) 🔴
```
Implement Executions table with:
- PK: EXECUTION#<id>, SK: METADATA
- GSI1: RULE#<ruleId> → STARTED#<startedAt>
- GSI2: MESSAGE#<messageId> → STARTED#<startedAt>
- GSI3: STATUS#<status> → STARTED#<startedAt>
- TTL attribute: ttl (90 days)
Reference: DATAMODEL.md Execution entity schema
```

**DB-006**: Create RuleProjects table (P1) 🔴
```
Implement RuleProjects table with:
- PK: PROJECT#<ruleId>, SK: VERSION#<version>
- GSI1: RULE#<ruleId> → CREATED#<createdAt>
Reference: DATAMODEL.md RuleProject entity schema
```

**DB-007**: Create Integrations table (P1) 🔴
```
Implement Integrations table with:
- PK: INTEGRATION#<id>, SK: METADATA
- GSI1: USER#<userId> → SERVICE#<service>#<createdAt>
Reference: DATAMODEL.md Integration entity schema
```

**DB-008**: Configure DynamoDB Streams on all tables (P1) 🔴
```
Enable DynamoDB Streams on all tables.
Set stream view type to NEW_AND_OLD_IMAGES.
Prepare for Lambda triggers (archival, notifications).
```

**DB-009**: Set up backup and point-in-time recovery (P2) 🔴
```
Enable point-in-time recovery on all tables.
Configure automated daily backups.
Set backup retention to 35 days.
```

### S3 Storage

**Context**: Create S3 buckets with encryption and lifecycle policies.

**S3-001**: Create S3 bucket for message attachments (P0) 🔴
```
Create S3 bucket: {env}-message-automation-attachments
Enable versioning and encryption (SSE-S3).
Configure bucket policy for Lambda access.
Set CORS for pre-signed URL uploads.
```

**S3-002**: Create S3 bucket for rule projects (P1) 🔴
```
Create S3 bucket: {env}-message-automation-projects
Enable versioning and encryption.
Store generated code archives and build artifacts.
```

**S3-003**: Create S3 bucket for archives (Glacier) (P2) 🔴
```
Create S3 bucket: {env}-message-automation-archives
Configure lifecycle policy: Standard → Glacier after 7 days.
Use for long-term storage of old messages/executions.
```

**S3-004**: Configure S3 lifecycle policies (P2) 🔴
```
Set lifecycle rules on attachments bucket:
- Delete after 90 days
- Transition to Infrequent Access after 30 days
```

**S3-005**: Set up bucket encryption (P1) 🔴
```
Enable default encryption on all buckets.
Use AWS-managed keys (SSE-S3) or customer-managed (SSE-KMS).
Enforce encryption via bucket policy.
```

**S3-006**: Configure CORS for web uploads (P1) 🔴
```
Add CORS configuration to attachments bucket.
Allow origins from frontend domain.
Enable GET, PUT, POST methods for pre-signed URLs.
```

### Security & IAM

**Context**: Implement security best practices with least-privilege IAM roles.

**SEC-001**: Set up KMS keys for encryption (P0) 🔴
```
Create KMS key for DynamoDB table encryption.
Create KMS key for S3 bucket encryption.
Create KMS key for Secrets Manager.
Configure key policies with proper access controls.
```

**SEC-002**: Create IAM roles for Lambda functions (P0) 🔴
```
Create roles for each Lambda function type:
- ApiHandlerRole: DynamoDB read/write
- MessageProcessorRole: DynamoDB, S3, SES
- RuleExecutorRole: DynamoDB, S3, ECR, ECS
- BuilderRole: ECR, CodeBuild, S3
Apply principle of least privilege.
```

**SEC-003**: Configure Secrets Manager for API keys (P1) 🔴
```
Create Secrets Manager entries for:
- OpenAI API key
- Twilio credentials
- Google OAuth client secrets
Grant Lambda functions read-only access.
```

**SEC-004**: Set up AWS Cognito user pool (P0) 🔴
```
Create Cognito User Pool for authentication.
Configure password policy (min 8 chars, complexity).
Enable MFA (optional).
Set up email verification.
Configure custom attributes if needed.
```

**SEC-005**: Configure Cognito identity pool (P1) 🔴
```
Create Cognito Identity Pool.
Link to User Pool.
Configure IAM roles for authenticated users.
Set up permissions for API Gateway access.
```

**SEC-006**: Implement row-level security policies (P2) 🔴
```
Add Lambda authorizer to verify user ownership.
Implement DynamoDB query filters by userId.
Ensure users can only access their own data.
```

---

## Epic 2: Core Backend Services

**Context**: Build serverless API using Lambda and API Gateway. Use TypeScript and follow AWS Lambda best practices.

### API Gateway & Lambda

**API-001**: Create API Gateway REST API (P0) 🔴
```
Create REST API in CDK.
Configure stages: dev, staging, prod.
Set up request/response models.
Enable API Gateway logging.
Add usage plans and API keys (if needed).
```

**API-002**: Set up request validation (P1) 🔴
```
Define request validators for all endpoints.
Create JSON schemas for request bodies.
Validate query parameters and headers.
Return 400 Bad Request for invalid requests.
```

**API-003**: Configure CORS (P0) 🔴
```
Enable CORS on all API endpoints.
Allow origins from frontend domain(s).
Configure allowed methods: GET, POST, PUT, DELETE, OPTIONS.
Set appropriate headers: Authorization, Content-Type.
```

**API-004**: Implement API rate limiting (P2) 🔴
```
Configure throttling on API Gateway.
Set burst limit: 100, rate limit: 50 req/sec.
Implement custom rate limiting per user if needed.
Return 429 Too Many Requests when exceeded.
```

**API-005**: Set up custom domain and SSL (P2) 🔴
```
Configure custom domain in API Gateway.
Create/import SSL certificate in ACM.
Set up Route53 DNS records.
Configure domain mapping to API stages.
```

### User Management

**USER-001**: Create user registration Lambda (P0) 🔴
```
Implement POST /auth/register endpoint.
Validate email format and password strength.
Create user in Cognito and DynamoDB Users table.
Send verification email.
Return user object and tokens.
Reference: DATAMODEL.md User entity
```

**USER-002**: Create user login/authentication Lambda (P0) 🔴
```
Implement POST /auth/login endpoint.
Authenticate with Cognito.
Verify email confirmation status.
Generate and return JWT tokens.
Handle incorrect credentials gracefully.
```

**USER-003**: Implement JWT token generation (P0) 🔴
```
Use Cognito tokens for authentication.
Implement token refresh logic.
Set appropriate token expiration (1 hour access, 30 days refresh).
Add userId and email to token claims.
```

**USER-004**: Create user profile CRUD endpoints (P1) 🔴
```
Implement GET /users/me - Get current user profile
Implement PUT /users/me - Update profile (name, settings)
Implement DELETE /users/me - Delete account
All endpoints require authentication.
```

**USER-005**: Implement password reset flow (P2) 🔴
```
Implement POST /auth/forgot-password endpoint.
Send reset code via Cognito.
Implement POST /auth/confirm-password endpoint.
Validate reset code and update password.
```

**USER-006**: Add email verification (P2) 🔴
```
Implement POST /auth/verify-email endpoint.
Accept verification code from email.
Mark user as verified in Cognito.
Auto-verify in development environment.
```

### Channel Management

**CHAN-001**: Create channel CRUD API endpoints (P0) 🔴
```
Implement POST /channels - Create channel
Implement GET /channels - List user's channels
Implement GET /channels/:id - Get channel details
Implement PUT /channels/:id - Update channel
Implement DELETE /channels/:id - Delete channel
Reference: DATAMODEL.md Channel entity
```

**CHAN-002**: Implement Gmail OAuth flow (P0) 🔴
```
Create GET /channels/gmail/auth - Initiate OAuth
Generate OAuth URL with appropriate scopes.
Implement GET /channels/gmail/callback - Handle callback
Exchange code for tokens, store in DynamoDB.
Encrypt credentials before storage.
```

**CHAN-003**: Create Gmail message polling Lambda (P0) 🔴
```
Create scheduled Lambda (every 5 minutes).
For each connected Gmail channel:
- Fetch new messages via Gmail API
- Parse message content
- Store in Messages table
- Trigger rule matching
Handle rate limits and errors gracefully.
```

**CHAN-004**: Implement SES email receiving (P1) 🔴
```
Configure SES to receive emails for domain.
Set up S3 bucket for email storage.
Create Lambda triggered by SES receipt rule.
Parse email from S3, store in Messages table.
```

**CHAN-005**: Create SMS webhook endpoint (P1) 🔴
```
Implement POST /webhooks/sms endpoint.
Accept Twilio webhook format.
Validate webhook signature.
Parse SMS content and store in Messages table.
Return 200 OK to acknowledge receipt.
```

**CHAN-006**: Implement Twilio SMS integration (P1) 🔴
```
Create Twilio account setup flow.
Store credentials in Secrets Manager.
Configure webhook URL in Twilio console.
Test end-to-end SMS receipt.
```

**CHAN-007**: Create webhook receiver endpoint (P2) 🔴
```
Implement POST /webhooks/generic endpoint.
Accept arbitrary JSON payloads.
Validate webhook secret/signature if provided.
Store as message and trigger rule matching.
```

**CHAN-008**: Add Outlook/Microsoft OAuth support (P2) 🔴
```
Implement Microsoft Graph API OAuth flow.
Similar to Gmail OAuth (CHAN-002).
Store credentials securely.
Create polling Lambda for Outlook messages.
```

**CHAN-009**: Implement IMAP polling for custom domains (P3) 🔴
```
Accept IMAP credentials from user.
Create polling Lambda using IMAP library.
Handle different IMAP server configurations.
Store messages in Messages table.
```

### Message Processing

**MSG-001**: Create message ingestion Lambda (P0) 🔴
```
Create Lambda triggered by channel events.
Accept message from various sources.
Normalize message format.
Store in Messages table with status: pending.
Trigger rule matching engine.
Reference: DATAMODEL.md Message entity
```

**MSG-002**: Implement message parsing (email/SMS) (P0) 🔴
```
Parse email messages:
- Extract from, to, subject, body
- Handle HTML and plain text
- Extract attachments
Parse SMS messages:
- Extract phone number, message content
Return ParsedMessage object.
```

**MSG-003**: Create message storage logic (DynamoDB + S3) (P0) 🔴
```
Store message metadata in DynamoDB.
Store large message bodies (>5KB) in S3.
Store attachments in S3 attachments bucket.
Generate S3 keys, update message record with references.
Set TTL for auto-deletion after 90 days.
```

**MSG-004**: Build rule matching engine (P0) 🔴
```
Query Rules table for user's active rules.
For each rule, check trigger conditions:
- Field matching (from, subject, body)
- Operator evaluation (contains, equals, matches)
Return array of matched rule IDs.
Update message with matchedRuleIds.
```

**MSG-005**: Implement attachment handling (P1) 🔴
```
Extract attachments from email messages.
Upload to S3 attachments bucket.
Store attachment metadata in message record.
Generate pre-signed URLs for access.
Handle large attachments (stream to S3).
```

**MSG-006**: Create message search API (P2) 🔴
```
Implement GET /messages?query=...&from=...&to=...
Query Messages table by channelId and time range.
Implement filtering by status, sender.
Return paginated results.
```

**MSG-007**: Add duplicate message detection (P2) 🔴
```
Check for existing message with same externalId.
Skip processing if duplicate found.
Log duplicate detection.
Return early to avoid re-processing.
```

### Rule Management

**RULE-001**: Create rule CRUD API endpoints (P0) 🔴
```
Implement POST /rules - Create rule
Implement GET /rules - List user's rules
Implement GET /rules/:id - Get rule details
Implement PUT /rules/:id - Update rule
Implement DELETE /rules/:id - Delete rule
Implement PUT /rules/:id/status - Activate/pause rule
Reference: DATAMODEL.md Rule entity
```

**RULE-002**: Implement markdown parser for rules (P0) 🔴
```
Parse markdown rule syntax into AST.
Extract triggers, conditions, actions.
Validate syntax and structure.
Return parsed rule metadata.
See DOC-002 for markdown syntax specification.
Handle parsing errors gracefully with detailed messages.
```

**RULE-003**: Build code generator from parsed markdown (P0) 🔴
```
Take parsed rule AST as input.
Generate TypeScript code implementing rule logic:
- Import required action modules
- Implement trigger matching
- Execute actions in sequence
- Handle errors per action config
Return generated code string.
```

**RULE-004**: Create rule validation logic (P0) 🔴
```
Validate rule before saving:
- Check trigger conditions are valid
- Verify action types are supported
- Validate action config (required fields)
- Check for circular dependencies
Return validation errors if any.
```

**RULE-005**: Implement rule testing/dry-run mode (P1) 🔴
```
Implement POST /rules/:id/test endpoint.
Accept test message as input.
Run rule matching and extraction without executing actions.
Return extracted data and actions that would execute.
```

**RULE-006**: Add rule versioning (P2) 🔴
```
Increment codeVersion on each rule update.
Store previous versions in RuleProjects table.
Allow rollback to previous version.
Show version history in UI.
```

**RULE-007**: Create rule import/export (P2) 🔴
```
Implement GET /rules/:id/export - Export as JSON.
Implement POST /rules/import - Import from JSON.
Validate imported rules before creating.
Handle conflicts (duplicate names).
```

---

## Epic 3: Rule Execution Engine

**Context**: Generate executable Node.js projects from rules and run them in containers.

### Code Generation

**GEN-001**: Create Node.js project template (P0) 🔴
```
Create template directory structure:
- src/index.ts - Entry point
- src/actions/ - Action implementations
- package.json
- tsconfig.json
- Dockerfile
Template should be parameterized for different rules.
```

**GEN-002**: Build package.json generator (P0) 🔴
```
Generate package.json based on rule dependencies.
Include common dependencies:
- @aws-sdk/* for AWS services
- axios for HTTP requests
- Required action libraries
Set name, version based on rule metadata.
```

**GEN-003**: Create Dockerfile generator (P0) 🔴
```
Generate multi-stage Dockerfile:
- Stage 1: Build TypeScript
- Stage 2: Production image with minimal dependencies
Use Alpine Linux for smaller image size.
Set proper entrypoint for Lambda or standalone execution.
```

**GEN-004**: Implement TypeScript code generator (P0) 🔴
```
Generate src/index.ts with:
- Import statements for actions
- Message extraction logic
- Action execution sequence
- Error handling
- Logging
Use templates and string interpolation.
```

**GEN-005**: Add error handling code generation (P1) 🔴
```
Generate try-catch blocks around action execution.
Handle continueOnError flag per action.
Log errors with context.
Set execution status appropriately.
```

**GEN-006**: Generate logging code (P1) 🔴
```
Add structured logging throughout generated code.
Log execution start, action results, errors.
Use console.log with JSON format for CloudWatch.
Include rule ID, message ID, timestamps.
```

**GEN-007**: Create unit test generation (P3) 🔴
```
Generate basic unit tests for rule logic.
Test message extraction.
Test action configuration parsing.
Use Jest framework.
```

### Build & Deploy

**BUILD-001**: Create Docker build Lambda (P0) 🔴
```
Create Lambda triggered when rule is saved.
Extract generated project files from DynamoDB.
Write files to /tmp directory.
Execute docker build command.
Handle build errors, update RuleProject status.
```

**BUILD-002**: Set up ECR repository for rule images (P0) 🔴
```
Create ECR repository: message-automation-rules.
Configure lifecycle policy (keep latest 3 per rule).
Grant Lambda push permissions.
Tag images with: {ruleId}-v{version}.
```

**BUILD-003**: Implement build status tracking (P1) 🔴
```
Update RuleProject record with buildStatus.
Store build logs in S3 or CloudWatch.
Send notification on build completion/failure.
Allow users to query build status via API.
```

**BUILD-004**: Add build failure notifications (P1) 🔴
```
Send email notification on build failure.
Include error message and logs.
Implement POST /rules/:id/rebuild endpoint.
Set rule status to 'error' on build failure.
```

**BUILD-005**: Create build logs storage (P2) 🔴
```
Stream build output to CloudWatch Logs.
Store logs with log group per rule.
Implement GET /rules/:id/build-logs endpoint.
Return paginated logs.
```

**BUILD-006**: Implement incremental builds (P3) 🔴
```
Cache Docker layers between builds.
Only rebuild if dependencies changed.
Use build cache from previous successful builds.
Significantly faster builds after first build.
```

### Execution Runtime

**EXEC-001**: Create rule execution Lambda invoker (P0) 🔴
```
Triggered when message matches rule.
Create execution record in Executions table.
Invoke rule Lambda/container with message context.
Track execution status and results.
Reference: DATAMODEL.md Execution entity
```

**EXEC-002**: Implement ECS Fargate task runner (alternative) (P1) 🔴
```
Alternative to Lambda for long-running rules.
Run Docker container in Fargate.
Pass message context as environment variables.
Monitor task status and capture output.
Use for rules with >15 minute execution time.
```

**EXEC-003**: Build execution context (message + extracted data) (P0) 🔴
```
Create execution context object:
- Message content (from, to, subject, body)
- Extracted data based on rule patterns
- User credentials for actions
- Rule configuration
Pass as JSON to rule executor.
```

**EXEC-004**: Create execution tracking (P0) 🔴
```
Create Execution record when rule starts.
Update status: running → success/failed/partial.
Store action results array.
Calculate duration, cost estimate.
Set TTL for auto-deletion after 90 days.
```

**EXEC-005**: Implement retry logic for failed executions (P1) 🔴
```
Check ExecutionError.recoverable flag.
Retry up to 3 times with exponential backoff.
Only retry if error is transient (network, rate limit).
Don't retry on validation or config errors.
```

**EXEC-006**: Add execution timeout handling (P1) 🔴
```
Set Lambda timeout to 5 minutes.
Kill execution if exceeds timeout.
Set execution status to 'failed' with timeout error.
Log timeout for debugging.
```

**EXEC-007**: Create execution logs aggregation (P1) 🔴
```
Capture logs from rule execution.
Store in CloudWatch Logs.
Link to execution record.
Implement GET /executions/:id/logs endpoint.
```

**EXEC-008**: Implement execution cost tracking (P2) 🔴
```
Calculate estimated cost per execution:
- Lambda duration * price per ms
- DynamoDB read/write units
- S3 requests
- API calls (OpenAI, etc.)
Store in costEstimate field.
```

---

## Epic 4: Action Integrations

**Context**: Implement action modules that generated code imports. Each action is a reusable npm package.

### Google Sheets

**ACT-GS-001**: Implement Google OAuth for Sheets (P0) 🔴
```
Create OAuth flow for Google Sheets API.
Request scope: https://www.googleapis.com/auth/spreadsheets
Store credentials in Integrations table (encrypted).
Implement token refresh logic.
Handle OAuth errors gracefully.
```

**ACT-GS-002**: Create append row action (P0) 🔴
```
Create @message-automation/action-google-sheets package.
Implement appendRow(spreadsheetId, sheetName, values).
Use Google Sheets API v4.
Map extracted data to column values.
Handle authentication with stored credentials.
```

**ACT-GS-003**: Create update cell action (P1) 🔴
```
Implement updateCell(spreadsheetId, sheetName, cell, value).
Use A1 notation for cell reference.
Update single cell with extracted data.
Handle range updates if needed.
```

**ACT-GS-004**: Implement find and update action (P2) 🔴
```
Implement findAndUpdate(spreadsheetId, sheetName, searchColumn, searchValue, updateColumn, newValue).
Search for row matching criteria.
Update specific cell in that row.
Handle multiple matches (update first or all).
```

**ACT-GS-005**: Add batch operations support (P2) 🔴
```
Batch multiple sheet operations into single API call.
Improves performance for rules with multiple updates.
Use batchUpdate API endpoint.
Handle partial failures.
```

**ACT-GS-006**: Handle rate limiting (P1) 🔴
```
Implement exponential backoff for rate limit errors.
Cache API calls when possible.
Respect Google Sheets API quotas (100 requests/100 seconds/user).
Return recoverable error if rate limited.
```

### Webhooks

**ACT-WH-001**: Create HTTP POST action (P1) 🔴
```
Create @message-automation/action-webhook package.
Implement sendWebhook(url, body, options).
Support POST, PUT, PATCH methods.
Return response data and status code.
Handle network errors with retries.
```

**ACT-WH-002**: Add custom headers support (P1) 🔴
```
Accept headers object in webhook config.
Support common headers: Authorization, Content-Type, Custom-*.
Include User-Agent header.
Allow dynamic header values from extracted data.
```

**ACT-WH-003**: Implement body template engine (P1) 🔴
```
Support template variables in body: {{fieldName}}.
Replace with extracted data values.
Support nested object access: {{data.amount}}.
Handle missing variables gracefully (empty string or error).
```

**ACT-WH-004**: Add authentication methods (Bearer, Basic) (P2) 🔴
```
Support Bearer token authentication.
Support Basic auth (username:password).
Support custom authentication headers.
Store credentials securely in Integration record.
```

**ACT-WH-005**: Handle webhook retries (P2) 🔴
```
Retry on 5xx errors (server errors).
Don't retry on 4xx errors (client errors).
Use exponential backoff: 1s, 2s, 4s.
Max 3 retries before marking as failed.
```

### Email

**ACT-EM-001**: Create send email action via SES (P1) 🔴
```
Create @message-automation/action-email package.
Implement sendEmail(to, subject, body, options).
Use AWS SES for sending.
Support both HTML and plain text.
Handle SES errors and bounces.
```

**ACT-EM-002**: Implement email forwarding (P2) 🔴
```
Forward original message to another address.
Preserve original message content and attachments.
Add "Forwarded by Message Automation Hub" header.
Use SES SendRawEmail API.
```

**ACT-EM-003**: Add email templates (P2) 🔴
```
Support HTML email templates with variables.
Use template engine (Handlebars or similar).
Include common templates: notification, alert, summary.
Allow users to create custom templates.
```

**ACT-EM-004**: Support HTML emails (P2) 🔴
```
Accept HTML content in email body.
Generate plain text alternative automatically.
Support inline CSS for styling.
Test rendering in common email clients.
```

### ChatGPT/OpenAI

**ACT-AI-001**: Integrate OpenAI API (P1) 🔴
```
Create @message-automation/action-openai package.
Implement callChatGPT(prompt, options).
Use OpenAI API v1 with latest models.
Store API key in Secrets Manager.
Handle API errors and rate limits.
```

**ACT-AI-002**: Create prompt template engine (P1) 🔴
```
Support template variables in prompts: {{body}}.
Replace with message content and extracted data.
Include system prompts for consistent behavior.
Allow multi-turn conversations if needed.
```

**ACT-AI-003**: Implement response parsing (P1) 🔴
```
Extract structured data from ChatGPT response.
Support JSON response format.
Parse natural language responses into fields.
Store result in outputField for use in subsequent actions.
```

**ACT-AI-004**: Add token usage tracking (P2) 🔴
```
Track input and output tokens.
Calculate cost based on model pricing.
Store in execution record.
Warn user if approaching budget limits.
```

**ACT-AI-005**: Support multiple models (P2) 🔴
```
Support: gpt-4, gpt-4-turbo, gpt-3.5-turbo.
Allow user to select model in rule config.
Automatically fallback to cheaper model on error.
Track which model was used in execution.
```

### Slack

**ACT-SL-001**: Implement Slack OAuth (P2) 🔴
```
Create OAuth flow for Slack.
Request scopes: chat:write, channels:read.
Store workspace and bot tokens in Integrations.
Handle token expiration and refresh.
```

**ACT-SL-002**: Create post message action (P2) 🔴
```
Create @message-automation/action-slack package.
Implement postMessage(channel, text, options).
Use Slack Web API.
Support blocks for rich formatting.
Handle channel not found errors.
```

**ACT-SL-003**: Add channel selection (P2) 🔴
```
List available channels via API.
Cache channel list for performance.
Support channel names and IDs.
Handle private channels (DMs, private channels).
```

**ACT-SL-004**: Support message formatting (P3) 🔴
```
Support Slack markdown formatting.
Create blocks for rich messages (buttons, images).
Support attachments and colors.
Preview formatting in rule builder.
```

---

## Epic 5: Frontend Application

**Context**: Build Next.js application with Tailwind CSS. Focus on clean, responsive UI with good UX.

### Project Setup

**FE-001**: Create Next.js application (P0) 🔴
```
Initialize Next.js 14+ with TypeScript.
Set up app directory structure.
Configure next.config.js for API routes.
Install core dependencies.
Set up development server.
```

**FE-002**: Set up Tailwind CSS (P0) 🔴
```
Install and configure Tailwind CSS.
Set up tailwind.config.js with theme customization.
Create base styles and CSS variables.
Configure dark mode support (optional).
```

**FE-003**: Configure React Query for API calls (P0) 🔴
```
Install @tanstack/react-query.
Set up QueryClientProvider.
Create API client with axios.
Configure default query/mutation options.
Set up request/response interceptors for auth.
```

**FE-004**: Set up React Router (P0) 🔴
```
Configure Next.js routing with app directory.
Set up route groups for authenticated/public routes.
Create loading and error states per route.
Configure middleware for auth checks.
```

**FE-005**: Implement authentication context (P0) 🔴
```
Create AuthContext with login/logout/register.
Store tokens in localStorage/cookies.
Implement token refresh logic.
Provide useAuth hook for components.
Redirect to login if unauthenticated.
```

**FE-006**: Create layout components (P1) 🔴
```
Create AppLayout with sidebar and header.
Create Sidebar with navigation links.
Create Header with user menu and notifications.
Make layout responsive (mobile, tablet, desktop).
Add loading skeleton for navigation.
```

### Authentication Pages

**FE-AUTH-001**: Build login page (P0) 🔴
```
Create /login route.
Form with email and password fields.
Validation using react-hook-form.
Call POST /auth/login API.
Redirect to dashboard on success.
Show error messages on failure.
```

**FE-AUTH-002**: Build registration page (P0) 🔴
```
Create /register route.
Form with email, password, confirm password, name.
Client-side validation (email format, password strength).
Call POST /auth/register API.
Redirect to email verification page.
```

**FE-AUTH-003**: Create password reset page (P2) 🔴
```
Create /forgot-password route.
Form with email field.
Call POST /auth/forgot-password API.
Show "Check your email" message.
Create /reset-password route with code input.
```

**FE-AUTH-004**: Implement OAuth callback handlers (P1) 🔴
```
Create /auth/callback/google route.
Handle OAuth redirect with code parameter.
Exchange code for tokens via API.
Store tokens and redirect to dashboard.
Handle OAuth errors gracefully.
```

### Dashboard

**FE-DASH-001**: Create main dashboard layout (P0) 🔴
```
Create /dashboard route.
Show overview statistics (rules, executions, channels).
Display recent activity feed.
Add quick action buttons (New Rule, Connect Channel).
Make responsive for mobile/tablet.
```

**FE-DASH-002**: Build rule list view (P0) 🔴
```
Display list of user's rules.
Show rule name, status, last executed.
Add filters (status: active/paused/draft).
Add search by name.
Click rule to navigate to detail/edit page.
```

**FE-DASH-003**: Display recent activity feed (P1) 🔴
```
Show recent executions with status.
Display message previews.
Show timestamp and rule name.
Click item to view details.
Refresh automatically (real-time or polling).
```

**FE-DASH-004**: Show execution statistics (P1) 🔴
```
Display charts for:
- Executions over time (line chart)
- Success/failure rate (pie chart)
- Most active rules (bar chart)
Use chart library (recharts or chart.js).
```

**FE-DASH-005**: Add quick actions menu (P2) 🔴
```
Floating action button or prominent buttons.
Quick actions:
- Create new rule
- Connect new channel
- View recent executions
One-click access to common tasks.
```

### Rule Builder

**FE-RULE-001**: Create markdown editor component (P0) 🔴
```
Create rule editor with markdown textarea.
Split view: editor on left, preview on right.
Use Monaco Editor or CodeMirror for better editing.
Auto-save drafts to localStorage.
Save to server with Save button.
```

**FE-RULE-002**: Build live preview panel (P0) 🔴
```
Parse markdown in real-time.
Display parsed rule structure:
- Triggers
- Actions
- Expected extracted data
Show validation errors.
Update on every keystroke (debounced).
```

**FE-RULE-003**: Implement syntax highlighting (P1) 🔴
```
Highlight rule syntax in editor.
Use custom grammar for rule markdown.
Highlight keywords: TRIGGER, ACTION, EXTRACT.
Different colors for field names, patterns, actions.
```

**FE-RULE-004**: Add autocomplete for actions (P1) 🔴
```
Show autocomplete dropdown in editor.
Suggest action types: google_sheets, webhook, email, etc.
Show action parameters and examples.
Insert template when selected.
```

**FE-RULE-005**: Create rule templates library (P2) 🔴
```
Pre-built rule templates for common use cases:
- Payment tracking
- Customer notifications
- Service monitoring
Browse and preview templates.
Click to use template (populate editor).
```

**FE-RULE-006**: Build validation error display (P1) 🔴
```
Show validation errors below editor or in sidebar.
Highlight error line in editor.
Show error message with suggestion to fix.
Prevent saving if validation fails.
```

**FE-RULE-007**: Add test rule functionality (P1) 🔴
```
Add "Test Rule" button in editor.
Show modal to input test message.
Call POST /rules/:id/test API.
Display extracted data and actions that would run.
Don't actually execute actions.
```

**FE-RULE-008**: Implement rule versioning UI (P2) 🔴
```
Show version history in rule detail page.
Display changes between versions (diff view).
Allow rollback to previous version.
Show who made changes and when.
```

### Channel Management

**FE-CHAN-001**: Create channel list view (P0) 🔴
```
Display connected channels with:
- Type (email, SMS, webhook)
- Status (connected, error)
- Last message received
- Message count
Add "Connect New Channel" button.
```

**FE-CHAN-002**: Build add channel wizard (P0) 🔴
```
Multi-step wizard for adding channel:
1. Select channel type
2. Configure connection (OAuth or credentials)
3. Test connection
4. Save and complete
Handle OAuth flows in steps.
```

**FE-CHAN-003**: Implement OAuth connection flows (P0) 🔴
```
For Gmail:
- Button to connect Gmail
- Redirect to Google OAuth
- Handle callback, show success
Similar flows for Outlook, Slack.
Store connection status and show in UI.
```

**FE-CHAN-004**: Create channel settings page (P1) 🔴
```
Edit channel configuration:
- Name
- Filters (folders, labels)
- Polling frequency
Save settings via PUT /channels/:id.
Test connection button.
```

**FE-CHAN-005**: Display channel status indicators (P1) 🔴
```
Show status badge (connected, disconnected, error).
Use colors: green (connected), red (error), gray (disconnected).
Show error message on hover or in detail view.
Add reconnect button for OAuth channels.
```

### Message History

**FE-MSG-001**: Create message list view (P1) 🔴
```
Display paginated list of messages.
Show from, subject/preview, received time.
Filter by channel, date range.
Search by content.
Click to view full message.
```

**FE-MSG-002**: Build message detail modal (P1) 🔴
```
Show full message content:
- From, to, subject
- Full body
- Attachments (download links)
- Matched rules
- Executions triggered
Navigate to related executions.
```

**FE-MSG-003**: Implement search and filters (P2) 🔴
```
Search bar for message content.
Filters:
- Channel
- Date range
- Status (processed, pending, failed)
- Has attachments
Apply filters without page reload.
```

**FE-MSG-004**: Add pagination (P1) 🔴
```
Implement cursor-based pagination.
Show "Load More" button or infinite scroll.
Display page count and total messages.
Preserve filters/search when paginating.
```

### Execution History

**FE-EXEC-001**: Create execution list view (P1) 🔴
```
Display paginated list of executions.
Show rule name, status, started time, duration.
Filter by rule, status, date range.
Color code by status (green/yellow/red).
Click to view details.
```

**FE-EXEC-002**: Build execution detail view (P1) 🔴
```
Show execution details:
- Rule name and version
- Message that triggered it
- Extracted data
- Action results (success/failed)
- Error messages if failed
- Duration and cost
```

**FE-EXEC-003**: Display execution logs (P1) 🔴
```
Fetch logs via GET /executions/:id/logs.
Display in monospace font with timestamps.
Syntax highlighting for JSON logs.
Auto-scroll to bottom for recent logs.
Filter by log level (info, warn, error).
```

**FE-EXEC-004**: Show action results (P1) 🔴
```
Display each action's result:
- Action type and order
- Status (success/failed)
- Output data
- Error message if failed
Show timeline of action execution.
```

**FE-EXEC-005**: Add retry button for failed executions (P2) 🔴
```
Show "Retry" button for failed executions.
Call POST /executions/:id/retry API.
Show loading state during retry.
Refresh execution status after retry.
Disable retry if not recoverable.
```

### Integrations

**FE-INT-001**: Create integrations list page (P1) 🔴
```
Display connected integrations:
- Service (Google, OpenAI, Slack, Twilio)
- Status (connected, expired, error)
- Last used
Add "Connect New Integration" button.
```

**FE-INT-002**: Build OAuth connection flows (P1) 🔴
```
For each service (Google, Slack):
- Button to connect
- Redirect to OAuth provider
- Handle callback
- Show success/error
Test connection after connecting.
```

**FE-INT-003**: Display integration status (P1) 🔴
```
Show status badge with color coding.
Display error message if connection failed.
Show token expiration date if applicable.
Add "Reconnect" button for expired tokens.
```

**FE-INT-004**: Add disconnect/reconnect actions (P2) 🔴
```
"Disconnect" button to revoke access.
Confirm before disconnecting.
Delete integration record via API.
"Reconnect" button to refresh OAuth.
Handle scope changes in reconnect.
```

### Settings

**FE-SET-001**: Create user profile settings (P2) 🔴
```
Edit user profile:
- Display name
- Email (read-only)
- Password change
- Notification preferences
Save via PUT /users/me.
```

**FE-SET-002**: Build notification preferences (P2) 🔴
```
Configure notifications for:
- Execution failures
- Build failures
- Weekly summary
Toggle email/SMS notifications.
Set quiet hours.
```

**FE-SET-003**: Add billing/subscription page (P3) 🔴
```
Display current plan (free, pro, enterprise).
Show usage statistics (executions, storage).
Upgrade/downgrade buttons.
Payment method management (Stripe).
```

**FE-SET-004**: Create API keys management (P3) 🔴
```
List API keys with name, created date.
Generate new API key.
Revoke existing keys.
Show key only once on creation.
Use API keys for programmatic access.
```

---

## Epic 6: Monitoring & Operations

**Context**: Set up observability and operational tooling for production.

### Logging

**OPS-LOG-001**: Set up CloudWatch Logs for all Lambdas (P0) 🔴
```
Enable CloudWatch Logs for all Lambda functions.
Set log retention to 7 days (dev) or 30 days (prod).
Create log groups per function.
Grant Lambda execution role cloudwatch:PutLogEvents.
```

**OPS-LOG-002**: Implement structured logging (P1) 🔴
```
Use JSON format for all logs.
Include fields: timestamp, level, message, context.
Add correlation ID to trace request across Lambdas.
Use winston or pino for structured logging.
```

**OPS-LOG-003**: Create log aggregation dashboard (P2) 🔴
```
Create CloudWatch Insights queries for:
- Error rate over time
- Slow requests (>1s)
- Most common errors
Save queries for quick access.
Share dashboard link with team.
```

**OPS-LOG-004**: Add log retention policies (P1) 🔴
```
Set retention based on environment:
- Dev: 7 days
- Staging: 14 days
- Prod: 30 days
Export old logs to S3 for long-term storage.
```

### Metrics & Alarms

**OPS-MET-001**: Create CloudWatch dashboards (P1) 🔴
```
Create dashboard showing:
- Lambda invocations and errors
- API Gateway requests and latency
- DynamoDB read/write capacity
- Execution success/failure rate
Auto-refresh every 1 minute.
```

**OPS-MET-002**: Set up alarms for failed executions (P1) 🔴
```
Alarm when execution failure rate > 5%.
Check every 5 minutes.
Send to SNS topic for email notification.
Include runbook link in alarm description.
```

**OPS-MET-003**: Add alarms for API errors (P1) 🔴
```
Alarm when API Gateway 5xx errors > 1%.
Alarm when API latency p99 > 5s.
Send to SNS topic.
Trigger PagerDuty in prod (optional).
```

**OPS-MET-004**: Monitor DynamoDB throttling (P1) 🔴
```
Alarm on throttled requests > 10/minute.
Check all tables and GSIs.
Auto-scale capacity if on-demand not sufficient.
Send notification to investigate query patterns.
```

**OPS-MET-005**: Track execution costs (P2) 🔴
```
Create custom metric for execution costs.
Publish from execution Lambda.
Dashboard showing cost breakdown:
- Lambda duration
- DynamoDB usage
- API calls (OpenAI, etc.)
Alert if daily cost exceeds threshold.
```

**OPS-MET-006**: Set up SNS notifications for alarms (P1) 🔴
```
Create SNS topic for CloudWatch alarms.
Subscribe email addresses for notifications.
Configure alarm actions to publish to SNS.
Add SMS notifications for critical alarms (optional).
```

### Error Tracking

**OPS-ERR-001**: Integrate Sentry or similar (P2) 🔴
```
Set up Sentry project.
Install Sentry SDK in Lambda functions.
Configure error sampling rate (100% for now).
Add context to errors (user ID, rule ID, message ID).
Set up alerts in Sentry for new errors.
```

**OPS-ERR-002**: Create error notification system (P1) 🔴
```
Lambda to process error logs from CloudWatch.
Triggered by log subscription filter.
Parse errors and send to Slack/email.
Include error details and stack trace.
Link to CloudWatch Logs for investigation.
```

**OPS-ERR-003**: Build error dashboard (P2) 🔴
```
Dashboard showing:
- Error rate over time
- Most common errors
- Errors by Lambda function
- Recent errors with details
Link to Sentry or CloudWatch for details.
```

### Data Management

**OPS-DATA-001**: Create DynamoDB backup Lambda (P2) 🔴
```
Scheduled Lambda (daily) to trigger backups.
Use DynamoDB on-demand backup API.
Create backup for all tables.
Tag backups with date for easy identification.
Set retention to 35 days.
```

**OPS-DATA-002**: Build S3 archive Lambda (triggered by Streams) (P2) 🔴
```
Lambda triggered by DynamoDB Streams.
Listen for REMOVE events (TTL deletions).
Archive deleted items to S3 before removal.
Use Glacier storage class for cost savings.
Partition by date: year=2025/month=11/day=14/
```

**OPS-DATA-003**: Implement data export functionality (P3) 🔴
```
API endpoint to export user's data.
Generate CSV or JSON export.
Include all messages, executions, rules.
Email download link when ready.
Delete export after 7 days.
```

**OPS-DATA-004**: Create data deletion tools (GDPR) (P2) 🔴
```
Implement DELETE /users/me endpoint.
Hard delete all user data:
- User record
- All rules and projects
- All channels and integrations
- All messages and executions
Use DynamoDB batch writes for efficiency.
Send confirmation email.
```

---

## Epic 7: Testing & Quality

**Context**: Ensure code quality with automated tests and load testing.

### Unit Tests

**TEST-001**: Write tests for rule parser (P1) 🔴
```
Test parsing of valid markdown rules.
Test error handling for invalid syntax.
Test extraction of triggers and actions.
Use Jest with 100% code coverage goal.
Test edge cases (empty rules, malformed syntax).
```

**TEST-002**: Write tests for code generator (P1) 🔴
```
Test generated code compiles successfully.
Test action imports are correct.
Test error handling is generated properly.
Snapshot tests for generated code.
Test different rule configurations.
```

**TEST-003**: Write tests for message matcher (P1) 🔴
```
Test trigger condition matching:
- contains, equals, matches operators
- Different fields (from, subject, body)
- Case sensitivity
Test with various message formats.
Aim for >90% coverage.
```

**TEST-004**: Write tests for all action integrations (P1) 🔴
```
Mock external APIs (Google, OpenAI, etc.).
Test action execution with various inputs.
Test error handling and retries.
Test authentication and token refresh.
Use nock or msw for HTTP mocking.
Aim for >80% coverage per action.
```

**TEST-005**: Achieve >80% backend code coverage (P2) 🔴
```
Run jest --coverage to check current coverage.
Identify untested code paths.
Write tests for edge cases and error paths.
Configure coverage thresholds in jest.config.js.
Fail CI if coverage drops below 80%.
```

**TEST-006**: Write frontend component tests (P2) 🔴
```
Test React components with React Testing Library.
Test user interactions (click, input, submit).
Test loading and error states.
Mock API calls with MSW.
Aim for >70% frontend coverage.
```

### Integration Tests

**TEST-INT-001**: Create end-to-end test suite (P1) 🔴
```
Use Playwright or Cypress for E2E tests.
Test complete user flows:
- Register → Connect channel → Create rule → View execution
Run tests against deployed dev environment.
Include in CI pipeline.
```

**TEST-INT-002**: Test OAuth flows (P1) 🔴
```
Test Gmail OAuth connection flow.
Test Google Sheets OAuth flow.
Mock OAuth providers in test environment.
Verify tokens are stored correctly.
Test token refresh logic.
```

**TEST-INT-003**: Test message processing pipeline (P1) 🔴
```
Send test message through channel.
Verify message is stored in DynamoDB.
Verify rule matching works correctly.
Verify execution is triggered.
Check execution results.
End-to-end integration test.
```

**TEST-INT-004**: Test rule execution flow (P1) 🔴
```
Create test rule with all action types.
Trigger execution with test message.
Verify each action executes correctly.
Check execution logs and results.
Test error scenarios (action failures).
```

### Load Testing

**TEST-LOAD-001**: Create load test scenarios (P2) 🔴
```
Use k6 or Artillery for load testing.
Test scenarios:
- 100 concurrent users creating rules
- 1000 messages/minute ingestion
- 100 concurrent executions
Measure response times and error rates.
Identify bottlenecks.
```

**TEST-LOAD-002**: Test DynamoDB capacity (P2) 🔴
```
Load test DynamoDB with realistic query patterns.
Monitor consumed capacity and throttling.
Test GSI query performance.
Verify auto-scaling works correctly.
Document required capacity for different loads.
```

**TEST-LOAD-003**: Test Lambda concurrency limits (P2) 🔴
```
Test Lambda cold start performance.
Measure warm vs cold invocation times.
Test concurrent execution limits.
Verify reserved concurrency if configured.
Test throttling behavior under high load.
```

---

## Epic 8: Documentation

**Context**: Create comprehensive documentation for users and developers.

### Technical Documentation

**DOC-001**: Write API documentation (OpenAPI/Swagger) (P1) 🔴
```
Create OpenAPI 3.0 specification.
Document all API endpoints:
- Request/response schemas
- Authentication requirements
- Error responses
- Examples
Generate Swagger UI from spec.
Host at /api/docs.
```

**DOC-002**: Document rule markdown syntax (P0) 🔴
```
Create RULE-SYNTAX.md with:
- Complete syntax specification
- All trigger operators
- All action types with parameters
- Extraction patterns
- Examples for each action type
Include migration guide for syntax changes.
```

**DOC-003**: Create architecture diagrams (P1) 🔴
```
Create diagrams showing:
- High-level system architecture
- Data flow (message → rule → execution)
- AWS infrastructure diagram
- Security architecture
Use draw.io or similar tool.
Export as PNG and include in docs/.
```

**DOC-004**: Write deployment guide (P1) 🔴
```
Create DEPLOYMENT.md with:
- Prerequisites (AWS account, tools)
- Step-by-step deployment instructions
- Environment configuration
- Troubleshooting common issues
- Rollback procedures
Test guide with fresh AWS account.
```

**DOC-005**: Document all action types (P1) 🔴
```
Create ACTIONS.md documenting:
- Each action type with description
- Required and optional parameters
- Authentication requirements
- Examples
- Rate limits and quotas
- Common errors and solutions
```

**DOC-006**: Create developer setup guide (P1) 🔴
```
Create CONTRIBUTING.md with:
- Local development setup
- Running tests
- Code style guidelines
- Submitting pull requests
- Project structure overview
Make it easy for new developers to contribute.
```

### User Documentation

**DOC-USER-001**: Write getting started guide (P0) 🔴
```
Create docs/getting-started.md with:
- Account registration
- Connecting first channel
- Creating first rule
- Viewing execution results
Include screenshots for each step.
Test with non-technical user.
```

**DOC-USER-002**: Create rule examples library (P0) 🔴
```
Create docs/examples/ directory with:
- Payment tracking (PayPal → Google Sheets)
- Customer notifications (Order → Slack)
- Service monitoring (Alert email → Webhook)
- Lead capture (Form submission → CRM)
At least 10 real-world examples.
Include complete rule markdown.
```

**DOC-USER-003**: Document OAuth setup for each provider (P1) 🔴
```
Create guide for each OAuth provider:
- Google (Gmail, Sheets)
- Microsoft (Outlook)
- Slack
- Twilio
Include screenshots of OAuth consent screens.
Document required scopes and permissions.
```

**DOC-USER-004**: Write troubleshooting guide (P1) 🔴
```
Create docs/troubleshooting.md with:
- Common issues and solutions
- Error messages explained
- How to read execution logs
- When to contact support
- FAQ section
Update based on user support tickets.
```

**DOC-USER-005**: Create video tutorials (P3) 🔴
```
Record screencasts for:
- Getting started (5 minutes)
- Creating your first rule (10 minutes)
- Advanced rule patterns (15 minutes)
- Integrations overview (10 minutes)
Host on YouTube or Loom.
Embed in documentation.
```

---

## Epic 9: MVP Polish

**Context**: Performance optimization and UX improvements before launch.

### Performance

**PERF-001**: Optimize DynamoDB queries (P1) 🔴
```
Review all query patterns.
Ensure proper use of GSIs.
Add missing indexes if needed.
Use BatchGetItem for multiple items.
Implement query result caching where appropriate.
Measure query latency before and after.
```

**PERF-002**: Implement caching layer (P2) 🔴
```
Add Redis/ElastiCache for frequently accessed data:
- User profiles
- Active rules
- Integration credentials
Set appropriate TTLs (5-60 minutes).
Implement cache invalidation on updates.
```

**PERF-003**: Optimize Lambda cold starts (P2) 🔴
```
Minimize Lambda package size.
Use Lambda layers for common dependencies.
Enable SnapStart for Java functions (if any).
Keep functions warm with scheduled pings (optional).
Measure cold start times before and after.
```

**PERF-004**: Add CDN for frontend assets (P2) 🔴
```
Set up CloudFront distribution.
Cache static assets (JS, CSS, images).
Configure cache invalidation on deployments.
Enable compression (gzip/brotli).
Measure load time improvement.
```

### UX Improvements

**UX-001**: Add loading states everywhere (P1) 🔴
```
Add spinners/skeletons for all async operations.
Show loading state during:
- Page loads
- API calls
- Form submissions
- OAuth flows
Disable buttons while loading.
```

**UX-002**: Implement optimistic updates (P2) 🔴
```
Update UI immediately on user actions:
- Rule status toggle
- Message marking as read
- Integration disconnect
Revert if API call fails.
Show subtle loading indicator.
```

**UX-003**: Add empty states with helpful CTAs (P1) 🔴
```
Design empty states for:
- No rules yet → "Create your first rule"
- No channels → "Connect a channel"
- No executions → "Create a rule to see executions"
Include illustration and clear action button.
```

**UX-004**: Create onboarding flow (P1) 🔴
```
Multi-step wizard for new users:
1. Welcome and product overview
2. Connect first channel
3. Create first rule (guided)
4. See it in action
Use progress indicator.
Allow skipping for advanced users.
```

**UX-005**: Add keyboard shortcuts (P3) 🔴
```
Common shortcuts:
- N: New rule
- /: Focus search
- ?: Show shortcuts help
- Esc: Close modals
Display shortcuts in help menu.
Work on Mac and Windows.
```

### Bug Fixes

**BUG-001**: Fix timezone handling (P1) 🔴
```
Store all dates in UTC in database.
Convert to user's timezone in frontend.
Allow user to set timezone preference.
Handle daylight saving time correctly.
Test with various timezones.
```

**BUG-002**: Handle large message bodies (P1) 🔴
```
Store message bodies >5KB in S3.
Reference S3 key in DynamoDB.
Stream large bodies instead of loading in memory.
Truncate display in UI with "Show more" link.
Handle binary content properly.
```

**BUG-003**: Fix race conditions in rule execution (P0) 🔴
```
Use DynamoDB conditional writes for execution status.
Prevent duplicate executions of same message.
Use idempotency tokens for retries.
Add distributed locking if needed (DynamoDB or Redis).
Test with concurrent executions.
```

---

## Epic 10: Post-MVP Features

**Context**: Features to implement after successful MVP launch.

### Advanced Features

**ADV-001**: Implement rule templates marketplace (P3) 🔴
```
Build template submission system.
Allow users to share their rules.
Create template categories.
Add rating and review system.
Implement template search.
```

**ADV-002**: Add team/organization support (P3) 🔴
```
Create Organization entity.
Allow multiple users per organization.
Implement role-based access control (admin, member, viewer).
Share channels and rules across team.
Team billing and usage tracking.
```

**ADV-003**: Create mobile app (iOS/Android) (P3) 🔴
```
Build React Native app for mobile.
Features:
- View executions
- Manage rules (pause/activate)
- Receive push notifications
- Quick rule testing
Publish to App Store and Play Store.
```

**ADV-004**: Implement AI-powered rule suggestions (P3) 🔴
```
Analyze user's messages to suggest rules.
Use ChatGPT to generate rule markdown from description.
"Smart compose" for rule creation.
Learn from user's rule patterns.
Suggest optimizations for existing rules.
```

**ADV-005**: Add visual workflow builder (P3) 🔴
```
Drag-and-drop rule builder.
Alternative to markdown for non-technical users.
Visual representation of triggers and actions.
Convert to/from markdown.
Preview execution flow.
```

**ADV-006**: Create Chrome extension (P3) 🔴
```
Browser extension for quick rule creation.
Right-click email to create rule.
Quick view of recent executions.
Test rules with current email.
One-click rule activation/deactivation.
```

### Additional Integrations

**INT-001**: Add Discord integration (P3) 🔴
```
OAuth flow for Discord.
Post message to Discord channel action.
Listen to Discord messages as trigger.
Support webhooks and bot API.
```

**INT-002**: Add Notion integration (P3) 🔴
```
OAuth flow for Notion.
Create database item action.
Update page action.
Support Notion blocks and rich text.
```

**INT-003**: Add Airtable integration (P3) 🔴
```
API key authentication.
Create record action.
Update record action.
Support all field types.
Handle attachments.
```

**INT-004**: Add Zapier compatibility (P3) 🔴
```
Create Zapier app integration.
Expose webhooks as Zapier triggers.
Expose actions as Zapier actions.
Publish to Zapier app directory.
Support instant triggers.
```

**INT-005**: Add database connectors (PostgreSQL, MySQL) (P3) 🔴
```
Support direct database connections.
Insert row action.
Update row action.
Execute custom SQL queries.
Handle connection pooling and security.
Support connection string format.
```

---

## Definition of Done

A task is considered "done" when:

1. ✅ Code is written and follows project conventions
2. ✅ Unit tests are written and passing (if applicable)
3. ✅ Integration tests are passing (if applicable)
4. ✅ Code is reviewed (self-review or peer review)
5. ✅ Documentation is updated (code comments, README, user docs)
6. ✅ Code is merged to main branch
7. ✅ Deployed to dev environment
8. ✅ Manually tested in dev environment
9. ✅ No known P0 or P1 bugs introduced
10. ✅ Task status updated in backlog

---

## Using This Backlog

### For Solo Development with Cursor

1. **Start each session** by reviewing the current sprint in `SPRINT-PLAN.md`
2. **Pick the next task** from the sprint tasks
3. **Open this file in Cursor** and navigate to the task
4. **Use Cursor Chat** with prompts like:
   - "Implement INFRA-001 based on the description in BACKLOG.md"
   - "Help me write tests for RULE-002"
   - "Review my implementation of API-001"
5. **Reference other docs**: Tell Cursor to check `PRD.md`, `DATAMODEL.md`, etc.
6. **Update status** when complete (🔴 → 🟡 → 🟢)
7. **Commit frequently** with task ID in commit message: "feat(INFRA-001): initialize repository"

### Cursor Prompts Examples
```
"Help me implement DB-001. Reference DATAMODEL.md for the User table schema."

"I'm working on RULE-002. Show me how to parse markdown rules into an AST."

"Review the code I just wrote for MSG-004. Does it match the requirements?"

"Generate tests for ACT-GS-002 based on the task description."

"I'm stuck on EXEC-001. What's the best approach for invoking rule containers?"
```

### Task Complexity Estimates

- P0 tasks: 2-8 hours each
- P1 tasks: 1-4 hours each
- P2 tasks: 1-6 hours each
- P3 tasks: 2-8 hours each

Actual times will vary based on complexity and unforeseen issues.

---

## Notes

- This backlog is a living document - update it as you learn
- Add discovered tasks as you encounter them
- Adjust priorities based on findings
- Some tasks may be split into smaller tasks
- Some tasks may be combined or removed
- See `SPRINT-PLAN.md` for execution timeline
- Refer to `PRD.md` for product requirements
- Refer to `DATAMODEL.md` for data structures
- Document architectural decisions in `ADR/` directory (create if needed)

---

## Progress Tracking

**Total Tasks**: 280+
**Completed**: 0 🟢
**In Progress**: 0 🟡
**Not Started**: 280+ 🔴
**Blocked**: 0 🔵

**MVP Progress**: 0% (0 of ~150 MVP tasks complete)

Last Updated: 2025-11-14
