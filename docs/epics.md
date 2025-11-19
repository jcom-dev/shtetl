# shtetl - Epic Breakdown

**Author:** BMad
**Date:** 2025-11-17
**Project Level:** Medium Complexity
**Target Scale:** Multi-Tenant SaaS

---

## Overview

This document provides the complete epic and story breakdown for shtetl, decomposing the requirements from the [PRD](./prd.md) into implementable stories.

**Living Document Notice:** This is the initial version created from PRD + Domain Brief + Architecture. It will be enhanced if UX Design workflow adds interaction details to stories.

**Epic Strategy:** Optimized for parallel development teams with API scaffolding in Epic 1 enabling simultaneous work across multiple epics.

## Epics Summary

**Epic 1: Development Environment & Project Foundation**
- Zero-friction developer onboarding with Coder workspace
- Complete CI/CD pipeline and infrastructure as code
- All service API scaffolds with mock implementations
- Duration: Foundation for all other work

**Epic 2: Zmanim Provider (Rabbinic Authority Interface)**
- Technical DSL for calendar calculations
- Lightweight formula input with autocomplete
- Calendar stream publishing with audit trails
- Parallel with: Epic 3, 4, 5, 6 (using scaffolded APIs)

**Epic 3: Minyan Scheduling (Shul Admin Interface)**
- Non-technical DSL for schedule configuration
- Tree-based rule system with validation
- 100% coverage guarantee engine
- Parallel with: Epic 2, 4, 5, 6

**Epic 4: Kehilla API & Web Interface**
- Public schedule access API
- Redis caching layer
- Responsive web frontend
- Parallel with: Epic 2, 3, 5, 6

**Epic 5: Kehilla Mobile App**
- React Native mobile app
- Push notification system
- Offline caching
- Parallel with: Epic 2, 3, 4, 6

**Epic 6: PDF Generation & Document Rendering**
- Hebrew RTL PDF generation
- Template system
- Multi-format export
- Can be done last (not blocking)

---

## Functional Requirements Inventory

Based on PRD analysis, here are all 100 functional requirements that must be covered by stories:

### User Account & Access Management (FR1-FR7)
- FR1: Users can create accounts with email or social authentication
- FR2: Users can log in securely and maintain sessions
- FR3: Users can reset passwords via email verification
- FR4: Users can update profile information and preferences
- FR5: Platform administrators can manage user roles and permissions per Shul
- FR6: Users can belong to multiple Shuls with different roles
- FR7: Users can designate a primary Shul for default views

### Zmanim Provider Builder (FR8-FR20)
- FR8: Rabbinic authorities can create new calendar calculation streams
- FR9: Authorities can define daily zmanim calculations using DSL
- FR10: System supports multiple halachic calculation opinions
- FR11: Authorities can define Hebrew calendar dates and events
- FR12: System accounts for geographic parameters
- FR13: Authorities can document calculation methodologies
- FR14: System validates calculations against reference libraries
- FR15: Authorities can publish calendar streams with version control
- FR16: System maintains version history and changelogs
- FR17: Authorities can compare calculation outputs between versions
- FR18: Published streams are discoverable by Shuls
- FR19: System maintains 7-year audit trail of calculation changes
- FR20: Authorities can export calculation streams

### Shul Administration (FR21-FR42)
- FR21: Shul administrators can create new Shul accounts
- FR22: Administrators can select authoritative calendar stream
- FR23: Administrators can apply local offsets to base zmanim
- FR24: Administrators can create minyan types
- FR25: Administrators can create minyan instances
- FR26: Administrators can configure minyan metadata
- FR27: Administrators can create scheduling rules using DSL
- FR28: System provides formula input with autocomplete
- FR29: System auto-suggests calendar primitives
- FR30: Administrators can define custom Shul-specific primitives
- FR31: System enforces primitive inheritance rules
- FR32: System validates schedule coverage in real-time
- FR33: System generates detailed coverage reports
- FR34: System prevents publishing until 100% coverage
- FR35: Administrators can manually override rule priority
- FR36: Administrators can mark minyanim as optional
- FR37: Administrators can preview generated schedules
- FR38: Administrators can generate formatted PDF timetables
- FR39: PDF output matches community-standard layouts
- FR40: Administrators can export schedules to multiple formats
- FR41: System maintains audit trail of schedule changes
- FR42: Administrators can clone minyan configurations

