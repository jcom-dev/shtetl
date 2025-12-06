# Story 1.4: Integration Testing Framework

Status: ready-for-dev

## Story

As a **developer**,
I want **early-stage integration testing infrastructure with test APIs**,
so that **we can validate deployments and contracts from day one**.

## Acceptance Criteria

1. **Pre-deployment integration test harness implemented**

   - Given basic services and CI/CD exist
   - When integration test framework is implemented
   - Then test infrastructure includes:
     - `tests/integration/` directory in shtetl-api repo
     - Go test framework with `testing` package
     - HTTP client helpers for testing REST APIs
     - Test environment configuration (GitHub Actions service containers)

2. **Health check tests pass locally and in CI**

   - Tests verify `/health` endpoints return 200 for all 3 services
   - Tests run against local services (cmd/server/main.go)
   - GitHub Actions service containers:
     - postgres:18-alpine (port 5432)
     - redis:8.4-alpine (port 6379)

3. **Connectivity tests validate infrastructure**

   - Test PostgreSQL connection (SELECT 1)
   - Test Redis connection (PING)
   - Test database pool configuration
   - Test Redis TTL operations

4. **OpenAPI contract validation configured**

   - go-swagger library installed
   - REST endpoint contract tests (response matches OpenAPI 3.1 schema)
   - Basic contract tests for health endpoints
   - Contract validation in CI pipeline

5. **Test data helpers available**

   - Helper functions to create test shuls
   - Helper functions to seed test database
   - Cleanup functions (teardown after tests)
   - Test fixtures for common scenarios

6. **Integration tests run in CI/CD pipeline**

   - `make test-integration` runs all integration tests locally
   - Tests run against local Coder workspace services
   - CI runs integration tests on every commit
   - Clear test output with readable failure messages
   - Tests complete in <30 seconds

7. **Post-deployment smoke tests implemented**

   - `tests/smoke/` directory in shtetl-api repo
   - Smoke tests run after deployment to dev/staging/prod
   - Tests verify deployed AWS infrastructure:
     - Lambda function execution via API Gateway
     - RDS connection from Lambda
     - ElastiCache connection from Lambda
     - End-to-end request flow
   - Failed smoke tests block deployment progression

8. **Smoke tests executable with environment variable**
   - Command: `make test-smoke`
   - Environment variable: `API_BASE_URL=https://api-dev.shtetl.com`
   - Tests curl deployed API Gateway endpoints
   - Tests verify database/Redis connectivity through Lambda

9. **Cross-repository integration tests verify multi-service flows**
   - Located in `shtetl/tests/integration-cross-repo/` (main repo)
   - Test API + Web integration (web can call API endpoints)
   - Test API + Mobile integration (mobile can fetch data)
   - Test cross-service workflows (e.g., create shul in API, view in Web)
   - Run against dev-candidate endpoints before promotion
   - Failed tests BLOCK deployment to dev environment

10. **Integration workflow updates submodule references automatically**
    - Triggered by repository_dispatch from submodule deployments
    - Updates submodule SHA to deployed commit (locally)
    - Runs cross-repo integration tests
    - On success: Commits updated submodule reference to main repo dev branch
    - On failure: Does NOT update reference, creates GitHub issue with details

11. **Schema compatibility tests prevent breaking changes**
    - Test backwards compatibility: old Lambda + new schema
    - Test forwards compatibility: new Lambda + old schema
    - Verify all services can read/write after schema changes
    - Block deployment if migration breaks another service
    - Verify RLS policies don't break cross-service access
    - Schema diff analysis before/after migrations

12. **Integration test failures trigger alerts and block promotion**
    - Failed tests prevent Lambda alias promotion to dev
    - GitHub issue created automatically with failure details
    - Slack/Discord alert posted with @channel mention
    - Email sent to platform-alerts@shtetl.io
    - GitHub deployment marked as FAILED
    - Developers can debug at dev-candidate endpoint

## Tasks / Subtasks

- [ ] **Task 1: Create integration test structure** (AC: 1)

  - [ ] 1.1 Create `tests/integration/` directory in shtetl-api
  - [ ] 1.2 Create `tests/integration/helpers.go` (HTTP client, assertions)
  - [ ] 1.3 Create `tests/integration/config.go` (test environment config)
  - [ ] 1.4 Install `github.com/stretchr/testify` for assertions
  - [ ] 1.5 Create Makefile target `make test-integration`

