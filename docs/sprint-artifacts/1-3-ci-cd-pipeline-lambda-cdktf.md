# Story 1.3: CI/CD Pipeline with Lambda Deployment (Dev Only)

Status: drafted

## Story

As a **developer merging code**,
I want **automated build, test, and deployment to dev environment**,
so that **changes are validated and deployed without manual steps**.

## Acceptance Criteria

1. **Build stage completes successfully**

   - Given code is pushed to GitHub `dev` branch
   - When GitHub Actions workflow runs
   - Then build stage includes:
     - Checkout code
     - Install Go 1.25.4
     - Install Node.js 24.x LTS
     - `go build` for all 3 services (REST-only)
     - `npm run build` for web (Next.js 16)
     - Build Lambda deployment packages (zip with `bootstrap` binary)
     - Artifacts uploaded

2. **Test stage passes**

   - `go test ./...` for all services
   - `npm run test` for web (Vitest)
   - Linting: `golangci-lint`, `eslint`, `tsc --noEmit`
   - Coverage reports generated
   - Tests must pass before deployment

3. **Deploy to dev stage succeeds**

   - Authenticate to AWS using GitHub OIDC (no long-lived credentials)
   - CDKTF `cdktf synth` (generate Terraform JSON)
   - CDKTF `cdktf deploy --auto-approve` (dev stack only)
   - Deploy to AWS Lambda (all 3 services as separate functions)
   - Deploy web to S3 + CloudFront
   - Run smoke tests against dev endpoints (Story 1.4)

4. **CDKTF infrastructure deployed**

   - **GitHub Repository Configuration:**
     - Branch protection rules (main, dev - require PR reviews)
     - GitHub OIDC provider setup in AWS
     - GitHub Actions IAM role with deployment permissions
   - **AWS Lambda Functions:**
     - `shtetl-dev-zmanim` (REST API on port 8101)
     - `shtetl-dev-shul` (REST API on port 8103)
     - `shtetl-dev-kehilla` (REST API on port 8105)
   - **API Gateway REST API** (not HTTP API)
   - **RDS PostgreSQL 18** (db.t4g.micro for cost control)
   - **ElastiCache Redis 8.4** (cache.t4g.micro for cost control)
   - **Private VPC** with NAT Gateway for Lambda → RDS/Redis access
   - **CloudWatch Logs + Alarms** (basic monitoring)
   - **S3 bucket + CloudFront** for web hosting
   - **S3 + DynamoDB** for Terraform state backend

5. **Lambda-compatible service structure implemented**

   - Services refactored to support both deployment modes:
     - `cmd/lambda/main.go` - AWS Lambda handler (uses aws-lambda-go-api-proxy)
     - `cmd/server/main.go` - HTTP server (for Coder local dev)
     - Shared business logic in `internal/` (no duplication)

6. **Pipeline triggers configured**

   - On push to `dev` → deploy to dev environment
   - On push to `main` → build + test only (no deploy yet - staging/prod deferred)
   - On pull request → build + test only (no deploy)

7. **GitHub OIDC authentication configured**
   - No AWS access keys stored in GitHub Secrets
   - GitHub Actions assumes IAM role via OIDC federation
   - Role scoped to minimal permissions (Lambda deploy, S3 write, CDKTF state)

8. **Lambda alias strategy implemented for safe deployments**
   - Each Lambda function has aliases: `dev-candidate`, `dev`, `staging`, `prod`
   - New deployments point `dev-candidate` to latest version
   - API Gateway routes:
     - `api-dev-candidate.shtetl.com` → `dev-candidate` alias (testing)
     - `api-dev.shtetl.com` → `dev` alias (stable)
   - Promotion to `dev` only after integration tests pass
   - Failed deployments do NOT affect stable `dev` alias

