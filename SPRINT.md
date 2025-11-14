# Sprint Plan

## Overview

This document outlines the sprint-by-sprint execution plan for the Message Automation Hub MVP. Each sprint is 2 weeks long with specific goals and deliverables.

**Timeline**: 14 weeks (7 sprints) to MVP
**Team Size**: 1 full-time developer
**Sprint Duration**: 2 weeks

---

## Sprint 1: Foundation (Weeks 1-2)

### Goal
Set up infrastructure and project structure

### Tasks
- INFRA-001: Initialize Git repository with proper .gitignore
- INFRA-002: Set up Nx monorepo structure
- INFRA-003: Configure TypeScript for all packages
- INFRA-004: Set up ESLint and Prettier
- INFRA-005: Create CDK app structure for infrastructure
- INFRA-006: Configure multiple environments (dev, staging, prod)
- INFRA-007: Set up AWS account structure (deployer + target accounts)
- INFRA-008: Configure GitHub Actions for CI/CD
- DB-001: Create Users table with CDK
- DB-002: Create Rules table with GSIs
- DB-003: Create Channels table with GSIs
- DB-004: Create Messages table with GSIs and TTL
- DB-005: Create Executions table with GSIs and TTL
- DB-006: Create RuleProjects table
- DB-007: Create Integrations table
- DB-008: Configure DynamoDB Streams on all tables
- S3-001: Create S3 bucket for message attachments
- S3-002: Create S3 bucket for rule projects
- S3-005: Set up bucket encryption
- SEC-001: Set up KMS keys for encryption
- SEC-002: Create IAM roles for Lambda functions
- SEC-003: Configure Secrets Manager for API keys
- SEC-004: Set up AWS Cognito user pool
- DOC-002: Document rule markdown syntax

### Deliverable
Working AWS infrastructure with all DynamoDB tables, S3 buckets, and security components in place. Project structure ready for development.

### Success Criteria
- [ ] Can deploy infrastructure to dev environment via CDK
- [ ] All tables visible in DynamoDB console
- [ ] S3 buckets created and encrypted
- [ ] CI/CD pipeline runs successfully
- [ ] Rule markdown syntax documented

---

## Sprint 2: Core Backend (Weeks 3-4)

### Goal
Build user management and API foundation

### Tasks
- API-001: Create API Gateway REST API
- API-002: Set up request validation
- API-003: Configure CORS
- USER-001: Create user registration Lambda
- USER-002: Create user login/authentication Lambda
- USER-003: Implement JWT token generation
- USER-004: Create user profile CRUD endpoints
- CHAN-001: Create channel CRUD API endpoints
- CHAN-002: Implement Gmail OAuth flow
- CHAN-003: Create Gmail message polling Lambda
- MSG-001: Create message ingestion Lambda
- MSG-002: Implement message parsing (email/SMS)
- MSG-003: Create message storage logic (DynamoDB + S3)
- MSG-004: Build rule matching engine
- OPS-LOG-001: Set up CloudWatch Logs for all Lambdas

### Deliverable
Users can register, log in, connect Gmail account, and system can poll and store messages.

### Success Criteria
- [ ] Can register new user via API
- [ ] Can log in and receive JWT token
- [ ] Can connect Gmail account via OAuth
- [ ] Messages from Gmail are polled and stored
- [ ] All Lambda functions have CloudWatch logging

---

## Sprint 3: Rule Engine (Weeks 5-6)

### Goal
Implement rule parsing and code generation

### Tasks
- RULE-001: Create rule CRUD API endpoints
- RULE-002: Implement markdown parser for rules
- RULE-003: Build code generator from parsed markdown
- RULE-004: Create rule validation logic
- RULE-005: Implement rule testing/dry-run mode
- GEN-001: Create Node.js project template
- GEN-002: Build package.json generator
- GEN-003: Create Dockerfile generator
- GEN-004: Implement TypeScript code generator
- GEN-005: Add error handling code generation
- GEN-006: Generate logging code
- BUILD-001: Create Docker build Lambda
- BUILD-002: Set up ECR repository for rule images
- BUILD-003: Implement build status tracking
- BUILD-004: Add build failure notifications
- TEST-001: Write tests for rule parser
- TEST-002: Write tests for code generator

### Deliverable
System can parse markdown rules, generate executable TypeScript code, and build Docker images.