- [ ] **Task 2: Implement health check tests** (AC: 2)

  - [ ] 2.1 Create `tests/integration/health_test.go`
  - [ ] 2.2 Test zmanim service health endpoint (localhost:8101)
  - [ ] 2.3 Test shul service health endpoint (localhost:8103)
  - [ ] 2.4 Test kehilla service health endpoint (localhost:8105)
  - [ ] 2.5 Verify JSON response format
  - [ ] 2.6 Verify status codes

- [ ] **Task 3: Implement connectivity tests** (AC: 3)

  - [ ] 3.1 Create `tests/integration/connectivity_test.go`
  - [ ] 3.2 Test PostgreSQL connection (SELECT 1)
  - [ ] 3.3 Test Redis PING
  - [ ] 3.4 Test database connection pool
  - [ ] 3.5 Test Redis SET/GET operations
  - [ ] 3.6 Handle connection errors gracefully

- [ ] **Task 4: Configure OpenAPI contract validation** (AC: 4)

  - [ ] 4.1 Install `github.com/go-swagger/go-swagger`
  - [ ] 4.2 Create `tests/integration/contracts_test.go`
  - [ ] 4.3 Load OpenAPI 3.1 specs from `api/openapi.yaml` (per service)
  - [ ] 4.4 Validate health endpoint responses match schema
  - [ ] 4.5 Create helper for schema validation
  - [ ] 4.6 Add contract tests to CI pipeline

- [ ] **Task 5: Create test data helpers** (AC: 5)

  - [ ] 5.1 Create `tests/integration/fixtures/` directory
  - [ ] 5.2 Create `fixtures/shul.go` (test shul creation helpers)
  - [ ] 5.3 Create `fixtures/db.go` (seed/cleanup functions)
  - [ ] 5.4 Create `fixtures/teardown.go` (cleanup after tests)
  - [ ] 5.5 Document fixture usage in tests/README.md

- [ ] **Task 6: Integrate tests with CI/CD** (AC: 6)

  - [ ] 6.1 Update `.github/workflows/ci-cd.yml`
  - [ ] 6.2 Add GitHub Actions service containers (postgres, redis)
  - [ ] 6.3 Start Go services in background (cmd/server/main.go)
  - [ ] 6.4 Run `make test-integration`
  - [ ] 6.5 Upload test results and coverage reports
  - [ ] 6.6 Fail workflow if tests fail

- [ ] **Task 7: Create smoke test structure** (AC: 7)

  - [ ] 7.1 Create `tests/smoke/` directory in shtetl-api
  - [ ] 7.2 Create `tests/smoke/helpers.go` (HTTP client for deployed APIs)
  - [ ] 7.3 Create `tests/smoke/config.go` (read API_BASE_URL env var)
  - [ ] 7.4 Create Makefile target `make test-smoke`

- [ ] **Task 8: Implement smoke tests** (AC: 7, 8)

  - [ ] 8.1 Create `tests/smoke/health_test.go`
  - [ ] 8.2 Test Lambda health endpoints via API Gateway
  - [ ] 8.3 Create `tests/smoke/infrastructure_test.go`
  - [ ] 8.4 Verify RDS connectivity (query through Lambda)
  - [ ] 8.5 Verify ElastiCache connectivity (Redis PING through Lambda)
  - [ ] 8.6 Test end-to-end request flow

- [ ] **Task 9: Integrate smoke tests with deployment** (AC: 7)
  - [ ] 9.1 Update `.github/workflows/ci-cd.yml`
  - [ ] 9.2 Run smoke tests after CDKTF deployment
  - [ ] 9.3 Set `API_BASE_URL` environment variable
  - [ ] 9.4 Block deployment progression on smoke test failure
  - [ ] 9.5 Add retry logic (3 attempts with 5s delay)
  - [ ] 9.6 Document smoke test usage

- [ ] **Task 10: Create cross-repo integration test structure** (AC: 9)
  - [ ] 10.1 Create `tests/integration-cross-repo/` in main repo
  - [ ] 10.2 Create test helpers for multi-service requests
  - [ ] 10.3 Create config loader (reads API URLs from environment)
  - [ ] 10.4 Create `api_web_test.go` (API + Web integration)
  - [ ] 10.5 Create `api_mobile_test.go` (API + Mobile integration)
  - [ ] 10.6 Create Makefile target: `make test-integration-cross-repo`
  - [ ] 10.7 Document test writing guide

- [ ] **Task 11: Implement cross-service workflow tests** (AC: 9)
  - [ ] 11.1 Test: Create shul in API → Verify visible in Web
  - [ ] 11.2 Test: Update minyan in API → Verify updated in Mobile
  - [ ] 11.3 Test: Subscribe user in Kehilla → Verify notification sent
  - [ ] 11.4 Test: Publish zmanim stream → Verify consumed by Shul service
  - [ ] 11.5 Add test for RLS policy enforcement across services
  - [ ] 11.6 Test authentication flow (Clerk JWT) across all services

