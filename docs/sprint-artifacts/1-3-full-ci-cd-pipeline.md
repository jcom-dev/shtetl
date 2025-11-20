# Story 1.3: CI/CD Pipeline with Lambda Deployment (Dev Only)

Status: ready-for-dev

## Story

As a developer merging code,
I want automated build, test, and deployment to dev environment,
So that changes are validated and deployed without manual steps.

## Acceptance Criteria

### AC1: Build Stage
**Given** code is pushed to GitHub `dev` branch
**When** CI/CD pipeline runs
**Then** GitHub Actions workflow includes:
- Checkout code
- Install Go 1.25.4
- Install Node.js 24.x LTS
- `go build` for all 3 services (REST-only)
- `npm run build` for web (Next.js 16)
- Build Lambda deployment packages (zip with Go binary)
- Artifacts uploaded

### AC2: Test Stage
**Given** build stage completes successfully
**When** test stage runs
**Then**:
- `go test ./...` for all services
- `npm run test` for web (Vitest)
- Linting: `golangci-lint`, `eslint`, `tsc --noEmit`
- Coverage reports generated
- Tests must pass before deployment

### AC3: Deploy to Dev Stage
**Given** test stage passes
**When** deploy stage runs
**Then**:
- Authenticate to AWS using OIDC (no long-lived credentials)
- CDKTF `cdktf synth` (generate Terraform JSON)
- CDKTF `cdktf deploy --auto-approve` (dev stack only)
- Deploy to AWS Lambda (all 3 services as separate functions)
- Deploy web to S3 + CloudFront
- Run smoke tests against dev endpoints

### AC4: CDKTF Infrastructure
**Given** deployment is configured
**When** CDKTF code is applied
**Then** infrastructure includes:

**GitHub Repository Configuration:**
- Branch protection rules (main, dev - require PR reviews)
- GitHub OIDC provider setup in AWS
- GitHub Actions IAM role with deployment permissions

**AWS Lambda Functions:**
- `shtetl-dev-zmanim` (REST API on port 8101)
- `shtetl-dev-shul` (REST API on port 8103)
- `shtetl-dev-kehilla` (REST API on port 8105)

**API Gateway HTTP API** (REST endpoints, no gRPC)
**RDS PostgreSQL 18** (db.t4g.micro for cost control)
**ElastiCache Redis 8.4** (cache.t4g.micro for cost control)
**Private VPC** with NAT Gateway for Lambda → RDS/Redis access
**CloudWatch Logs + Alarms** (basic monitoring)
**S3 bucket + CloudFront** for web hosting

### AC5: Lambda-Compatible Service Structure
**Given** services need to support Lambda and local dev
**When** code is structured
**Then** services are refactored to support both deployment modes:
- `cmd/lambda/main.go` - AWS Lambda handler (uses aws-lambda-go-api-proxy)
- `cmd/server/main.go` - HTTP server (for Coder local dev)
- Shared business logic in `internal/` (no duplication)

### AC6: Pipeline Triggers
**Given** code changes are pushed
**When** triggers are evaluated
**Then**:
- On push to `dev` → deploy to dev environment
- On push to `main` → build + test only (no deploy yet - staging/prod deferred)
- On pull request → build + test only (no deploy)

### AC7: GitHub OIDC Authentication
**Given** deployment requires AWS credentials
**When** GitHub Actions authenticates
**Then**:
- No AWS access keys stored in GitHub Secrets
- GitHub Actions assumes IAM role via OIDC federation
- Role scoped to minimal permissions (Lambda deploy, S3 write, CDKTF state)

## Tasks / Subtasks

- [ ] Task 1: Configure GitHub Actions Workflow (AC1, AC2, AC6)
  - [ ] Create `.github/workflows/ci-cd.yml`
  - [ ] Add build job (Go, Node.js, Lambda packaging)
  - [ ] Add test job (go test, npm test, linting)
  - [ ] Add deploy job with dev environment condition
  - [ ] Configure job dependencies and artifact passing

- [ ] Task 2: Set up GitHub OIDC + AWS IAM (AC7, AC4)
  - [ ] Create AWS OIDC provider for GitHub
  - [ ] Create IAM role with trust policy for GitHub Actions
  - [ ] Add minimal permissions policy (Lambda, S3, CDKTF state, CloudWatch)
  - [ ] Store OIDC role ARN in GitHub repository variables

- [ ] Task 3: Implement CDKTF Infrastructure as Code (AC4)
  - [ ] Create `infrastructure/` directory with TypeScript CDKTF project
  - [ ] Define GitHub provider stack (branch protection, OIDC setup)
  - [ ] Define AWS Lambda functions stack (3 functions with API Gateway)
  - [ ] Define RDS PostgreSQL 18 instance (db.t4g.micro)
  - [ ] Define ElastiCache Redis 8.4 instance (cache.t4g.micro)
  - [ ] Define VPC with private subnets and NAT Gateway
  - [ ] Define S3 bucket + CloudFront distribution for web hosting
  - [ ] Define CloudWatch log groups and basic alarms
  - [ ] Add `cdktf.json` configuration