### Success Criteria
- [ ] Can create rule via API with markdown definition
- [ ] Markdown is parsed correctly into AST
- [ ] TypeScript code is generated from markdown
- [ ] Docker image is built and pushed to ECR
- [ ] Build status is tracked in DynamoDB
- [ ] Rule parser has test coverage >80%

---

## Sprint 4: Execution & Actions (Weeks 7-8)

### Goal
Make rules actually execute and perform actions

### Tasks
- EXEC-001: Create rule execution Lambda invoker
- EXEC-003: Build execution context (message + extracted data)
- EXEC-004: Create execution tracking
- EXEC-005: Implement retry logic for failed executions
- EXEC-007: Create execution logs aggregation
- ACT-GS-001: Implement Google OAuth for Sheets
- ACT-GS-002: Create append row action
- ACT-GS-003: Create update cell action
- ACT-GS-006: Handle rate limiting
- ACT-WH-001: Create HTTP POST action
- ACT-WH-002: Add custom headers support
- ACT-WH-003: Implement body template engine
- TEST-003: Write tests for message matcher
- TEST-004: Write tests for all action integrations

### Deliverable
Rules can execute when messages arrive and perform Google Sheets and webhook actions successfully.

### Success Criteria
- [ ] Message triggers rule execution
- [ ] Rule execution creates execution record
- [ ] Can append data to Google Sheets
- [ ] Can send webhook POST requests
- [ ] Failed executions are retried
- [ ] Execution logs are stored
- [ ] Action integrations have test coverage >80%

---

## Sprint 5: Frontend Foundation (Weeks 9-10)

### Goal
Build core UI for rule management

### Tasks
- FE-001: Create Next.js application
- FE-002: Set up Tailwind CSS
- FE-003: Configure React Query for API calls
- FE-004: Set up React Router
- FE-005: Implement authentication context
- FE-006: Create layout components
- FE-AUTH-001: Build login page
- FE-AUTH-002: Build registration page
- FE-AUTH-004: Implement OAuth callback handlers
- FE-DASH-001: Create main dashboard layout
- FE-DASH-002: Build rule list view
- FE-RULE-001: Create markdown editor component
- FE-RULE-002: Build live preview panel
- FE-RULE-003: Implement syntax highlighting
- FE-RULE-004: Add autocomplete for actions
- FE-RULE-006: Build validation error display

### Deliverable
Users can log in via web UI and create/edit rules using markdown editor.

### Success Criteria
- [ ] Can log in via web interface
- [ ] Can register new account via web interface
- [ ] Dashboard shows list of rules
- [ ] Can create new rule with markdown editor
- [ ] Markdown editor has syntax highlighting
- [ ] Validation errors display in UI
- [ ] OAuth callback redirects work correctly

---

## Sprint 6: MVP Polish (Weeks 11-12)

### Goal
Complete MVP features and integrate all components

### Tasks
- FE-CHAN-001: Create channel list view
- FE-CHAN-002: Build add channel wizard
- FE-CHAN-003: Implement OAuth connection flows
- FE-CHAN-004: Create channel settings page
- FE-CHAN-005: Display channel status indicators
- FE-MSG-001: Create message list view
- FE-MSG-002: Build message detail modal
- FE-MSG-004: Add pagination
- FE-EXEC-001: Create execution list view
- FE-EXEC-002: Build execution detail view
- FE-EXEC-003: Display execution logs
- FE-EXEC-004: Show action results
- FE-INT-001: Create integrations list page
- FE-INT-002: Build OAuth connection flows
- FE-INT-003: Display integration status
- OPS-LOG-002: Implement structured logging
- OPS-MET-001: Create CloudWatch dashboards
- OPS-MET-002: Set up alarms for failed executions
- OPS-MET-003: Add alarms for API errors
- OPS-MET-006: Set up SNS notifications for alarms

### Deliverable
Fully functional MVP with complete user journey from signup to rule execution.

### Success Criteria
- [ ] Can complete full user journey: signup → connect channel → create rule → see execution
- [ ] All integrations accessible via UI
- [ ] Can view message and execution history
- [ ] CloudWatch dashboards show key metrics
- [ ] Alarms trigger on errors
- [ ] All core features working end-to-end

---

## Sprint 7: Testing & Launch Prep (Weeks 13-14)

### Goal
Test thoroughly, fix bugs, and prepare for launch