- [ ] **Task 12: Create main repo integration workflow** (AC: 10, 12)
  - [ ] 12.1 Create `.github/workflows/integration-test.yml` in main repo
  - [ ] 12.2 Add repository_dispatch trigger (event: service-deployed)
  - [ ] 12.3 Parse payload: service, commit SHA, API URL
  - [ ] 12.4 Update submodule to deployed commit SHA (git submodule update)
  - [ ] 12.5 Run cross-repo integration tests
  - [ ] 12.6 On success: Commit updated submodule reference
  - [ ] 12.7 On failure: Create GitHub issue, alert team

- [ ] **Task 13: Implement schema compatibility tests** (AC: 11)
  - [ ] 13.1 Create `tests/schema-compatibility/` in shtetl-api
  - [ ] 13.2 Test backwards compatibility:
        - Deploy OLD Lambda code to test environment
        - Run NEW migration
        - Verify OLD Lambda can read/write
  - [ ] 13.3 Test forwards compatibility:
        - Keep OLD schema
        - Deploy NEW Lambda code
        - Verify NEW Lambda handles missing fields gracefully
  - [ ] 13.4 Test cross-service schema access (Kehilla reads shuls table)
  - [ ] 13.5 Verify RLS policies after schema changes
  - [ ] 13.6 Add to CI pipeline (run before deployment)

- [ ] **Task 14: Implement deployment promotion logic** (AC: 10, 12)
  - [ ] 14.1 Integration workflow checks test results
  - [ ] 14.2 On success: Call AWS Lambda update-alias API
        - `aws lambda update-alias --name dev --function-version N+1`
  - [ ] 14.3 On success: Commit submodule reference to shtetl/dev
  - [ ] 14.4 On success: Mark GitHub deployment successful
  - [ ] 14.5 On failure: Leave dev alias unchanged
  - [ ] 14.6 On failure: Mark GitHub deployment failed
  - [ ] 14.7 Add deployment status API integration

- [ ] **Task 15: Implement failure alerting** (AC: 12)
  - [ ] 15.1 Create GitHub issue on integration test failure
        - Title: "Integration test failed: {service} {sha}"
        - Body: Test output, logs, dev-candidate URL
  - [ ] 15.2 Configure Slack webhook for alerts
  - [ ] 15.3 Post alert to #engineering channel with @channel
  - [ ] 15.4 Send email to platform-alerts@shtetl.io
  - [ ] 15.5 Add alert template with debugging instructions
  - [ ] 15.6 Test alert delivery for different failure scenarios

- [ ] **Task 16: Configure GitHub App for automated commits** (AC: 10)
  - [ ] 16.1 Create GitHub App for main repo automation
  - [ ] 16.2 Grant permissions: Contents write, Issues write, Deployments write
  - [ ] 16.3 Install App to main repo
  - [ ] 16.4 Store App credentials in GitHub Secrets
  - [ ] 16.5 Update workflow to use App token (not PAT)
  - [ ] 16.6 Test automated commit creation (submodule updates)

## Dev Notes

### Pre-Deployment Integration Tests

**Test Target:** Local services running in GitHub Actions
**Dependencies:** GitHub Actions service containers

```yaml
# .github/workflows/ci-cd.yml
services:
  postgres:
    image: postgres:18-alpine
    env:
      POSTGRES_PASSWORD: test
      POSTGRES_DB: shtetl_test
    ports:
      - 5432:5432

  redis:
    image: redis:8.4-alpine
    ports:
      - 6379:6379
```

**Test Execution:**

```bash
# Start services in background
cd submodules/shtetl-api/zmanim
go run cmd/server/main.go &

cd ../shul
go run cmd/server/main.go &

cd ../kehilla
go run cmd/server/main.go &

# Run integration tests
make test-integration
```

**Test Example:**

```go
func TestZmanimServiceHealth(t *testing.T) {
    resp, err := http.Get("http://localhost:8101/health")
    assert.NoError(t, err)
    assert.Equal(t, 200, resp.StatusCode)

    var body map[string]interface{}
    json.NewDecoder(resp.Body).Decode(&body)
    assert.Equal(t, "healthy", body["status"])
    assert.Equal(t, "zmanim", body["service"])
}
```

### Post-Deployment Smoke Tests

**Test Target:** Deployed AWS infrastructure (Lambda + API Gateway)
**Environment:** Dev, Staging, Production

**Test Execution:**