9. **Database migrations run safely before Lambda deployment**
   - Migrations stored in `db/migrations/` (centralized for shared tables)
   - Use golang-migrate for migration management
   - Migrations run BEFORE Lambda deployment
   - Migrations must be EXPAND-only (backwards-compatible)
   - Automated safety checks block unsafe migrations (DROP, RENAME, etc.)
   - Failed migrations block deployment
   - Table ownership documented (core vs service-specific)

10. **Migration safety enforcement prevents breaking changes**
    - Automated checks for unsafe operations (DROP, RENAME, ALTER incompatible)
    - Require DEFAULT values for new NOT NULL columns
    - Backwards-compatibility tests: old Lambda + new schema
    - Manual review required for CONTRACT migrations
    - EXPAND-CONTRACT pattern documented and enforced
    - 24-48h wait period required between EXPAND and CONTRACT phases

11. **Cross-repo integration triggered after deployment**
    - Successful deployment dispatches webhook to main repo
    - Payload includes: service name, commit SHA, environment, API endpoint
    - Main repo integration workflow triggered automatically
    - Deployment workflow waits for integration test result
    - Failed integration tests BLOCK promotion to `dev` alias

12. **Schema compatibility tests verify cross-service safety**
    - Before migration: test backwards compatibility
    - After deployment: verify all services can read/write
    - Block deployment if migration breaks another service
    - Schema ownership enforced (shared tables require cross-service review)
    - Automated detection of breaking schema changes

## Tasks / Subtasks

- [ ] **Task 1: Create CDKTF infrastructure project** (AC: 4)

  - [ ] 1.1 Create `infrastructure/` directory in main repo
  - [ ] 1.2 Initialize CDKTF TypeScript project (`cdktf init --template=typescript`)
  - [ ] 1.3 Install AWS provider (`@cdktf/provider-aws`)
  - [ ] 1.4 Install GitHub provider (`@cdktf/provider-github`)
  - [ ] 1.5 Create Terraform state backend stack (S3 + DynamoDB)
  - [ ] 1.6 Configure CDKTF to use S3 backend

- [ ] **Task 2: Implement GitHub repository governance** (AC: 4, 7)

  - [ ] 2.1 Create GitHub OIDC provider in AWS
  - [ ] 2.2 Create IAM role for GitHub Actions with trust policy
  - [ ] 2.3 Attach IAM policies (Lambda, S3, CloudWatch, RDS, ElastiCache)
  - [ ] 2.4 Configure branch protection rules (main, dev)
  - [ ] 2.5 Document OIDC setup in infrastructure README

- [ ] **Task 3: Implement AWS infrastructure** (AC: 4)

  - [ ] 3.1 Create VPC with public/private subnets
  - [ ] 3.2 Create NAT Gateway for Lambda egress
  - [ ] 3.3 Create RDS PostgreSQL 18 (db.t4g.micro) in private subnet
  - [ ] 3.4 Create ElastiCache Redis 8.4 (cache.t4g.micro) in private subnet
  - [ ] 3.5 Create security groups (Lambda → RDS/Redis)
  - [ ] 3.6 Create API Gateway REST API
  - [ ] 3.7 Create S3 bucket for web hosting
  - [ ] 3.8 Create CloudFront distribution
  - [ ] 3.9 Create CloudWatch log groups for Lambda functions

- [ ] **Task 4: Refactor services for Lambda compatibility** (AC: 5)

  - [ ] 4.1 Create `cmd/lambda/main.go` for zmanim service
  - [ ] 4.2 Create `cmd/lambda/main.go` for shul service
  - [ ] 4.3 Create `cmd/lambda/main.go` for kehilla service
  - [ ] 4.4 Install `github.com/awslabs/aws-lambda-go-api-proxy`
  - [ ] 4.5 Wrap HTTP handlers with Lambda proxy adapter
  - [ ] 4.6 Test Lambda handlers locally with SAM or Lambda RIE
  - [ ] 4.7 Update go.mod dependencies