### Hierarchical Primitive System (FR43-FR50)
- FR43: System maintains global calendar primitives
- FR44: System auto-loads country primitives
- FR45: System auto-loads state/province primitives
- FR46: System auto-loads city primitives
- FR47: Primitives cascade with inheritance
- FR48: Lower-level admins can add but not remove inherited primitives
- FR49: Autocomplete displays applicable primitives
- FR50: System validates primitive references in rules

### Congregant Access (FR51-FR64)
- FR51: Congregants can browse available Shuls
- FR52: Congregants can view today's minyan times
- FR53: Congregants can view weekly schedule
- FR54: Congregants can view schedule for specific date
- FR55: Congregants can search for minyanim
- FR56: Congregants can follow multiple Shuls
- FR57: Congregants can subscribe to specific minyanim
- FR58: Congregants can configure notification timing
- FR59: Congregants can choose notification methods
- FR60: Congregants can manage all subscriptions
- FR61: System sends personalized alerts
- FR62: Mobile app caches schedules for offline viewing
- FR63: Congregants can access via web browser
- FR64: Congregants can share schedule links

### Shiur & Event Management (FR65-FR70)
- FR65: Administrators can create shiur entries
- FR66: Administrators can categorize shiurim
- FR67: Administrators can specify shiur metadata
- FR68: Administrators can create communal event entries
- FR69: Congregants can subscribe to shiur categories
- FR70: Congregants can view shiur and event calendars

### Automation & Integration (FR71-FR77)
- FR71: System exposes webhook endpoints
- FR72: System can trigger automation actions via webhook
- FR73: Administrators can configure automation rules
- FR74: System integrates with Clerk for authentication
- FR75: System sends SMS notifications
- FR76: System sends push notifications to mobile apps
- FR77: System sends email notifications

### Multi-Tenancy & Data Isolation (FR78-FR83)
- FR78: System isolates Shul data by tenant
- FR79: Users can only access data for their Shuls
- FR80: Platform administrators can access any Shul for support
- FR81: System validates tenant access on every API request
- FR82: System prevents cross-tenant data leakage
- FR83: Each Shul maintains independent minyan tree configuration

### Mobile Application (FR84-FR93)
- FR84: Mobile app provides iOS support
- FR85: Mobile app provides Android support
- FR86: Mobile app displays today's minyan times on home screen
- FR87: Mobile app supports pull-to-refresh
- FR88: Mobile app provides swipe navigation
- FR89: Mobile app caches schedules for offline viewing
- FR90: Mobile app displays offline indicator
- FR91: Mobile app supports push notifications
- FR92: Mobile app allows in-app subscription management
- FR93: Mobile app supports multiple Shul profiles per user

### System Administration (FR94-FR100)
- FR94: Platform administrators can view all Shuls and users
- FR95: Platform administrators can manage global primitives
- FR96: Platform administrators can access any Shul for support
- FR97: System logs all administrative actions
- FR98: System maintains 7-year retention for audit logs
- FR99: System provides health monitoring and error alerting
- FR100: System supports database backups and disaster recovery

---

## FR Coverage Map

This map shows which epic addresses which functional requirements:

**Epic 1 (Foundation):** Infrastructure for all FRs
- Enables: FR74 (Clerk auth), FR78-FR83 (multi-tenancy), FR94-FR100 (system admin)
- Direct Coverage: FR99, FR100 (monitoring, backups)

**Epic 2 (Zmanim Provider):** FR8-FR20
- Complete coverage of Zmanim Provider Builder requirements
- Rabbinic authority interface and calendar stream publishing

**Epic 3 (Minyan Scheduling):** FR21-FR50, FR65-FR70
- Complete coverage of Shul Admin requirements
- Hierarchical primitive system
- Shiur and event management

**Epic 4 (Kehilla Web):** FR1-FR7, FR51-FR64 (partial), FR74
- User account management via Clerk
- Web-based congregant access
- Shul browsing and searching