```bash
# Set API base URL
export API_BASE_URL=https://api-dev.shtetl.com

# Run smoke tests
make test-smoke
```

**Smoke Test Example:**

```go
func TestLambdaHealthEndpoint(t *testing.T) {
    baseURL := os.Getenv("API_BASE_URL")
    require.NotEmpty(t, baseURL, "API_BASE_URL must be set")

    resp, err := http.Get(baseURL + "/zmanim/health")
    assert.NoError(t, err)
    assert.Equal(t, 200, resp.StatusCode)

    // Verify Lambda execution
    var body map[string]interface{}
    json.NewDecoder(resp.Body).Decode(&body)
    assert.Equal(t, "healthy", body["status"])
    assert.Equal(t, "connected", body["database"])
    assert.Equal(t, "connected", body["redis"])
}
```

### OpenAPI Contract Validation

**Library:** go-swagger
**Specs Location:** `submodules/shtetl-api/{service}/api/openapi.yaml`

**Validation Example:**

```go
import "github.com/go-swagger/go-swagger/strfmt"
import "github.com/go-swagger/go-swagger/validate"

func TestHealthContractValidation(t *testing.T) {
    // Load OpenAPI spec
    spec := loadOpenAPISpec("../zmanim/api/openapi.yaml")

    // Make request
    resp := httpGet(t, "http://localhost:8101/health")

    // Validate response against schema
    validator := validate.NewSchemaValidator(spec, nil, "", strfmt.Default)
    result := validator.Validate(resp.Body)
    assert.True(t, result.IsValid())
}
```

### Test Performance

- Integration tests must complete in <30 seconds
- Smoke tests must complete in <60 seconds
- Use parallel test execution where possible
- Use test fixtures to minimize setup time

### Cross-Repository Integration Tests

**Test Location:** `shtetl/tests/integration-cross-repo/`

**Test Example:**
```go
// tests/integration-cross-repo/api_web_test.go
func TestCreateShulInAPIVisibleInWeb(t *testing.T) {
    // Get API URLs from environment
    apiURL := os.Getenv("API_BASE_URL") // dev-candidate endpoint
    webURL := os.Getenv("WEB_BASE_URL")

    // Create shul via API
    shulPayload := map[string]interface{}{
        "name": "Test Shul",
        "timezone": "America/New_York",
    }

    resp, err := http.Post(apiURL+"/shul/create", "application/json", jsonPayload(shulPayload))
    require.NoError(t, err)
    require.Equal(t, 201, resp.StatusCode)

    var shul Shul
    json.NewDecoder(resp.Body).Decode(&shul)

    // Verify shul visible in Web (via its API call)
    webResp, err := http.Get(webURL + "/api/shuls/" + shul.ID)
    require.NoError(t, err)
    require.Equal(t, 200, webResp.StatusCode)

    var webShul Shul
    json.NewDecoder(webResp.Body).Decode(&webShul)
    assert.Equal(t, shul.Name, webShul.Name)
}
```

**Test Execution:**
```bash
# Set URLs to dev-candidate endpoints (before promotion)
export API_BASE_URL=https://api-dev-candidate.shtetl.com
export WEB_BASE_URL=https://web-dev-candidate.shtetl.com

# Run cross-repo tests
cd shtetl/tests/integration-cross-repo
go test ./... -v
```

### Schema Compatibility Tests

**Test Location:** `shtetl-api/tests/schema-compatibility/`

**Backwards Compatibility Test Example:**
```go
// tests/schema-compatibility/backwards_test.go
func TestBackwardsCompatibility_OldLambdaNewSchema(t *testing.T) {
    // Setup: Deploy OLD Lambda code to test environment
    deployOldLambda(t, "zmanim", "v42")

    // Run NEW migration
    runMigration(t, "000015_add_timezone.up.sql")

    // Test: OLD Lambda should still work
    resp, err := http.Get("http://test-env:8101/shuls")
    require.NoError(t, err)
    require.Equal(t, 200, resp.StatusCode)

    // OLD Lambda ignores new 'timezone' column
    var shuls []Shul
    json.NewDecoder(resp.Body).Decode(&shuls)
    assert.NotEmpty(t, shuls)
}
```

**Cross-Service Schema Access Test:**
```go
// tests/schema-compatibility/cross_service_test.go
func TestCrossServiceSchemaAccess(t *testing.T) {
    // Kehilla service reads from shuls table (owned by Shul service)
    resp, err := http.Get("http://localhost:8105/kehilla/shuls/list")
    require.NoError(t, err)
    require.Equal(t, 200, resp.StatusCode)

    // Verify RLS policy enforces tenant isolation
    var shuls []Shul
    json.NewDecoder(resp.Body).Decode(&shuls)

    // Should only see shuls for current tenant
    for _, shul := range shuls {
        assert.Equal(t, currentTenantID, shul.ShulID)
    }
}
```