- [ ] **Task 5: Create Lambda deployment stack** (AC: 4)

  - [ ] 5.1 Create Lambda function resource (zmanim)
  - [ ] 5.2 Create Lambda function resource (shul)
  - [ ] 5.3 Create Lambda function resource (kehilla)
  - [ ] 5.4 Configure Lambda runtime (`provided.al2023`)
  - [ ] 5.5 Configure environment variables (RDS, Redis connection strings)
  - [ ] 5.6 Attach Lambda execution role
  - [ ] 5.7 Configure VPC integration (private subnets)
  - [ ] 5.8 Configure API Gateway integrations

- [ ] **Task 6: Create GitHub Actions workflow** (AC: 1, 2, 3, 6)

  - [ ] 6.1 Create `.github/workflows/ci-cd.yml`
  - [ ] 6.2 Implement build stage (Go build, npm build, Lambda packaging)
  - [ ] 6.3 Implement test stage (go test, npm test, linting)
  - [ ] 6.4 Implement deploy stage (CDKTF synth + deploy)
  - [ ] 6.5 Configure OIDC authentication step
  - [ ] 6.6 Add smoke tests execution (call Story 1.4 smoke tests)
  - [ ] 6.7 Configure branch-based triggers

- [ ] **Task 7: Test end-to-end deployment** (AC: 3)
  - [ ] 7.1 Push to dev branch
  - [ ] 7.2 Verify workflow runs successfully
  - [ ] 7.3 Verify Lambda functions deployed
  - [ ] 7.4 Verify API Gateway endpoints accessible
  - [ ] 7.5 Verify RDS and ElastiCache connectivity
  - [ ] 7.6 Verify smoke tests pass
  - [ ] 7.7 Document deployment URL (https://api-dev.shtetl.com)

- [ ] **Task 8: Implement Lambda alias strategy** (AC: 8)
  - [ ] 8.1 Create Lambda aliases: `dev-candidate`, `dev`, `staging`, `prod`
  - [ ] 8.2 Configure API Gateway to route traffic:
        - `api-dev-candidate.shtetl.com` → `dev-candidate` alias
        - `api-dev.shtetl.com` → `dev` alias
  - [ ] 8.3 Update deployment workflow to point `dev-candidate` to new version
  - [ ] 8.4 Add workflow job to wait for integration test result
  - [ ] 8.5 On success: Promote `dev` alias to new version
  - [ ] 8.6 On failure: Leave `dev` alias unchanged, exit with error
  - [ ] 8.7 Document alias strategy in `infrastructure/README.md`

- [ ] **Task 9: Set up centralized migration infrastructure** (AC: 9)
  - [ ] 9.1 Install golang-migrate in deployment workflow
  - [ ] 9.2 Create `shtetl-api/db/migrations/` directory (centralized)
  - [ ] 9.3 Create initial migration: `000001_init_core_schema.up.sql`
  - [ ] 9.4 Document table ownership (core vs service-specific)
        - Core tables: shuls, users, organizations (centralized)
        - Service tables: service-specific migrations directories
  - [ ] 9.5 Configure RDS connection for migrations
  - [ ] 9.6 Test migration execution locally in Coder workspace
  - [ ] 9.7 Create migration developer guide: `docs/database-migrations.md`

- [ ] **Task 10: Implement migration safety checks** (AC: 10, 12)
  - [ ] 10.1 Create `.github/workflows/migration-safety-check.yml`
  - [ ] 10.2 Add automated checks for unsafe operations:
        - Block: DROP COLUMN, DROP TABLE, RENAME COLUMN
        - Block: ALTER COLUMN without backwards compatibility
        - Block: NOT NULL without DEFAULT value
  - [ ] 10.3 Add migration type classification (EXPAND vs CONTRACT)
  - [ ] 10.4 Require manual approval for CONTRACT migrations
  - [ ] 10.5 Create migration PR template with safety checklist
  - [ ] 10.6 Document EXPAND-CONTRACT pattern in detail
  - [ ] 10.7 Test safety checks with example unsafe migrations

- [ ] **Task 11: Implement backwards-compatibility testing** (AC: 10, 12)
  - [ ] 11.1 Create backwards-compatibility test workflow
  - [ ] 11.2 Deploy OLD Lambda code to test environment
  - [ ] 11.3 Run NEW migration against test database
  - [ ] 11.4 Verify OLD Lambda can still read/write data
  - [ ] 11.5 Block deployment if compatibility test fails
  - [ ] 11.6 Add to CI pipeline (run before migration)
  - [ ] 11.7 Document testing procedure for developers

- [ ] **Task 12: Integrate migrations into deployment** (AC: 9)
  - [ ] 12.1 Add migration step to deployment workflow (before Lambda deploy)
  - [ ] 12.2 Configure RDS connection string in GitHub Secrets
  - [ ] 12.3 Run migrations: `migrate -path db/migrations -database $DB_URL up`
  - [ ] 12.4 Capture migration logs in CloudWatch
  - [ ] 12.5 Block Lambda deployment if migration fails
  - [ ] 12.6 Test migration execution in CI
  - [ ] 12.7 Document migration rollback procedures

- [ ] **Task 13: Configure cross-repo integration webhooks** (AC: 11)
  - [ ] 13.1 Create GitHub Personal Access Token for cross-repo triggers
  - [ ] 13.2 Store PAT in submodule repo secrets: `MAIN_REPO_TOKEN`
  - [ ] 13.3 Add repository_dispatch step to deployment workflow
  - [ ] 13.4 Send payload: service, commit SHA, environment, API URL
  - [ ] 13.5 Configure workflow to wait for integration test result
  - [ ] 13.6 Parse integration test status (success/failure)
  - [ ] 13.7 Test webhook delivery from submodule → main repo

- [ ] **Task 14: Implement deployment blocking logic** (AC: 11)
  - [ ] 14.1 Add workflow_run trigger to listen for integration results
  - [ ] 14.2 Block deployment until integration workflow completes
  - [ ] 14.3 Check integration workflow conclusion (success/failure)
  - [ ] 14.4 On failure: Do NOT promote `dev` alias, mark deployment failed
  - [ ] 14.5 On success: Promote `dev` alias, mark deployment successful
  - [ ] 14.6 Add timeout: fail if integration tests don't complete in 10 minutes
  - [ ] 14.7 Document promotion process in runbook

- [ ] **Task 15: Create migration documentation** (AC: 9, 10)
  - [ ] 15.1 Document EXPAND-CONTRACT pattern with examples
  - [ ] 15.2 Create migration checklist template
  - [ ] 15.3 Document forwards-compatibility requirements
  - [ ] 15.4 Provide migration examples (add column, add table, rename column)
  - [ ] 15.5 Document testing migrations locally in Coder
  - [ ] 15.6 Document RLS policy patterns for multi-tenancy
  - [ ] 15.7 Create troubleshooting guide for migration failures

## Dev Notes

### Technical Details

**Lambda Build Process:**

```bash
# Build for Lambda (provided.al2023 requires binary named "bootstrap")
GOOS=linux GOARCH=amd64 CGO_ENABLED=0 go build -o bootstrap cmd/lambda/main.go
zip function.zip bootstrap
```

**Lambda Runtime:**

- Runtime: `provided.al2023` (Amazon Linux 2023)
- Go version: 1.25.4
- Binary name: `bootstrap` (required for custom runtime)
- Handler: Not needed for custom runtime

**API Gateway:**

- Type: REST API ($3.50 per 1M requests)
- Features: Caching, usage plans, API keys, WAF integration
- Endpoints:
  - `/zmanim/*` → Lambda: shtetl-dev-zmanim
  - `/shul/*` → Lambda: shtetl-dev-shul
  - `/kehilla/*` → Lambda: shtetl-dev-kehilla

**CDKTF State Backend Bootstrap:**

1. Initial CDKTF deploy creates S3 + DynamoDB (local state)
2. Configure CDKTF to use S3 backend
3. Migrate local state to S3

**Cost Estimate (Dev Environment):**

- RDS db.t4g.micro: ~$15/month
- ElastiCache cache.t4g.micro: ~$12/month
- NAT Gateway: ~$33/month
- Lambda: Free tier (1M requests/month)
- API Gateway: Free tier (1M requests/month)
- S3 + CloudFront: ~$1/month
- **Total: ~$61/month**

### Lambda Alias Strategy

**Alias Architecture:**
```
AWS Lambda Function: shtetl-dev-zmanim

Versions:
├── $LATEST (always newest code)
├── 42 (previous stable)
├── 43 (current stable)
└── 44 (newly deployed, testing)

Aliases:
├── dev-candidate → version 44 (testing)
├── dev → version 43 (stable, serving traffic)
├── staging → version 41
└── prod → version 38

API Gateway Routes:
├── api-dev-candidate.shtetl.com → dev-candidate alias
└── api-dev.shtetl.com → dev alias (stable)
```

**Deployment Flow:**
1. New code merged → Build Lambda version 44
2. Point `dev-candidate` alias to version 44
3. Run integration tests against dev-candidate
4. **IF PASS:** Promote `dev` alias to version 44 ✅
5. **IF FAIL:** Leave `dev` alias on version 43 (stable) 🚫

**Zero-Downtime Guarantee:**
- `dev` alias always points to known-good version
- Integration test failures never affect stable endpoint
- Developers can debug failures at dev-candidate endpoint

### Database Migration Strategy

**EXPAND-CONTRACT Pattern:**

**Phase 1: EXPAND (Backwards-Compatible)**
```sql
-- Migration 015: Add new column (safe for old Lambda code)
ALTER TABLE shuls ADD COLUMN timezone VARCHAR(50) DEFAULT 'America/New_York';
```

**Deployment:**
1. Migration runs (adds column)
2. Lambda v42 still serving (ignores new column) ✅
3. Lambda v43 deployed (uses new column)
4. Integration tests pass
5. Lambda v43 promoted to dev ✅

**Wait Period:** 24-48 hours for stability

**Phase 2: CONTRACT (Remove Old)**
```sql
-- Migration 016: Drop old column (separate deployment)
ALTER TABLE shuls DROP COLUMN legacy_timezone;
```

**Migration Safety Checks:**
```yaml
# Automated blocking of unsafe operations
BLOCKED:
  - DROP COLUMN / DROP TABLE
  - RENAME COLUMN
  - ALTER COLUMN (type changes)
  - NOT NULL without DEFAULT

ALLOWED:
  - ADD COLUMN (with DEFAULT or NULL)
  - ADD TABLE
  - ADD INDEX
  - ADD CONSTRAINT (if backwards-compatible)
```

**Centralized Migration Ownership:**
```
shtetl-api/db/migrations/    # Core tables (shuls, users, orgs)
├── 000001_init_core.up.sql
├── 000002_add_shul_timezone.up.sql
└── 000003_add_rls_policies.up.sql

shtetl-api/zmanim/migrations/  # Zmanim-only tables
shtetl-api/shul/migrations/    # Shul-only tables
shtetl-api/kehilla/migrations/ # Kehilla-only tables
```

### Cross-Repo Integration Flow

**Deployment Sequence:**
```
1. Developer merges PR to shtetl-api/dev
   ↓
2. Safety checks run (migration validation)
   ↓
3. Backwards-compatibility test (old Lambda + new schema)
   ↓
4. Migration runs
   ↓
5. Lambda v44 deployed to dev-candidate alias
   ↓
6. Repository dispatch webhook → shtetl main repo
   ↓
7. shtetl/.github/workflows/integration-test.yml runs
   - Update submodule reference (locally)
   - Run cross-service tests
   ↓
8. IF PASS:
   - Promote dev alias to v44
   - Commit submodule reference
   - Mark deployment successful
   ↓
9. IF FAIL:
   - Leave dev alias on v43
   - Create GitHub issue
   - Alert team (Slack + email)
   - Block deployment
```

### Slack Integration Strategy

**What CDKTF Creates (Story 1.3):**
- SNS topics: `shtetl-dev-platform-alerts`
- SNS email subscriptions: `platform-alerts@shtetl.io`
- CloudWatch Alarms → SNS topic routing

**What GitHub Actions Handles (Story 1.4):**
- Slack webhook for integration test failures
- Webhook URL created in Slack UI: https://hooks.slack.com/services/T.../B.../...
- Stored in GitHub Secrets: `SLACK_WEBHOOK_URL`
- GitHub Actions workflow posts directly to Slack (not via SNS)

**Why Split?**
- SNS → Email: Simple, infrastructure-managed
- GitHub Actions → Slack: More control over message format, context from workflow

**Setup Steps:**
1. Create Slack webhook: https://api.slack.com/messaging/webhooks
2. Add to GitHub Secrets (in each submodule repo + main repo)
3. Workflow uses webhook directly for rich formatting

### Repository Dispatch Webhook

**Trigger from Submodule:**
```yaml
# shtetl-api/.github/workflows/deploy.yml
- name: Trigger Integration Tests
  uses: peter-evans/repository-dispatch@v2
  with:
    token: ${{ secrets.MAIN_REPO_TOKEN }}
    repository: jcom-dev/shtetl
    event-type: service-deployed
    client-payload: |
      {
        "service": "zmanim",
        "commit_sha": "${{ github.sha }}",
        "environment": "dev",
        "api_url": "https://api-dev-candidate.shtetl.com"
      }
```

**Receive in Main Repo:**
```yaml
# shtetl/.github/workflows/integration-test.yml
on:
  repository_dispatch:
    types: [service-deployed]

jobs:
  integration-test:
    runs-on: ubuntu-latest
    steps:
      - name: Run Integration Tests
        env:
          SERVICE: ${{ github.event.client_payload.service }}
          API_URL: ${{ github.event.client_payload.api_url }}
        run: |
          # Test cross-service integration
          make test-integration-cross-repo
```

### Prerequisites

- Story 1.1 (Multi-Repository Structure Setup)
- Story 1.2 (Coder Workspace Template)
- Story 1.6 (API Contract Design - OpenAPI specs)

### Deliverables

**CI/CD Workflows:**
- `.github/workflows/ci-cd.yml` - Main CI/CD pipeline
- `.github/workflows/migration-safety-check.yml` - Migration validation
- `.github/workflows/integration-test.yml` - Cross-repo integration (in main repo)

**Infrastructure:**
- `infrastructure/` - CDKTF TypeScript project
- `infrastructure/stacks/dev.ts` - Dev environment with Lambda aliases
- `infrastructure/stacks/bootstrap.ts` - Terraform state backend
- Lambda aliases: `dev-candidate`, `dev`, `staging`, `prod`

**Code:**
- `cmd/lambda/main.go` - Lambda entry points for all 3 services
- `cmd/server/main.go` - HTTP server for local development

**Database:**
- `db/migrations/` - Centralized migration directory (core tables)
- `db/migrations/000001_init_core_schema.up.sql` - Initial schema
- `db/schema.sql` - Complete schema dump (reference)

**Documentation:**
- `docs/database-migrations.md` - Migration guide with EXPAND-CONTRACT pattern
- `infrastructure/README.md` - Lambda alias strategy, deployment process
- `.github/pull_request_template.md` - PR template with migration checklist
- `docs/deployment-runbook.md` - Deployment procedures and troubleshooting

**Endpoints:**
- Working dev environment: https://api-dev.shtetl.com (stable)
- Testing environment: https://api-dev-candidate.shtetl.com (pre-integration)

### Future Work

- Story 1.14: Staging and production environments
- Story 1.15: Blue/green deployments
- Story 1.16: Canary deployments
