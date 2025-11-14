```markdown
# Sprint Plan

## Overview

This document outlines the sprint-by-sprint execution plan for the Message Automation Hub MVP. Each sprint is 2 weeks long with specific goals and deliverables.

**For Cursor IDE Users**: This plan is optimized for AI-assisted development. Use task IDs with Cursor to get implementation help. Reference `BACKLOG.md` for detailed task descriptions.

**Timeline**: 14 weeks (7 sprints) to MVP
**Team Size**: 1 full-time developer
**Sprint Duration**: 2 weeks

---

## How to Use This Plan with Cursor

### Daily Workflow

1. **Morning**: Review current sprint goals and today's tasks
2. **In Cursor**: 
   - Open relevant files from previous session
   - Prompt: "What should I work on next for [SPRINT-X]? Check SPRINT-PLAN.md and BACKLOG.md"
   - Prompt: "Help me implement [TASK-ID] from BACKLOG.md"
3. **During Development**:
   - Use Cursor Composer for multi-file changes
   - Reference PRD.md and DATAMODEL.md in prompts
   - Commit with task ID: `git commit -m "feat(TASK-ID): description"`
4. **End of Day**: 
   - Update task status in BACKLOG.md (🔴 → 🟡 → 🟢)
   - Document blockers or discoveries
   - Push to GitHub

### Cursor Prompts for Sprint Management

```
"Show me remaining tasks for Sprint 1 based on SPRINT-PLAN.md"

"I completed INFRA-001. What's the next logical task to work on?"

"Help me test all Sprint 2 deliverables before moving to Sprint 3"

"Generate a sprint retrospective based on what I've completed this sprint"