**Epic 5 (Kehilla Mobile):** FR51-FR64, FR75-FR77, FR84-FR93
- Complete mobile app requirements
- Notification system (SMS, email, push)
- Subscription management

**Epic 6 (PDF Generation):** FR38-FR40
- PDF rendering with Hebrew RTL
- Multi-format export
- Template system

---

## Epic 1: Development Environment & Project Foundation

**Goal:** Enable any developer to contribute within 30 minutes, with complete CI/CD pipeline and realistic API mocks allowing parallel team development.

**Value Delivered:** Zero-friction onboarding, continuous deployment, unblocked parallel work across all epics.

**FRs Covered:** FR74 (Clerk), FR78-FR83 (multi-tenancy), FR94-FR100 (system admin infrastructure)

---

### Story 1.1: Multi-Repository Structure Setup

**As a** developer,
**I want** a clear multi-repo structure with initialized codebases,
**So that** I can start contributing to the appropriate service immediately.

**Acceptance Criteria:**

**Given** no existing repositories
**When** repositories are created
**Then** the following structure exists:
- `shtetl-api/` with 3 service directories (zmanim, shul, kehilla)
- `shtetl-web/` with Next.js + TypeScript initialized
- `shtetl-mobile/` with Expo + React Native + TypeScript initialized

**And** each Go service has:
- `go.mod` initialized with module path
- Directory structure: `cmd/`, `internal/`, `api/`, `pkg/`
- Basic `main.go` with HTTP server scaffold
- README.md with service description

**And** each frontend repo has:
- Package.json with core dependencies
- TypeScript configured (tsconfig.json)
- Basic folder structure (src/, components/, pages/)
- README.md with setup instructions

**Prerequisites:** None (first story)

**Technical Notes:**
- Go module path: `github.com/shtetl-platform/shtetl-api`
- Use Go 1.25.4, Node.js 24.x LTS
- Follow architecture.md service separation (3 independent services)
- Initialize git repos with .gitignore files

---

### Story 1.2: Coder Workspace Template

**As a** new developer,
**I want** to run one command and have a complete working environment,
**So that** I don't waste hours on environment setup.

**Acceptance Criteria:**

**Given** Coder is installed locally
**When** developer runs `coder create shtetl-dev --template shtetl`
**Then** workspace is created with:
- PostgreSQL 18 running on port 5432
- Redis 8.4 running on port 6379
- Go 1.25.4 installed
- Node.js 24.x LTS installed
- All 3 repos cloned automatically

**And** startup script completes:
- `go mod download` for all Go services
- `npm install` for web and mobile
- Database migrations applied (empty initially)
- All services start successfully

**And** developer can verify:
- Visit http://localhost:8001/health → Zmanim service "ok"
- Visit http://localhost:8002/health → Shul service "ok"
- Visit http://localhost:8003/health → Kehilla service "ok"
- Visit http://localhost:3000 → Web app loads
- Connect to PostgreSQL → database exists

**And** tested on:
- macOS (tested on latest)
- Linux (Ubuntu 22.04)
- Windows WSL2

**Prerequisites:** Story 1.1

**Technical Notes:**
- Create `.coder/shtetl-workspace.tf` template
- Startup script: `.coder/startup.sh`
- Use Docker images: postgres:18-alpine, redis:8.4-alpine
- Environment variables in workspace template
- Record 2-minute demo video showing `coder create` → working environment

---

### Story 1.3: Full CI/CD Pipeline

**As a** developer merging code,
**I want** automated build, test, and deployment to staging,
**So that** changes are validated and deployed without manual steps.

**Acceptance Criteria:**

**Given** code is pushed to GitHub
**When** CI/CD pipeline runs
**Then** GitHub Actions workflow includes:

**Build Stage:**
- Checkout code
- Install Go 1.25.4
- Install Node.js 24.x LTS
- `go build` for all 3 services
- `npm run build` for web (Next.js 16)
- `npx expo export` for mobile (Expo SDK 54)
- Artifacts uploaded