- [ ] Task 4: Refactor Services for Lambda Compatibility (AC5)
  - [ ] Create `cmd/lambda/main.go` for Zmanim service
  - [ ] Create `cmd/lambda/main.go` for Shul service
  - [ ] Create `cmd/lambda/main.go` for Kehilla service
  - [ ] Install `github.com/awslabs/aws-lambda-go-api-proxy`
  - [ ] Wrap HTTP handlers with aws-lambda-go-api-proxy
  - [ ] Ensure `cmd/server/main.go` still works for local dev
  - [ ] Test both deployment modes locally

- [ ] Task 5: Lambda Packaging and Deployment (AC3)
  - [ ] Create build script to compile Go binaries for Lambda (GOOS=linux GOARCH=arm64)
  - [ ] Create zip packaging step with runtime dependencies
  - [ ] Add CDKTF synth step to workflow
  - [ ] Add CDKTF deploy step to workflow (dev only, auto-approve)
  - [ ] Configure environment variables for Lambda functions (DB, Redis, Clerk)

- [ ] Task 6: Web Deployment to S3 + CloudFront (AC3)
  - [ ] Add Next.js build step with static export
  - [ ] Add S3 sync step to upload build artifacts
  - [ ] Add CloudFront invalidation step
  - [ ] Configure custom domain (api-dev.shtetl.com)

- [ ] Task 7: Smoke Tests (AC3)
  - [ ] Create smoke test script (`tests/smoke/`)
  - [ ] Test health endpoints for all 3 services
  - [ ] Test database connectivity from Lambda
  - [ ] Test Redis connectivity from Lambda
  - [ ] Run smoke tests after deployment in CI/CD
  - [ ] Fail deployment if smoke tests fail

- [ ] Task 8: Documentation and Testing (All ACs)
  - [ ] Document CDKTF stack structure in README
  - [ ] Document deployment process for dev environment
  - [ ] Document rollback procedure
  - [ ] Test full pipeline end-to-end on dev branch
  - [ ] Verify dev URL https://api-dev.shtetl.com works
  - [ ] Verify cost estimate is ~$50/month

## Dev Notes

### Architecture Constraints
- **REST-only for Lambda**: Simplified Lambda deployment without gRPC complexity (Lambda supports HTTP/REST natively via API Gateway)
- **CDKTF over pure Terraform**: Vendor-neutral infrastructure as code with TypeScript type safety
- **Lambda + API Gateway**: Serverless REST endpoints for cost efficiency in dev/staging
- **GitHub OIDC**: Security best practice - no long-lived credentials in GitHub
- **Shared code structure**: `internal/` shared between `cmd/lambda/` and `cmd/server/`

### Service Structure
Each Go service needs two entrypoints:
- `cmd/lambda/main.go`: AWS Lambda handler (wraps HTTP server with aws-lambda-go-api-proxy)
- `cmd/server/main.go`: Standard HTTP server (for Coder local development)

Business logic in `internal/` remains unchanged.

### Testing Strategy
- Unit tests: Run in CI on every commit
- Integration tests: Run in CI with testcontainers
- Smoke tests: Run post-deployment against live dev endpoints
- Coverage threshold: 80% minimum (NFR-MAINT-1)

### Cost Optimization
- Dev environment estimated ~$50/month:
  - RDS db.t4g.micro: ~$15/month
  - ElastiCache cache.t4g.micro: ~$12/month
  - NAT Gateway: ~$20/month
  - Lambda: Free tier (minimal usage in dev)
  - S3 + CloudFront: <$5/month

### Project Structure
```
shtetl-api/
  zmanim/
    cmd/
      lambda/main.go   # Lambda handler
      server/main.go   # HTTP server
    internal/          # Shared business logic
    api/              # OpenAPI specs
  shul/
    cmd/
      lambda/main.go
      server/main.go
    internal/
  kehilla/
    cmd/
      lambda/main.go
      server/main.go
    internal/

infrastructure/
  stacks/
    github.ts        # GitHub provider (branch protection, OIDC)
    aws-lambda.ts    # Lambda functions + API Gateway
    aws-rds.ts       # PostgreSQL RDS
    aws-vpc.ts       # VPC + NAT Gateway
    aws-web.ts       # S3 + CloudFront
  cdktf.json
  main.ts

.github/
  workflows/
    ci-cd.yml        # Main CI/CD pipeline
```

### References
- [Source: docs/epics.md#Story-1-3-CI-CD-Pipeline-with-Lambda-Deployment]
- [Source: docs/prd.md - FR99 Health monitoring and error alerting]
- [Source: docs/prd.md - FR100 Database backups and disaster recovery]
- Prerequisites: Story 1.1 (repos), Story 1.2 (Coder workspace), Story 1.6 (REST API contracts)

## Dev Agent Record

### Context Reference

<!-- Path(s) to story context XML will be added here by context workflow -->

### Agent Model Used

_Will be populated during development_

### Debug Log References

_Will be populated during development_

### Completion Notes List

_Will be populated during development_

### File List

_Will be populated during development_