### Integration Workflow (Main Repo)

**Workflow File:** `shtetl/.github/workflows/integration-test.yml`

```yaml
name: Cross-Repo Integration Tests

on:
  repository_dispatch:
    types: [service-deployed]

jobs:
  integration-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: false

      - name: Update Submodule Reference
        env:
          SERVICE: ${{ github.event.client_payload.service }}
          COMMIT_SHA: ${{ github.event.client_payload.commit_sha }}
        run: |
          cd submodules/shtetl-api
          git fetch origin
          git checkout $COMMIT_SHA

      - name: Run Cross-Repo Integration Tests
        env:
          API_BASE_URL: ${{ github.event.client_payload.api_url }}
        run: |
          cd tests/integration-cross-repo
          go test ./... -v

      - name: Promote to Dev (on success)
        if: success()
        run: |
          # Update Lambda alias
          aws lambda update-alias \
            --function-name shtetl-dev-${{ github.event.client_payload.service }} \
            --name dev \
            --function-version $LATEST

          # Commit submodule reference
          git add submodules/shtetl-api
          git commit -m "chore: Update shtetl-api to ${{ github.event.client_payload.commit_sha }} [integration-tested]"
          git push origin dev

      - name: Create Failure Issue (on failure)
        if: failure()
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.create({
              owner: context.repo.owner,
              repo: context.repo.repo,
              title: `Integration test failed: ${{ github.event.client_payload.service }} ${{ github.event.client_payload.commit_sha }}`,
              body: `## Integration Test Failure\n\n**Service:** ${{ github.event.client_payload.service }}\n**Commit:** ${{ github.event.client_payload.commit_sha }}\n**Debug URL:** ${{ github.event.client_payload.api_url }}\n\nSee workflow logs for details.`,
              labels: ['integration-failure', 'blocked-deployment']
            })

      - name: Alert Team (on failure)
        if: failure()
        uses: slackapi/slack-github-action@v1
        with:
          payload: |
            {
              "text": "<!channel> Integration tests failed for ${{ github.event.client_payload.service }}",
              "blocks": [
                {
                  "type": "section",
                  "text": {
                    "type": "mrkdwn",
                    "text": ":x: *Integration Test Failure*\n*Service:* ${{ github.event.client_payload.service }}\n*Commit:* `${{ github.event.client_payload.commit_sha }}`\n*Debug:* ${{ github.event.client_payload.api_url }}"
                  }
                }
              ]
            }
```

### Prerequisites

- Story 1.2 (Coder Workspace Template)
- Story 1.3 (CI/CD Pipeline)
- Story 1.6 (API Contract Design - OpenAPI specs)

### Deliverables

**Service-Level Tests (shtetl-api repo):**
- `tests/integration/` - Pre-deployment integration tests (local services)
- `tests/smoke/` - Post-deployment smoke tests (deployed Lambda)
- `tests/schema-compatibility/` - Schema compatibility tests
- `tests/integration/helpers.go` - Test utilities
- `tests/integration/fixtures/` - Test data helpers

**Cross-Repo Tests (shtetl main repo):**
- `tests/integration-cross-repo/` - Multi-service integration tests
- `tests/integration-cross-repo/api_web_test.go` - API + Web tests
- `tests/integration-cross-repo/api_mobile_test.go` - API + Mobile tests

**Workflows:**
- `shtetl-api/.github/workflows/ci-cd.yml` - Updated with integration test trigger
- `shtetl/.github/workflows/integration-test.yml` - Cross-repo integration workflow
- `shtetl/.github/workflows/schema-compatibility.yml` - Schema validation

**Makefile Targets:**
- `make test-integration` - Run service-level integration tests
- `make test-smoke` - Run post-deployment smoke tests
- `make test-integration-cross-repo` - Run cross-repo tests (main repo)
- `make test-schema-compatibility` - Run schema compatibility tests

**Documentation:**
- `tests/README.md` - Testing overview and usage guide
- `tests/integration-cross-repo/README.md` - Cross-repo test writing guide
- `docs/integration-testing-strategy.md` - Architecture and patterns
- `docs/schema-compatibility-guide.md` - Schema testing best practices

### Future Work

- Story 1.10: End-to-end testing with Playwright
- Story 1.11: Load testing with k6
- Story 1.12: Chaos engineering tests