**Test Stage:**
- `go test ./...` for all services
- `npm run test` for web (Vitest)
- Linting: `golangci-lint`, `eslint`, `tsc --noEmit`
- Coverage reports generated
- Tests must pass 100%

**Deploy to Staging Stage:**
- AWS CDK `cdk synth`
- AWS CDK `cdk deploy --require-approval never` (staging stack)
- Deploy to AWS Lambda (all 3 services)
- Deploy web to S3 + CloudFront
- Run smoke tests against staging endpoints

**Deploy to Production Stage:**
- Manual approval required
- AWS CDK deploy (production stack)
- Blue/green deployment
- Rollback capability

**And** AWS CDK infrastructure includes:
- Lambda functions for each service
- API Gateway (REST + HTTP for gRPC over HTTP/2)
- RDS PostgreSQL instance
- ElastiCache Redis
- CloudWatch Logs + Alarms
- S3 bucket + CloudFront for web hosting

**And** pipeline triggers:
- On push to `dev` → deploy to dev environment
- On push to `main` → deploy to staging
- On git tag `v*` → deploy to production (with approval)
- On pull request → build + test only (no deploy)

**Prerequisites:** Story 1.1 (repos), Story 1.2 (Coder workspace)

**Technical Notes:**
- GitHub Actions workflow: `.github/workflows/ci-cd.yml`
- AWS CDK in TypeScript: `infrastructure/` directory
- Use AWS CDK v2
- Secrets: Clerk keys, AWS credentials in GitHub Secrets
- Dev URL: api-dev.shtetl.com
- Staging URL: api-staging.shtetl.com
- Production URL: api.shtetl.com

---

### Story 1.4: API Contract Design (All 3 Services)

**As a** frontend or backend developer,
**I want** complete API specifications before implementation starts,
**So that** I can build against stable contracts without surprises.

**Acceptance Criteria:**

**Given** service architecture is defined
**When** API contracts are designed
**Then** Zmanim Service has:
- `api/zmanim/v1/zmanim.proto` (gRPC)
- Services defined: GetCalendarStream, CalculateZmanim, PublishStream
- Message types: CalendarStream, ZmanimRequest, ZmanimResponse
- Comments documenting each field and service

**And** Shul Service has:
- `api/shul/v1/openapi.yaml` (REST)
- Endpoints: /shuls, /minyanim, /rules, /primitives, /validate-coverage
- Request/response schemas for all endpoints
- Error response formats defined

**And** Kehilla Service has:
- `api/kehilla/v1/openapi.yaml` (REST)
- Endpoints: /schedules, /subscriptions, /shuls
- Request/response schemas
- Query parameters documented

**And** contracts are approved by:
- Epic 2 team (reviews Zmanim gRPC)
- Epic 3 team (reviews Shul REST)
- Epic 4 team (reviews Kehilla REST for web)
- Epic 5 team (reviews Kehilla REST for mobile)

**And** documentation includes:
- Authentication requirements (JWT in header)
- Tenant isolation (shul_id in requests)
- Pagination patterns
- Error code catalog

**Prerequisites:** Story 1.1

**Technical Notes:**
- Use OpenAPI 3.1 spec
- Use protobuf3 for gRPC
- Generate TypeScript types from OpenAPI (openapi-typescript)
- Generate Go types from proto (protoc-gen-go)
- Store specs in dedicated `api/` directory in each service

---

### Story 1.5: Domain Sample Data Fixtures

**As a** developer building UI or testing APIs,
**I want** realistic sample data based on actual Shul schedules,
**So that** I can test with domain complexity, not toy examples.

**Acceptance Criteria:**

**Given** architecture and domain brief reference Beis Mordechai
**When** sample data fixtures are created
**Then** fixtures include:

**Zmanim Stream Sample:**
- Calendar stream: "Rabbi Cohen Manchester 5786"
- Daily zmanim for Manchester (lat: 53.48, lon: -2.24)
- Date range: 2025-01-01 to 2025-12-31
- Fields: alot, netz, sof_zman_shma_gra, sof_zman_tefillah, chatzot, mincha_gedola, mincha_ketana, plag_hamincha, shkiah, tzait_gra, tzait_rt
- Format: JSON with ISO 8601 timestamps