### Tasks
- TEST-INT-001: Create end-to-end test suite
- TEST-INT-002: Test OAuth flows
- TEST-INT-003: Test message processing pipeline
- TEST-INT-004: Test rule execution flow
- DOC-001: Write API documentation (OpenAPI/Swagger)
- DOC-003: Create architecture diagrams
- DOC-004: Write deployment guide
- DOC-005: Document all action types
- DOC-006: Create developer setup guide
- DOC-USER-001: Write getting started guide
- DOC-USER-002: Create rule examples library
- DOC-USER-003: Document OAuth setup for each provider
- DOC-USER-004: Write troubleshooting guide
- UX-001: Add loading states everywhere
- UX-003: Add empty states with helpful CTAs
- UX-004: Create onboarding flow
- BUG-001: Fix timezone handling
- BUG-002: Handle large message bodies
- BUG-003: Fix race conditions in rule execution
- PERF-001: Optimize DynamoDB queries

### Deliverable
Production-ready MVP with comprehensive documentation and all critical bugs fixed.

### Success Criteria
- [ ] End-to-end tests cover happy path
- [ ] All OAuth flows tested
- [ ] No known P0 or P1 bugs
- [ ] API documentation complete
- [ ] User documentation complete
- [ ] Getting started guide tested with new user
- [ ] Onboarding flow guides users effectively
- [ ] All pages have loading states
- [ ] Performance is acceptable (<2s page loads)

---

## Post-MVP Sprints

After the initial MVP launch, subsequent sprints will focus on:

### Sprint 8+: User Feedback & Iteration
- Address user feedback from alpha testing
- Fix bugs discovered in production
- Add most-requested features
- Improve performance based on real usage data

### Sprint 9+: Additional Integrations
- ACT-AI-001 through ACT-AI-005: ChatGPT integration
- ACT-SL-001 through ACT-SL-004: Slack integration
- ACT-EM-001 through ACT-EM-004: Email actions
- CHAN-006: Twilio SMS integration

### Sprint 10+: Advanced Features
- Rule templates marketplace
- Team/organization support
- Advanced monitoring and analytics
- Mobile app development

---

## Sprint Rituals

### Sprint Planning (Start of each sprint)
- Review and prioritize backlog
- Select tasks for sprint
- Break down tasks if needed
- Set sprint goal

### Daily Standup (Daily - solo journaling)
- What did I complete yesterday?
- What will I work on today?
- Any blockers or concerns?

### Sprint Review (End of each sprint)
- Demo completed features
- Review sprint goal achievement
- Document lessons learned

### Sprint Retrospective (End of each sprint)
- What went well?
- What could be improved?
- Action items for next sprint

---

## Risk Management

### Technical Risks
| Risk | Mitigation | Sprint |
|------|-----------|--------|
| OAuth integration complexity | Allocate extra time, use well-tested libraries | Sprint 2 |
| Code generation edge cases | Extensive testing, start with simple cases | Sprint 3 |
| Docker build performance | Use caching, consider alternatives | Sprint 3 |
| Lambda cold starts | Keep functions warm, optimize bundle size | Sprint 4 |
| DynamoDB query performance | Use GSIs effectively, test with realistic data | Sprint 6 |

### Schedule Risks
| Risk | Mitigation | Action |
|------|-----------|--------|
| Feature creep | Strict MVP scope, defer non-critical features | Weekly scope review |
| Underestimated tasks | Add 20% buffer to estimates | Sprint planning |
| Blocked tasks | Identify dependencies early, have backup tasks | Daily check-ins |

---

## Definition of Sprint Success

A sprint is successful when:
1. Sprint goal is achieved
2. All P0 tasks are complete
3. At least 80% of planned tasks are done
4. Deliverable is functional and tested
5. No major regressions introduced
6. Documentation is updated

---

## Tracking

**Tools**:
- GitHub Projects for task tracking
- GitHub Issues for bugs and tasks
- This markdown file for sprint planning
- BACKLOG.md for detailed task list

**Updates**:
- Update sprint status daily
- Move completed tasks to "Done"
- Add discovered tasks to backlog
- Update time estimates based on actuals

---

## Notes

- Sprints are flexible and can be adjusted based on progress
- Some tasks may move between sprints
- New tasks will be discovered during development
- Priority may shift based on findings and user feedback
- Always maintain working software at end of each sprint