"I'm behind on Sprint 3. Which tasks can be deferred to Sprint 4?"
```

---

## Sprint 1: Foundation (Weeks 1-2)

### Goal
Set up infrastructure and project structure with AWS CDK

### Context for Cursor
This sprint focuses on infrastructure-as-code using AWS CDK (TypeScript). You're creating the foundation that all other sprints build on. Reference your ScansafeGuard project for CDK patterns.

### Priority Tasks (Complete in Order)

#### Week 1: Project Setup
1. **INFRA-001** → INFRA-004: Basic project structure
   ```
   Cursor prompt: "Help me set up an Nx monorepo for message-automation-hub 
   with TypeScript. Reference INFRA-001 through INFRA-004 in BACKLOG.md"
   ```

2. **INFRA-005** → INFRA-007: CDK infrastructure
   ```
   Cursor prompt: "Create CDK stacks for message-automation-hub. I want 
   NetworkStack, DataStack, ComputeStack, ApiStack. Reference INFRA-005 
   and my ScansafeGuard project patterns."
   ```

3. **SEC-001** → SEC-004: Security foundation
   ```
   Cursor prompt: "Set up KMS keys, IAM roles, and Cognito for 
   message-automation-hub. Reference SEC-001 through SEC-004."
   ```

#### Week 2: Data Layer
4. **DB-001** → DB-008: DynamoDB tables
   ```
   Cursor prompt: "Create all DynamoDB tables for message-automation-hub 
   using CDK. Reference DATAMODEL.md for schemas and DB-001 through DB-008 
   for requirements."
   ```

5. **S3-001**, **S3-002**, **S3-005**: S3 buckets
   ```
   Cursor prompt: "Create S3 buckets for attachments and projects with 
   encryption. Reference S3-001, S3-002, and S3-005 in BACKLOG.md"
   ```

6. **DOC-002**: Document rule syntax
   ```
   Cursor prompt: "Help me create RULE-SYNTAX.md documenting the markdown 
   syntax for rules. Include trigger operators, action types, and examples."
   ```

### Deliverable
Working AWS infrastructure with all DynamoDB tables, S3 buckets, and security components. Can deploy to dev environment with `cdk deploy`.

### Testing with Cursor
```
Cursor prompt: "Help me write a test script to verify all infrastructure 
components are deployed correctly. Check DynamoDB tables exist, S3 buckets 
are accessible, and Cognito user pool is configured."
```

### Success Criteria
- [ ] Can deploy infrastructure to dev environment via CDK
- [ ] All 7 DynamoDB tables visible in AWS console with correct GSIs
- [ ] S3 buckets created and encrypted
- [ ] Cognito user pool configured
- [ ] CI/CD pipeline runs successfully (INFRA-008)
- [ ] Rule markdown syntax documented in RULE-SYNTAX.md
- [ ] Can create new Nx library/app without errors

### Time Allocation
- Nx monorepo setup: 4 hours
- CDK infrastructure: 16 hours
- DynamoDB tables: 12 hours
- S3 and security: 8 hours
- Documentation: 4 hours
- Testing and debugging: 6 hours
- **Total: 50 hours** (2.5 hours/day buffer)

---

## Sprint 2: Core Backend (Weeks 3-4)

### Goal
Build user management and message ingestion pipeline

### Context for Cursor
You're building serverless Lambda functions with TypeScript. Focus on authentication with Cognito, Gmail OAuth integration, and the message processing pipeline.

### Priority Tasks (Complete in Order)

#### Week 3: API Foundation & Auth
1. **API-001** → API-003: API Gateway setup
   ```
   Cursor prompt: "Create REST API in API Gateway using CDK. Configure 
   CORS and request validation. Reference API-001 through API-003."
   ```

2. **USER-001** → USER-004: User management
   ```
   Cursor prompt: "Implement user registration and login Lambdas with 
   Cognito integration. Reference USER-001 through USER-004 and 
   DATAMODEL.md User entity."
   ```

3. **OPS-LOG-001**: CloudWatch logging
   ```
   Cursor prompt: "Set up CloudWatch Logs for all Lambda functions. 
   Enable structured logging with JSON format."
   ```

#### Week 4: Channel & Message Processing
4. **CHAN-001** → CHAN-003: Gmail integration
   ```
   Cursor prompt: "Implement Gmail OAuth flow and message polling. Create 
   channel CRUD endpoints. Reference CHAN-001 through CHAN-003 and 
   DATAMODEL.md Channel entity."
   ```

5. **MSG-001** → MSG-004: Message processing
   ```
   Cursor prompt: "Build message ingestion Lambda, parser, and rule 
   matching engine. Reference MSG-001 through MSG-004 and DATAMODEL.md 
   Message entity."
   ```

### Deliverable
Users can register, log in via API, connect Gmail account via OAuth, and system polls/stores messages in DynamoDB.

### Testing with Cursor
```
Cursor prompt: "Help me create integration tests for the auth and message 
processing flow. Test: register → login → connect Gmail → poll messages → 
store in DynamoDB. Use Jest and mock AWS services."
```

### Success Criteria
- [ ] Can register new user via POST /auth/register
- [ ] Can log in and receive JWT token
- [ ] Can initiate Gmail OAuth flow and receive callback
- [ ] Gmail polling Lambda runs every 5 minutes
- [ ] Messages from Gmail are parsed and stored in DynamoDB
- [ ] Rule matching engine identifies active rules for messages
- [ ] All Lambda functions log to CloudWatch
- [ ] API Gateway has CORS configured correctly

### API Endpoints Completed
```
POST   /auth/register
POST   /auth/login
GET    /users/me
PUT    /users/me
POST   /channels
GET    /channels
GET    /channels/:id
PUT    /channels/:id
DELETE /channels/:id
GET    /channels/gmail/auth
GET    /channels/gmail/callback
```

### Testing Checklist with Cursor
```
Cursor prompt: "Create a test checklist for Sprint 2. For each endpoint, 
generate curl commands or Postman collections to test manually."
```

### Time Allocation
- API Gateway setup: 6 hours
- User management Lambdas: 12 hours
- Gmail OAuth implementation: 10 hours
- Message polling Lambda: 8 hours
- Message parsing and storage: 10 hours
- Testing and debugging: 8 hours
- **Total: 54 hours** (1 hour/day buffer)

---

## Sprint 3: Rule Engine (Weeks 5-6)

### Goal
Implement rule parsing, code generation, and Docker builds

### Context for Cursor
The most complex sprint. You're building a markdown parser, TypeScript code generator, and Docker build system. Break this into small, testable pieces.

### Priority Tasks (Complete in Order)

#### Week 5: Rule Management & Parsing
1. **RULE-001**: Rule CRUD endpoints
   ```
   Cursor prompt: "Create REST API endpoints for rule CRUD operations. 
   Reference RULE-001 and DATAMODEL.md Rule entity."
   ```

2. **RULE-002**: Markdown parser
   ```
   Cursor prompt: "Build a markdown parser that converts rule syntax into 
   an AST. Reference RULE-SYNTAX.md for syntax specification. Use a parsing 
   library like marked or remark."
   ```

3. **RULE-004**: Rule validation
   ```
   Cursor prompt: "Create validation logic for parsed rules. Check trigger 
   conditions are valid, action types supported, required fields present."
   ```

4. **TEST-001**: Parser tests
   ```
   Cursor prompt: "Write comprehensive tests for the rule parser. Test 
   valid rules, invalid syntax, edge cases. Aim for 100% coverage."
   ```

#### Week 6: Code Generation & Build
5. **GEN-001** → GEN-004: Code generation
   ```
   Cursor prompt: "Create TypeScript code generator from parsed rule AST. 
   Generate index.ts, package.json, and Dockerfile. Reference GEN-001 
   through GEN-004. Create templates first."
   ```

6. **RULE-003**: Connect parser to generator
   ```
   Cursor prompt: "Wire up the rule parser to code generator. When rule 
   is saved, parse markdown and generate code. Store in RuleProject table."
   ```

7. **BUILD-001** → BUILD-004: Docker build system
   ```
   Cursor prompt: "Create Lambda that builds Docker images from generated 
   code. Push to ECR. Track build status. Reference BUILD-001 through 
   BUILD-004."
   ```

8. **TEST-002**: Generator tests
   ```
   Cursor prompt: "Write tests for code generator. Verify generated code 
   compiles, imports are correct, TypeScript types are valid."
   ```

### Deliverable
System can parse markdown rules, generate executable TypeScript code, and build Docker images stored in ECR.

### Testing with Cursor
```
Cursor prompt: "Create end-to-end test for Sprint 3: Create rule via API 
→ verify markdown parsed → verify code generated → verify Docker build 
succeeds → verify image in ECR. Mock Docker build in tests."
```

### Success Criteria
- [ ] Can create rule via POST /rules with markdown
- [ ] Markdown is parsed into AST correctly
- [ ] TypeScript code is generated from AST
- [ ] Generated code compiles without errors
- [ ] Docker image is built successfully
- [ ] Image is pushed to ECR with correct tag
- [ ] Build status tracked in RuleProject table
- [ ] Build failures are logged and surfaced
- [ ] Rule parser has >90% test coverage
- [ ] Code generator has >80% test coverage

### Example Test Rule for Validation
```markdown
# Payment Tracker

## Trigger
When email from: paypal@notification.paypal.com
And subject contains: "You've got money"