**Minyan Tree Sample (Beis Mordechai):**
- 3 Minyan Types: Shacharit, Mincha, Maariv
- 8 Minyan Instances:
  - Shacharit: First Minyan (Ezrat Nashim), Second Minyan (Main), Third Minyan (Beit Midrash)
  - Mincha: First Minyan, Second Minyan
  - Maariv: First Minyan, Second Minyan, Third Minyan
- 25+ scheduling rules covering:
  - Weekdays, Sundays, Rosh Chodesh, Yom Tov, Fast Days, Chanukah, Selichot
  - Conditional times (RoshChodesh AND Sunday)
- Format: JSON minyan tree structure

**Primitive Samples:**
- Global: RoshChodesh, YomTov, FastDays, Chanukah, Purim, SpecialShabbatot
- Country (UK): BankHolidays, QueensBirthday
- City (Manchester): LocalEvents
- Shul (Beis Mordechai): ShulAnniversary, RabbiYahrtzeit

**Prerequisites:** Story 1.4 (needs data model from contracts)

**Technical Notes:**
- Store in `fixtures/` directory
- JSON format for easy consumption
- Include JSDoc/comments explaining domain concepts
- Validate against OpenAPI schemas
- Reference: docs/domain-brief.md for Beis Mordechai details

---

### Story 1.6: Mock API Servers (Realistic Responses)

**As a** frontend developer,
**I want** mock API servers returning realistic data,
**So that** I can build and test UI before real backends are ready.

**Acceptance Criteria:**

**Given** API contracts and sample data exist
**When** mock servers are implemented
**Then** Zmanim Service mock:
- Listens on port 8001 (gRPC)
- `GetCalendarStream()` returns Rabbi Cohen Manchester stream
- `CalculateZmanim(date, location)` returns zmanim from fixtures
- Responses match proto definitions exactly

**And** Shul Service mock:
- Listens on port 8002 (REST)
- `GET /api/v1/shuls/beis-mordechai` returns sample shul
- `GET /api/v1/shuls/beis-mordechai/minyanim` returns minyan tree
- `POST /api/v1/minyanim/{id}/validate-coverage` returns 100% coverage
- Responses match OpenAPI schemas

**And** Kehilla Service mock:
- Listens on port 8003 (REST)
- `GET /api/v1/schedules/{shulId}/today` returns today's schedule
- `GET /api/v1/schedules/{shulId}/week` returns weekly schedule
- Data includes Hebrew dates (1 Kislev 5786, etc.)
- Responses match OpenAPI schemas

**And** mock data quality:
- Uses actual Beis Mordechai schedule structure
- Times change based on date parameter
- Hebrew calendar dates are accurate
- Multi-tenant: Accepts any shul_id, returns fixture with that ID

**Prerequisites:** Story 1.5 (sample data), Story 1.4 (contracts)

**Technical Notes:**
- Implement in Go using same framework as real services
- Load fixtures from JSON files
- Add delay simulation (50ms) for realistic latency
- Include OpenAPI UI (Swagger) for manual testing at :8002/docs, :8003/docs
- Mock servers run in Coder workspace automatically

---

### Story 1.7: Shared Primitive System Architecture

**As a** developer working on Epic 2 or Epic 3,
**I want** a clear design for the primitive system,
**So that** we don't conflict on database schema or resolution logic.

**Acceptance Criteria:**

**Given** both Zmanim and Minyan services use primitives
**When** architecture is designed
**Then** design document includes:

**Database Schema:**
```sql
CREATE TABLE primitives (
  id UUID PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  type VARCHAR(20) NOT NULL, -- 'global', 'country', 'state', 'city', 'shul'
  scope_id VARCHAR(100),     -- country_code, state_code, city_id, shul_id (NULL for global)
  definition JSONB,          -- Flexible primitive data
  inheritable BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE primitive_inheritance (
  id UUID PRIMARY KEY,
  primitive_id UUID REFERENCES primitives(id),
  inherits_from_id UUID REFERENCES primitives(id)
);
```

**And** resolution algorithm specified:
1. Load global primitives (type='global')
2. Load country primitives (scope_id=shul.country)
3. Load state primitives (scope_id=shul.state)
4. Load city primitives (scope_id=shul.city)
5. Load shul primitives (scope_id=shul.id)
6. Merge with inheritance (lower levels cannot remove inherited)

**And** API ownership:
- Shul Service: Owns CRUD operations for primitives table
- Zmanim Service: Reads primitives via gRPC call to Shul Service
- Epic 3 team: Implements primitive CRUD endpoints
- Epic 2 team: Consumes primitive data

**And** documented:
- ER diagram showing relationships
- Sequence diagram for resolution
- Ownership matrix (who can modify what)
- Migration script creating tables

**Prerequisites:** Story 1.4 (service boundaries defined)

**Technical Notes:**
- Store design in `docs/architecture/primitives.md`
- Include sample queries
- Note: Primitives are multi-tenant (shul_id filter)
- Reference: architecture.md "Pattern 2: Hierarchical Primitive Cascade"

---

### Story 1.8: Formula Input Component with Autocomplete

**As a** developer implementing Epic 2 or Epic 3,
**I want** a reusable formula input component with autocomplete,
**So that** both DSLs share consistent UX without duplicating code.

**Acceptance Criteria:**

**Given** both DSLs need single-line formula input with autocomplete
**When** component is created
**Then** React component exists at `shtetl-web/src/components/FormulaInput.tsx`

**And** component provides:
- Props: `value`, `onChange`, `getSuggestions`, `onValidate`, `placeholder`
- Autocomplete dropdown using Downshift `useCombobox` hook
- Keyboard navigation (Arrow keys, Enter to select, Escape to close)
- Fuzzy matching for suggestions
- Inline validation with visual feedback (red border for errors)
- Theme support (light/dark)

**And** component is documented:
- TypeScript interface for props
- Example usage in Storybook
- Guide for creating custom suggestion providers

**And** tested:
- Renders without errors
- onChange fires on text edit
- Autocomplete dropdown appears on typing
- Keyboard navigation works correctly
- Works in both light and dark theme

**Prerequisites:** Story 1.1 (web repo exists)

**Technical Notes:**
- Use `downshift` package with `useCombobox` hook
- Use `@floating-ui/react` for dropdown positioning
- Export reusable `SuggestionProvider` interface
- Store in shared components directory
- Epic 2 will add Zmanim suggestion provider
- Epic 3 will add Minyan scheduling suggestion provider
- Lightweight alternative to Monaco (~14KB vs ~2MB)

---

### Story 1.9: Database Schema Design (Multi-Tenant)

**As a** developer implementing data models,
**I want** complete database schema with multi-tenancy patterns,
**So that** all services have consistent tenant isolation.

**Acceptance Criteria:**

**Given** multi-tenant architecture requirements
**When** database schema is designed
**Then** schema includes:

**Zmanim Service Tables:**
- `calendar_streams` (stream_id, author_id, methodology, version, published_at)
- `zmanim_calculations` (stream_id, date, location_id, zmanim JSONB)
- `calculation_formulas` (stream_id, formula_name, dsl_code TEXT, version)
- `audit_logs_zmanim` (7-year retention, immutable)

**Shul Service Tables:**
- `shuls` (shul_id UUID, name, country, state, city, location POINT)
- `minyan_types` (shul_id, type_id, name, display_order)
- `minyan_instances` (shul_id, instance_id, type_id, name, location, optional BOOL)
- `minyan_rules` (shul_id, instance_id, rule_id, dsl_code TEXT, priority INT)
- `primitives` (see Story 1.6)
- `shiurim` (shul_id, shiur_id, title, category, speaker, schedule)

**Kehilla Service Tables:**
- `subscriptions` (user_id, shul_id, minyan_id, notification_config JSONB)
- `notification_queue` (queue_id, user_id, message, status, scheduled_at)
- `user_preferences` (user_id, shul_id, preferences JSONB)

**And** all tables have:
- Multi-tenant key (shul_id) on relevant tables
- Indexes on shul_id for query performance
- created_at, updated_at timestamps
- Soft delete support (deleted_at)