## Extract
- amount: regex /\$([0-9,]+\.\d{2})/
- sender: regex /from (.+?) \(/
- date: current_date

## Actions
1. Google Sheets: Append to "Income 2025"
   - Column A: {{date}}
   - Column B: {{sender}}
   - Column C: {{amount}}
```

### Cursor Prompts for Debugging
```
"My rule parser is failing on this markdown: [paste markdown]. Help me debug."

"The generated code has a TypeScript error: [paste error]. Fix the generator."

"Docker build is failing with this error: [paste error]. What's wrong?"
```

### Time Allocation
- Rule CRUD API: 6 hours
- Markdown parser: 16 hours
- Code generator: 16 hours
- Docker build system: 12 hours
- Testing: 10 hours
- Integration and debugging: 10 hours
- **Total: 70 hours** (need to work extra ~5 hours)

---

## Sprint 4: Execution & Actions (Weeks 7-8)

### Goal
Make rules actually execute and perform Google Sheets and webhook actions

### Context for Cursor
You're building the execution engine that runs generated Docker containers and implementing the first action integrations.

### Priority Tasks (Complete in Order)

#### Week 7: Execution Engine
1. **EXEC-001**: Rule execution invoker
   ```
   Cursor prompt: "Create Lambda that invokes rule containers when message 
   matches. Can run as Lambda or ECS Fargate task. Reference EXEC-001."
   ```

2. **EXEC-003** → EXEC-004: Execution context & tracking
   ```
   Cursor prompt: "Build execution context object and tracking system. 
   Create Execution record in DynamoDB. Reference DATAMODEL.md Execution 
   entity."
   ```

3. **EXEC-005**, **EXEC-007**: Retries and logging
   ```
   Cursor prompt: "Implement retry logic for failed executions. Aggregate 
   logs from rule execution. Reference EXEC-005 and EXEC-007."
   ```

#### Week 8: Action Integrations
4. **ACT-GS-001** → ACT-GS-003**: Google Sheets
   ```
   Cursor prompt: "Implement Google Sheets OAuth and actions. Create 
   npm package @message-automation/action-google-sheets. Support append 
   row and update cell. Reference ACT-GS-001 through ACT-GS-003."
   ```

5. **ACT-GS-006**: Rate limiting
   ```
   Cursor prompt: "Add rate limiting and exponential backoff for Google 
   Sheets API calls. Handle quota errors gracefully."
   ```

6. **ACT-WH-001** → ACT-WH-003: Webhooks
   ```
   Cursor prompt: "Create webhook action package. Support POST with custom 
   headers and body templates. Reference ACT-WH-001 through ACT-WH-003."
   ```

7. **TEST-003** → TEST-004: Integration tests
   ```
   Cursor prompt: "Write tests for message matcher and action integrations. 
   Mock Google Sheets and webhook APIs. Reference TEST-003 and TEST-004."
   ```

### Deliverable
Rules can execute when messages arrive, append data to Google Sheets, and send webhooks. Full end-to-end flow working.

### Testing with Cursor
```
Cursor prompt: "Create end-to-end test for Sprint 4: Send test message → 
verify rule matches → verify execution created → verify Google Sheets row 
appended → verify webhook sent. Use test spreadsheet and webhook.site."
```

### Success Criteria
- [ ] Message triggers rule execution automatically
- [ ] Execution record created in DynamoDB with status tracking
- [ ] Can append data to Google Sheets successfully
- [ ] Can send POST request to webhook URL
- [ ] Failed executions are retried up to 3 times
- [ ] Execution logs are stored and retrievable
- [ ] Google Sheets rate limiting works correctly
- [ ] Webhook body templates interpolate variables
- [ ] Action integrations have >80% test coverage

### Manual Testing Checklist
```
1. Create test Gmail account
2. Create test Google Sheets spreadsheet
3. Create rule to track test emails
4. Send test email
5. Verify execution in DynamoDB
6. Verify row appended to spreadsheet
7. Send another test email
8. Verify second execution works
```

### Cursor Prompts for Testing
```
"Help me create a test harness for the execution engine. I want to test 
rule execution without needing real Gmail/Sheets connections."

"Generate mock data for testing Google Sheets actions. Create sample 
OAuth tokens and spreadsheet responses."

"My execution is failing with this error: [paste error]. Help debug."
```

### Time Allocation
- Execution engine: 14 hours
- Execution tracking: 6 hours
- Google Sheets OAuth: 8 hours
- Google Sheets actions: 10 hours
- Webhook actions: 6 hours
- Retry and error handling: 6 hours
- Testing: 10 hours
- Integration and debugging: 10 hours
- **Total: 70 hours** (need to work extra ~5 hours)

---

## Sprint 5: Frontend Foundation (Weeks 9-10)

### Goal
Build core UI for authentication and rule management

### Context for Cursor
Switching to frontend development. Use Next.js 14+ with App Router, Tailwind CSS, and React Query. Focus on clean, responsive UI.

### Priority Tasks (Complete in Order)

#### Week 9: Project Setup & Auth
1. **FE-001** → FE-006: Next.js setup
   ```
   Cursor prompt: "Set up Next.js 14 app with TypeScript, Tailwind CSS, 
   and React Query. Create layout components and auth context. Reference 
   FE-001 through FE-006."
   ```

2. **FE-AUTH-001** → FE-AUTH-002**: Auth pages
   ```
   Cursor prompt: "Build login and registration pages using react-hook-form. 
   Integrate with auth API endpoints. Reference FE-AUTH-001 and FE-AUTH-002."
   ```

3. **FE-AUTH-004**: OAuth callbacks
   ```
   Cursor prompt: "Create OAuth callback routes for Google. Handle token 
   exchange and redirect to dashboard."
   ```

4. **FE-DASH-001** → FE-DASH-002: Dashboard
   ```
   Cursor prompt: "Create dashboard layout with sidebar navigation and 
   rule list view. Fetch rules using React Query."
   ```

#### Week 10: Rule Builder
5. **FE-RULE-001** → FE-RULE-004: Rule editor
   ```
   Cursor prompt: "Build markdown rule editor with Monaco or CodeMirror. 
   Add live preview panel and syntax highlighting. Reference FE-RULE-001 
   through FE-RULE-004 and RULE-SYNTAX.md."
   ```

6. **FE-RULE-006**: Validation display
   ```
   Cursor prompt: "Show validation errors in rule editor. Highlight error 
   lines and display helpful error messages."
   ```

### Deliverable
Users can log in via web UI, see dashboard with rule list, and create/edit rules using markdown editor.

### Testing with Cursor
```
Cursor prompt: "Help me test the frontend manually. Create a test plan 
for: login → dashboard → create rule → edit rule → save rule."
```

### Success Criteria
- [ ] Can access login page at /login
- [ ] Can register new account with form validation
- [ ] Can log in and receive auth token
- [ ] Token stored in cookies/localStorage
- [ ] Redirected to dashboard after login
- [ ] Dashboard shows list of user's rules
- [ ] Can click "New Rule" to open editor
- [ ] Markdown editor has syntax highlighting
- [ ] Live preview shows parsed rule structure
- [ ] Validation errors display clearly
- [ ] Can save rule via "Save" button
- [ ] Rule appears in dashboard list
- [ ] All pages are responsive (mobile/tablet/desktop)

### Component Structure
```
app/
├── (auth)/
│   ├── login/
│   ├── register/
│   └── layout.tsx
├── (dashboard)/
│   ├── dashboard/
│   ├── rules/
│   │   ├── [id]/
│   │   └── new/
│   ├── channels/
│   └── layout.tsx
└── api/
```

### Cursor Prompts for Frontend
```
"Create a reusable Button component with Tailwind CSS. Support variants: 
primary, secondary, danger. Include loading state."

"Build a RuleCard component that displays rule name, status badge, and 
last executed time. Make it clickable to navigate to rule detail."

"Help me set up React Query for API calls. Create hooks: useRules, 
useCreateRule, useUpdateRule, useDeleteRule."

"My Monaco editor isn't syntax highlighting correctly. Help me configure 
it for our custom rule markdown syntax."
```

### Time Allocation
- Next.js project setup: 4 hours
- Auth pages: 8 hours
- Dashboard layout: 6 hours
- Rule list view: 4 hours
- Rule editor (markdown): 12 hours
- Live preview: 8 hours
- Form validation: 6 hours
- API integration with React Query: 6 hours
- Responsive design: 6 hours
- Testing and polish: 10 hours
- **Total: 70 hours** (need to work extra ~5 hours)

---

## Sprint 6: MVP Polish (Weeks 11-12)

### Goal
Complete MVP features and integrate all components

### Context for Cursor
You're filling in missing pieces and polishing the user experience. Focus on channel management UI, message/execution history, and monitoring.

### Priority Tasks (Complete in Order)

#### Week 11: Channels & Integrations
1. **FE-CHAN-001** → FE-CHAN-005: Channel UI
   ```
   Cursor prompt: "Build channel management UI. List channels, add new 
   channel wizard, OAuth flows. Reference FE-CHAN-001 through FE-CHAN-005."
   ```

2. **FE-INT-001** → FE-INT-003: Integrations UI
   ```
   Cursor prompt: "Create integrations page showing connected services 
   (Google, OpenAI). Display status and allow reconnection."
   ```

3. **S3-006**: CORS for uploads
   ```
   Cursor prompt: "Configure CORS on S3 bucket for pre-signed URL uploads 
   from frontend. Test file upload flow."
   ```

#### Week 12: History & Monitoring
4. **FE-MSG-001**, **FE-MSG-002**, **FE-MSG-004**: Message history
   ```
   Cursor prompt: "Build message list view with pagination. Show message 
   details in modal. Reference FE-MSG-001, FE-MSG-002, and FE-MSG-004."
   ```

5. **FE-EXEC-001** → FE-EXEC-004: Execution history
   ```
   Cursor prompt: "Create execution list and detail views. Show logs and 
   action results. Reference FE-EXEC-001 through FE-EXEC-004."
   ```

6. **OPS-LOG-002**, **OPS-MET-001** → OPS-MET-003**, **OPS-MET-006: Monitoring
   ```
   Cursor prompt: "Set up structured logging, CloudWatch dashboards, and 
   alarms for failed executions and API errors. Configure SNS notifications."
   ```

### Deliverable
Complete MVP with full user journey: signup → connect channel → create rule → view execution.

### Testing with Cursor
```
Cursor prompt: "Create a comprehensive test plan for the complete user 
journey. Start from registration and end with viewing execution results. 
Include screenshots at each step."
```

### Success Criteria
- [ ] Can complete full user journey without errors
- [ ] Channel list shows all connected channels with status
- [ ] Can add new Gmail channel via OAuth
- [ ] Channel connection status updates correctly
- [ ] Message list shows recent messages with pagination
- [ ] Can view message details including attachments
- [ ] Execution list shows all executions with status
- [ ] Can view execution details, logs, and action results
- [ ] Integration list shows all connected services
- [ ] CloudWatch dashboard shows key metrics
- [ ] Alarms configured for errors and failures
- [ ] SNS notifications work for alarms
- [ ] All UI components are responsive

### End-to-End Test Flow
```
1. Register new user account
2. Verify email (dev: auto-verify)
3. Log in to dashboard
4. Connect Gmail channel via OAuth
5. Create rule to track specific emails
6. Send test email to Gmail
7. Wait for polling (or trigger manually)
8. View message in message history
9. View execution in execution history
10. Verify Google Sheets has new row
11. Check CloudWatch logs for execution
```

### Cursor Prompts for Integration
```
"Help me debug the OAuth flow. User clicks 'Connect Gmail' but callback 
isn't working. Show me the full flow and where it might be failing."

"Create a migration script to add sample data for testing: 3 channels, 
5 rules, 20 messages, 30 executions."

"Generate CloudWatch Insights queries for common debugging scenarios: 
errors by Lambda, slow requests, failed executions by rule."
```

### Time Allocation
- Channel management UI: 12 hours
- Integrations UI: 6 hours
- Message history: 10 hours
- Execution history: 10 hours
- CloudWatch monitoring setup: 8 hours
- End-to-end testing: 8 hours
- Bug fixes: 10 hours
- Polish and refinement: 6 hours
- **Total: 70 hours** (need to work extra ~5 hours)

---

## Sprint 7: Testing & Launch Prep (Weeks 13-14)

### Goal
Test thoroughly, fix bugs, write documentation, and prepare for launch

### Context for Cursor
Final sprint before MVP launch. Focus on quality, stability, and documentation. No new features.

### Priority Tasks (Complete in Order)

#### Week 13: Testing & Bug Fixes
1. **TEST-INT-001** → TEST-INT-004: Integration tests
   ```
   Cursor prompt: "Create comprehensive integration tests for: OAuth flows, 
   message processing pipeline, rule execution. Use Playwright or Cypress 
   for E2E. Reference TEST-INT-001 through TEST-INT-004."
   ```

2. **BUG-001** → BUG-003: Critical bug fixes
   ```
   Cursor prompt: "Fix these critical bugs: timezone handling, large 
   message bodies, race conditions in rule execution. Reference BUG-001 
   through BUG-003."
   ```

3. **PERF-001**: Query optimization
   ```
   Cursor prompt: "Review all DynamoDB queries. Optimize slow queries, 
   add missing indexes if needed. Measure before and after latency."
   ```

4. **TEST-005**: Code coverage
   ```
   Cursor prompt: "Run test coverage report. Identify uncovered code paths. 
   Write tests to achieve >80% backend coverage."
   ```

#### Week 14: Documentation & Polish
5. **DOC-001**: API documentation
   ```
   Cursor prompt: "Generate OpenAPI 3.0 specification for all API endpoints. 
   Include request/response schemas and examples. Set up Swagger UI."
   ```

6. **DOC-003** → DOC-006: Technical docs
   ```
   Cursor prompt: "Create architecture diagrams and write deployment guide, 
   developer setup guide. Reference DOC-003 through DOC-006."
   ```

7. **DOC-USER-001** → DOC-USER-004: User docs
   ```
   Cursor prompt: "Write user documentation: getting started guide, rule 
   examples library, OAuth setup guides, troubleshooting. Reference 
   DOC-USER-001 through DOC-USER-004."
   ```

8. **UX-001**, **UX-003**, **UX-004**: UX polish
   ```
   Cursor prompt: "Add loading states to all async operations. Create empty 
   states with helpful CTAs. Build onboarding flow for new users."
   ```

### Deliverable
Production-ready MVP with comprehensive documentation, tests, and all critical bugs fixed.

### Testing with Cursor
```
Cursor prompt: "Create a pre-launch checklist covering: all features work, 
tests pass, documentation complete, security review done, performance 
acceptable. Generate detailed checklist with verification steps."
```

### Success Criteria
- [ ] All integration tests pass
- [ ] Backend code coverage >80%
- [ ] Frontend component tests cover critical flows
- [ ] No known P0 or P1 bugs
- [ ] All timezone issues resolved
- [ ] Large messages handled correctly
- [ ] No race conditions in concurrent executions
- [ ] API documentation complete (Swagger UI accessible)
- [ ] Architecture diagrams created
- [ ] Deployment guide tested on fresh AWS account
- [ ] Getting started guide verified with new user
- [ ] All OAuth flows documented with screenshots
- [ ] Loading states on all pages
- [ ] Empty states with helpful CTAs
- [ ] Onboarding flow guides new users
- [ ] Performance metrics acceptable (p95 < 2s)

### Pre-Launch Checklist

#### Functionality
- [ ] User registration and login works
- [ ] Email verification works (or auto-verified in dev)
- [ ] Password reset flow works
- [ ] Gmail OAuth connection works
- [ ] Gmail message polling works
- [ ] Rule creation and editing works
- [ ] Rule validation works
- [ ] Rule execution works
- [ ] Google Sheets action works
- [ ] Webhook action works
- [ ] Execution retry works
- [ ] Error notifications work

#### Security
- [ ] All credentials encrypted at rest
- [ ] All API calls use HTTPS
- [ ] JWT tokens expire appropriately
- [ ] No secrets in code or logs
- [ ] IAM roles follow least privilege
- [ ] CORS configured correctly
- [ ] Input validation on all endpoints
- [ ] Rate limiting configured

#### Performance
- [ ] Page load times <2s
- [ ] API response times <500ms (p95)
- [ ] Lambda cold starts <3s
- [ ] Message polling completes in <30s
- [ ] Rule execution completes in <1m
- [ ] No DynamoDB throttling under normal load

#### Monitoring
- [ ] CloudWatch Logs configured
- [ ] CloudWatch dashboards created
- [ ] Alarms set for critical errors
- [ ] SNS notifications configured
- [ ] Log retention policies set

#### Documentation
- [ ] README.md complete
- [ ] API documentation (Swagger) accessible
- [ ] Architecture diagrams created
- [ ] Deployment guide tested
- [ ] User getting started guide complete
- [ ] Rule examples library has 10+ examples
- [ ] Troubleshooting guide written

#### Testing
- [ ] Unit tests pass (>80% coverage)
- [ ] Integration tests pass
- [ ] E2E tests pass
- [ ] Manual testing complete
- [ ] Tested on multiple browsers
- [ ] Tested on mobile devices

### Cursor Prompts for Final Testing
```
"Run through the entire user journey and document any issues. Create a 
test report with screenshots."

"Generate a performance test report. Measure: page load times, API latency, 
Lambda duration, DynamoDB query times."

"Review all error handling. Are error messages helpful? Do we log enough 
context for debugging?"

"Check all frontend forms for validation. Can users submit invalid data?"

"Audit all API endpoints for proper authentication and authorization."
```

### Launch Day Checklist
```
1. Deploy to production environment
2. Run smoke tests in production
3. Monitor CloudWatch logs for errors
4. Monitor CloudWatch metrics for anomalies
5. Test user registration flow
6. Test OAuth flows
7. Test rule creation and execution
8. Have rollback plan ready
9. Monitor for first hour
10. Announce to alpha testers
```

### Time Allocation
- Integration tests: 12 hours
- Bug fixes: 12 hours
- Performance optimization: 6 hours
- Unit test coverage: 6 hours
- API documentation: 4 hours
- Technical documentation: 8 hours
- User documentation: 10 hours
- UX polish: 6 hours
- Pre-launch testing: 8 hours
- **Total: 72 hours** (need to work extra ~7 hours)

---

## Post-MVP Sprints

After successful MVP launch and alpha testing, continue with these focus areas:

### Sprint 8: User Feedback & Iteration (Weeks 15-16)

**Goal**: Address feedback from alpha users and fix production issues

**Tasks**:
- Monitor production metrics and logs
- Collect user feedback (surveys, interviews, support tickets)
- Fix bugs discovered in production
- Add most-requested features from feedback
- Improve onboarding based on user confusion points
- Optimize performance bottlenecks
- Improve error messages based on user questions

**Cursor Prompts**:
```
"Analyze these user feedback items and prioritize fixes: [paste feedback]"

"User is confused about [feature]. How can we improve the UI/documentation?"

"Production error rate increased. Help me investigate CloudWatch logs."
```

### Sprint 9: Additional Integrations (Weeks 17-18)

**Goal**: Add ChatGPT, Slack, and SMS integrations

**Tasks**:
- ACT-AI-001 through ACT-AI-005: ChatGPT integration
- ACT-SL-001 through ACT-SL-004: Slack integration
- ACT-EM-001 through ACT-EM-004: Email actions
- CHAN-005 through CHAN-006: SMS integration (Twilio)

**Cursor Prompts**:
```
"Implement OpenAI integration for rule actions. Reference ACT-AI-001 
through ACT-AI-005."

"Build Slack OAuth flow and post message action. Reference ACT-SL-001 
through ACT-SL-004."
```

### Sprint 10: Scale & Advanced Features (Weeks 19-20)

**Goal**: Prepare for scale and add power user features

**Tasks**:
- RULE-006: Rule versioning
- RULE-007: Rule import/export
- TEST-LOAD-001 through TEST-LOAD-003: Load testing
- PERF-002: Implement caching layer (Redis)
- OPS-DATA-001 through OPS-DATA-004: Data management tools
- ADV-002: Team/organization support (if needed)

**Cursor Prompts**:
```
"Help me implement rule versioning. Users should be able to rollback to 
previous versions."

"Set up Redis caching for frequently accessed data: user profiles, active 
rules, integration credentials."

"Create load tests using k6. Test scenarios: 100 concurrent users, 1000 
messages/minute, 100 concurrent executions."
```

---

## Sprint Rituals for Solo Development

### Sprint Planning (Start of Sprint)
**Time**: 1 hour at start of each sprint

**Cursor Prompt**:
```
"Help me plan Sprint [N]. Review tasks in SPRINT-PLAN.md and BACKLOG.md. 
Estimate how long each task will take. Identify dependencies and risks."
```

**Activities**:
1. Review sprint goals and deliverables
2. Review all tasks for the sprint
3. Estimate effort for each task (2, 4, 8, 16 hours)
4. Identify blockers and dependencies
5. Adjust scope if needed
6. Set up tracking (GitHub Project board)

### Daily Standup (Daily - 15 minutes)
**Time**: Every morning

**Journal Format** (write in STANDUP.md):
```markdown
## [Date]

### Yesterday
- [x] Completed INFRA-001
- [x] Completed INFRA-002
- [ ] Started INFRA-003 (blocked)

### Today
- [ ] Finish INFRA-003
- [ ] Start INFRA-004
- [ ] Deploy to dev and test

### Blockers
- Waiting for AWS account approval for cross-account deploy
- Need to research best Nx plugin for CDK

### Notes
- INFRA-003 taking longer than expected due to TypeScript config issues
- Should allocate more time for CDK stack interdependencies
```

**Cursor Prompt for Planning**:
```
"Review my standup from yesterday. What should I focus on today? Check 
SPRINT-PLAN.md for Sprint [N] priorities."
```

### Mid-Sprint Check-in (End of Week 1)
**Time**: 30 minutes

**Cursor Prompt**:
```
"Review my progress on Sprint [N]. I've completed [X] tasks. Am I on track 
to finish the sprint? What tasks should I prioritize this week?"
```

**Activities**:
1. Count completed vs remaining tasks
2. Calculate if on track (should be ~50% done)
3. Identify tasks that are behind
4. Adjust scope if significantly behind
5. Document learnings and blockers

### Sprint Review (End of Sprint)
**Time**: 1 hour

**Cursor Prompt**:
```
"Help me create a sprint review for Sprint [N]. What did I accomplish? 
Did I meet the sprint goal? Generate a summary with screenshots if possible."
```

**Activities**:
1. Demo all completed features (record video)
2. Review sprint goal achievement
3. Document what shipped vs what didn't
4. Celebrate wins
5. Create sprint summary document

**Template for Sprint Review**:
```markdown
# Sprint [N] Review

## Goal
[Sprint goal from SPRINT-PLAN.md]

## Completed
- [x] TASK-001: Description
- [x] TASK-002: Description
...

## Not Completed (moved to next sprint)
- [ ] TASK-015: Description (reason: ...)

## Deliverable Status
[✓/✗] [Deliverable description]

## Demos
[Links to screenshots/videos]

## Metrics
- Tasks completed: X/Y
- Code coverage: Z%
- Deployment: Success/Failed

## Overall Assessment
[Met/Partially Met/Did Not Meet] sprint goal.
```

### Sprint Retrospective (End of Sprint)
**Time**: 30 minutes

**Cursor Prompt**:
```
"Help me run a sprint retrospective for Sprint [N]. Review my standup 
notes and sprint progress. What went well? What could be improved?"
```

**Retro Format** (write in RETRO.md):
```markdown
# Sprint [N] Retrospective

## What Went Well
- CDK patterns from ScansafeGuard were very helpful
- Cursor helped unblock TypeScript config issues quickly
- Good progress on DynamoDB table design

## What Could Be Improved
- Underestimated time for OAuth implementation
- Should have written tests earlier, not at end
- Need better local testing setup before deploying

## Action Items for Next Sprint
- [ ] Set up local DynamoDB for faster testing
- [ ] Create test data generation script
- [ ] Research OAuth libraries before implementing from scratch

## Learnings
- GSI projections need to be set correctly or queries fail
- Lambda cold starts are worse than expected, need to optimize
- React Query makes API integration much easier

## Time Tracking
- Planned: 50 hours
- Actual: 55 hours
- Variance: +5 hours (10%)
```

---

## Progress Tracking

### Weekly Progress Update

Update this table each Friday:

| Sprint | Week | Tasks Complete | Tasks Remaining | On Track? | Notes |
|--------|------|----------------|-----------------|-----------|-------|
| 1 | 1 | 0/13 | 13 | Yes | Just started |
| 1 | 2 | 13/13 | 0 | Yes | Completed sprint 1 |
| 2 | 3 | 6/11 | 5 | Yes | Good progress |
| 2 | 4 | 11/11 | 0 | Yes | Sprint complete |
| ... | ... | ... | ... | ... | ... |

### Overall MVP Progress

Update monthly:

```
Sprint 1 (Foundation):           █████████░ 90% (12/13 complete)
Sprint 2 (Core Backend):         ████░░░░░░ 40% (5/11 complete)
Sprint 3 (Rule Engine):          ░░░░░░░░░░  0% (0/13 complete)
Sprint 4 (Execution & Actions):  ░░░░░░░░░░  0% (0/12 complete)
Sprint 5 (Frontend Foundation):  ░░░░░░░░░░  0% (0/11 complete)
Sprint 6 (MVP Polish):           ░░░░░░░░░░  0% (0/15 complete)
Sprint 7 (Testing & Launch):     ░░░░░░░░░░  0% (0/12 complete)

Overall MVP Progress: 18% (17/87 tasks complete)
```

### Velocity Tracking

Track your average tasks per week to improve estimates:

| Sprint | Planned Tasks | Completed Tasks | Velocity |
|--------|---------------|-----------------|----------|
| 1 | 13 | 13 | 100% |
| 2 | 11 | 10 | 91% |
| 3 | 13 | 11 | 85% |
| ... | ... | ... | ... |

**Average Velocity**: Calculate after 3-4 sprints to predict future capacity

---

## Risk Management & Mitigation

### Technical Risks

| Risk | Probability | Impact | Mitigation | Sprint |
|------|-------------|--------|------------|--------|
| OAuth integration more complex than expected | High | Medium | Allocate 2x time estimate, use library | Sprint 2 |
| Code generation produces invalid code | Medium | High | Extensive testing, start simple | Sprint 3 |
| Docker builds are slow (>5 min) | Medium | Medium | Use caching, consider alternatives | Sprint 3 |
| Lambda cold starts exceed 5s | Low | Medium | Optimize bundle size, keep warm | Sprint 4 |
| DynamoDB queries are slow | Low | High | Use GSIs properly, load test early | Sprint 6 |
| Generated code has security vulnerabilities | Low | Critical | Security review, input validation | Sprint 7 |

**Cursor Prompt for Risk Assessment**:
```
"I'm concerned about [risk]. What are the best practices for mitigating 
this in AWS serverless applications?"
```

### Schedule Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Feature creep | High | High | Strict MVP scope, defer all non-P0 tasks |
| Underestimated complexity | High | Medium | 20% time buffer, track actuals vs estimates |
| Blocked by AWS service issues | Low | Medium | Have backup plan, test early |
| Burnout from working extra hours | Medium | High | Take breaks, don't work weekends |
| Discovered blocker late in sprint | Medium | Medium | Identify dependencies in planning |

**Action**: Review risks at sprint planning and mid-sprint check-in

---

## Tools & Resources

### Development Tools
- **IDE**: Cursor with Claude integration
- **Version Control**: GitHub (private repo)
- **CI/CD**: GitHub Actions
- **AWS**: CDK for IaC, AWS Console for monitoring
- **API Testing**: Postman or Thunder Client (VS Code)
- **Database**: NoSQL Workbench for DynamoDB modeling

### Cursor Best Practices

1. **Context Management**:
   ```
   Always reference relevant docs in prompts:
   "Reference DATAMODEL.md and BACKLOG.md task USER-001"
   ```

2. **Iterative Development**:
   ```
   "Generate the basic structure for [component]"
   "Now add error handling"
   "Now add tests"
   ```

3. **Code Review**:
   ```
   "Review this code for: security issues, performance problems, 
   edge cases I might have missed"
   ```

4. **Debugging**:
   ```
   "This code is failing with error: [paste error]. Here's the relevant 
   code: [paste code]. Help me debug."
   ```

5. **Learning**:
   ```
   "Explain how this CDK construct works: [paste construct]"
   "What are best practices for [technology/pattern]?"
   ```

### Productivity Tips

1. **Time Blocking**: 
   - Morning (9am-12pm): Deep work on complex tasks
   - Afternoon (1pm-4pm): Meetings, integration, testing
   - Evening (4pm-6pm): Documentation, light tasks

2. **Pomodoro Technique**:
   - 25 minutes focused work
   - 5 minute break
   - Every 4 pomodoros, take 15-30 minute break

3. **Weekly Planning**:
   - Sunday evening: Review next week's sprint tasks
   - Friday afternoon: Review week's progress, update tracking

4. **Context Switching**:
   - Minimize switching between frontend/backend in same day
   - Group similar tasks together
   - Document context before switching

---

## Communication & Documentation

### Code Comments
Write comments for Cursor to understand context:
```typescript
/**
 * Parses rule markdown syntax into an AST.
 * 
 * See RULE-SYNTAX.md for the complete syntax specification.
 * 
 * @example
 * const ast = parseRule(`
 *   # Payment Tracker
 *   ## Trigger
 *   When email from: paypal@notification.paypal.com
 * `);
 * 
 * @throws {RuleParseError} If markdown syntax is invalid
 */
export function parseRule(markdown: string): RuleAST {
  // Implementation
}
```

### Commit Messages
Use conventional commits with task IDs:
```
feat(INFRA-001): initialize nx monorepo with typescript config

- Set up nx workspace
- Configure typescript with strict mode
- Add eslint and prettier
- Configure path aliases

Closes #1
```

### Branch Strategy
```
main (production)
├── develop (integration)
    ├── sprint-1-foundation
    ├── sprint-2-backend
    ├── feat/USER-001-registration
    └── fix/BUG-003-race-condition
```

### Pull Request Template
```markdown
## Description
[Brief description of changes]

## Related Tasks
- Closes #[task-id]
- References BACKLOG.md [task-id]

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing complete

## Checklist
- [ ] Code follows project conventions
- [ ] Comments added where needed
- [ ] Documentation updated
- [ ] No console.logs or debug code
```

---

## Launch Criteria

The MVP is ready to launch when ALL of these criteria are met:

### Functionality (Must Have)
- [x] Users can register and log in
- [x] Users can connect Gmail via OAuth
- [x] System polls Gmail for new messages
- [x] Users can create rules using markdown
- [x] Rules parse and validate correctly
- [x] Rules generate executable code
- [x] Docker images build successfully
- [x] Rules execute when messages match
- [x] Google Sheets actions work correctly
- [x] Webhook actions work correctly
- [x] Users can view execution history
- [x] Users can view message history

### Quality (Must Have)
- [x] No known P0 bugs
- [x] Backend code coverage >80%
- [x] All integration tests pass
- [x] Performance meets targets (p95 < 2s)
- [x] Security review complete
- [x] No secrets in code or logs

### Documentation (Must Have)
- [x] User getting started guide complete
- [x] API documentation available (Swagger)
- [x] Deployment guide tested
- [x] Rule syntax documented with examples
- [x] Troubleshooting guide written

### Operations (Must Have)
- [x] Monitoring dashboards configured
- [x] Alarms set for critical errors
- [x] Logs properly structured
- [x] Backup and recovery tested
- [x] Rollback procedure documented

### User Experience (Should Have)
- [x] Onboarding flow guides new users
- [x] Loading states on all async operations
- [x] Empty states with helpful CTAs
- [x] Error messages are clear and helpful
- [x] Mobile responsive

### Nice to Have (Can Defer)
- [ ] Video tutorials
- [ ] Rule templates library (>10 templates)
- [ ] Real-time updates (websockets)
- [ ] Advanced analytics dashboard

---

## Success Metrics

Track these metrics post-launch:

### User Metrics
- **Weekly Active Users (WAU)**: Target 10 in first month
- **User Retention**: Target >50% week-over-week
- **Time to First Rule**: Target <10 minutes
- **Rules per User**: Target 3+ active rules

### Technical Metrics
- **System Uptime**: Target 99.9%
- **API Response Time (p95)**: Target <500ms
- **Rule Execution Success Rate**: Target >95%
- **Build Success Rate**: Target >98%

### Business Metrics
- **User Satisfaction (NPS)**: Target >40
- **Support Tickets per User**: Target <0.5/month
- **Feature Request Rate**: Track for roadmap
- **Cost per User**: Target <$1/month

---

## Final Notes

- This plan is a living document - adjust as you learn
- Don't be afraid to defer P2/P3 tasks if behind
- Quality over speed - a working MVP beats incomplete features
- Take breaks - sustainable pace is important
- Document learnings for future reference
- Celebrate milestones along the way

**Remember**: The goal is a working MVP, not perfection. Ship early, get feedback, iterate.

Good luck! 🚀

---

Last Updated: 2025-11-14
Next Review: End of Sprint 1
```

This sprint plan is now fully optimized for Cursor IDE with:
- Specific Cursor prompts for each sprint and task
- Context for AI-assisted development
- Detailed testing strategies
- Progress tracking templates
- Daily workflow guidance
- Risk management
- Launch criteria

Save this as `SPRINT-PLAN.md` in your repository!