**And** GORM models created:
- Go structs with GORM tags
- Migration functions (AutoMigrate)
- Repository interfaces

**And** deliverables:
- ER diagram (draw.io or Mermaid)
- Migration scripts (numbered: 001_initial_schema.sql)
- GORM model files in each service

**Prerequisites:** Story 1.7 (primitive schema), Story 1.4 (data models from API)

**Technical Notes:**
- Use PostgreSQL 18 features (JSONB, POINT type, improved I/O subsystem)
- All UUIDs use uuid-ossp extension
- Indexes: (shul_id, created_at) for pagination
- GORM v2 (latest stable) with pgx driver
- Store schema docs in `docs/architecture/database.md`

---

### Story 1.10: Authentication with Clerk

**As a** developer implementing protected endpoints,
**I want** Clerk authentication integrated in all services,
**So that** user identity and roles are available in every request.

**Acceptance Criteria:**

**Given** Clerk is the authentication provider
**When** integration is complete
**Then** all 3 Go services have:
- Clerk Go SDK v2 installed
- JWT validation middleware
- User metadata extraction (shul_id, role)
- Request context includes: UserID, ShulID, Role

**And** authentication flow works:
- Frontend: Clerk Next.js component loads
- User signs in: Google or email/password
- JWT token included in API requests (Authorization header)
- Backend: Validates token, extracts claims
- Backend: Populates context with user info

**And** user metadata structure:
```json
{
  "shul_id": "uuid",
  "role": "shul_admin|kehilla|rabbinic_authority|platform_admin",
  "primary_shul_id": "uuid"
}
```

**And** role-based access:
- Middleware checks role for protected endpoints
- platform_admin can access all tenants
- shul_admin can only access their shul_id
- kehilla can only read, not write

**And** test users created in Clerk:
- admin@shtetl.com (platform_admin)
- gabbai@beismordechai.com (shul_admin, shul_id=beis-mordechai)
- user@beismordechai.com (kehilla, shul_id=beis-mordechai)

**Prerequisites:** Story 1.1 (services exist), Story 1.9 (user data model)

**Technical Notes:**
- Install `github.com/clerk/clerk-sdk-go/v2`
- Install `@clerk/nextjs` in web
- Install `@clerk/clerk-expo` in mobile
- Middleware: Verify JWT on every request except /health
- Store Clerk keys in environment variables
- Reference: PRD FR74, architecture.md auth section

---

### Story 1.11: Monitoring & Observability

**As a** developer debugging production issues,
**I want** structured logging and health monitoring,
**So that** I can quickly identify and fix problems.

**Acceptance Criteria:**

**Given** services are deployed
**When** monitoring is configured
**Then** all Go services:
- Use zerolog for structured JSON logging
- Log level: DEBUG (local), INFO (staging), WARN (production)
- Every log includes: service, timestamp, level, request_id, shul_id, user_id
- HTTP requests logged with: method, path, status, duration

**And** health check endpoints:
- `GET /health` returns 200 OK with:
  ```json
  {
    "status": "healthy",
    "service": "zmanim",
    "version": "0.1.0",
    "database": "connected",
    "redis": "connected"
  }
  ```
- Returns 503 if database or Redis unavailable

**And** CloudWatch integration:
- All logs stream to CloudWatch Logs
- Log groups: /aws/lambda/shtetl-zmanim, /aws/lambda/shtetl-shul, /aws/lambda/shtetl-kehilla
- Retention: 30 days

**And** CloudWatch Alarms:
- Lambda errors > 10/minute → alert
- API Gateway 5xx responses > 5% → alert
- RDS CPU > 80% → alert
- Lambda duration > 10s → alert

**And** metrics dashboard:
- Request count per service
- Error rate percentage
- P50, P95, P99 latency
- Database connection pool usage

**Prerequisites:** Story 1.3 (CI/CD deployed to AWS)

**Technical Notes:**
- Install `github.com/rs/zerolog`
- Middleware: Generate request_id (UUID) for distributed tracing
- CloudWatch dashboard: `infrastructure/dashboards/main.json`
- Alarm notifications: SNS topic → email (platform admin)
- Future: Add distributed tracing (OpenTelemetry)

---

### Story 1.12: Testing Framework Setup

**As a** developer writing tests,
**I want** complete testing infrastructure,
**So that** I can write unit, integration, and contract tests easily.

**Acceptance Criteria:**

**Given** code needs testing
**When** testing framework is configured
**Then** Go services have:
- Standard `testing` package
- `github.com/stretchr/testify` for assertions and mocking
- Table-driven test examples
- Test database setup/teardown helpers
- Mock interfaces for repositories

**And** web has:
- Vitest configured
- React Testing Library
- Sample component test
- Coverage threshold: 80%

**And** test structure:
```
services/zmanim/
  internal/
    domain/
      calculator_test.go
      calculator_mock.go
  api/
    integration_test.go
```

**And** test commands:
- `go test ./...` (runs all Go tests)
- `go test -race ./...` (detects race conditions)
- `go test -cover ./...` (shows coverage)
- `npm run test` (runs Vitest)
- `npm run test:coverage` (coverage report)

**And** CI integration:
- Tests run on every commit
- Coverage reports uploaded
- Failed tests block merge

**And** sample tests provided:
- Unit test: Pure function test
- Integration test: Database test with test containers
- Mock test: Service with mocked repository

**Prerequisites:** Story 1.1 (code structure), Story 1.9 (database)

**Technical Notes:**
- Use `testing.T` and `testing.M` for Go
- Test database: Use Docker testcontainers or in-memory PostgreSQL
- Fixtures: Load from `fixtures/` for integration tests
- Parallel test execution: `t.Parallel()`
- Reference: NFR-MAINT-1 (80% coverage), NFR-MAINT-2 (95% for critical paths)

---

## Epic 1 Summary

**Total Stories:** 12
**Dependencies:** Linear flow (most stories build on previous)
**Estimated Duration:** Foundation for all work (2-3 weeks)
**Teams Unblocked:** All parallel epics can start after Epic 1 completes

**Critical Path:**
- Story 1.3 (CI/CD) enables continuous delivery for all teams
- Story 1.4 (API contracts) blocks all frontend work
- Story 1.6 (mocks) unblocks Epic 4 & 5 immediately

**Key Deliverables:**
- ✅ Developer can contribute in 30 minutes (`coder create`)
- ✅ All APIs have contracts approved by consuming teams
- ✅ Frontend teams can build against realistic mocks
- ✅ Every commit auto-deploys to staging
- ✅ Monitoring shows service health in production

---

## Summary

**Epic Breakdown Status:** Epic 1 Complete (Epics 2-6 to be added later)

**Epic 1: Development Environment & Project Foundation**
- **Stories:** 12 detailed stories with BDD acceptance criteria
- **FRs Covered:** FR74, FR78-FR83, FR94-FR100
- **Value Delivered:** Zero-friction developer onboarding, full CI/CD, realistic API mocks enabling parallel team development
- **Key Deliverables:**
  - Developer can contribute within 30 minutes via `coder create`
  - All API contracts approved by consuming teams
  - Frontend teams can build against realistic mocks immediately
  - Every commit auto-deploys to dev/staging/production
  - Complete monitoring and observability infrastructure

**Technology Stack (Latest Stable Versions):**
- Go 1.25.4
- Node.js 24.x LTS
- Next.js 16
- PostgreSQL 18
- Redis 8.4
- Expo SDK 54
- GORM v2

**Next Steps:**
When ready to continue, the remaining epics will be:
- Epic 2: Zmanim Provider (Rabbinic Authority Interface)
- Epic 3: Minyan Scheduling (Shul Admin Interface)
- Epic 4: Kehilla API & Web Interface
- Epic 5: Kehilla Mobile App
- Epic 6: PDF Generation & Document Rendering

**FR Coverage:** All 100 functional requirements from PRD mapped to epics. Epic 1 establishes the foundation enabling all parallel development work.

---

_This epic breakdown was created using the BMad Method with First Principles and Pre-mortem Analysis applied to ensure robust, production-ready planning._

