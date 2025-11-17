# Shtetl - Architecture Document

## Executive Summary

Shtetl's architecture implements a multi-tenant SaaS platform with three independent microservices, supporting dual domain-specific languages (DSLs) for zmanim calculation and minyan scheduling. The system uses a multi-repository approach with Go backends, React/TypeScript web frontends, and React Native mobile apps, all designed for portability and vendor-neutrality while maintaining strict halachic accuracy requirements.

## Project Initialization

### Prerequisites (One-Time Setup)

Before creating repositories, initialize the foundational tooling:

**1. Install BMAD v6** (Already completed for this project)
```bash
# BMAD v6 is already installed at .bmad/
# Configuration: .bmad/bmm/config.yaml
```

**2. Setup Coder Locally**
```bash
# Install Coder
curl -fsSL https://coder.com/install.sh | sh

# Start Coder server
coder server --address 0.0.0.0:3000

# Access at http://localhost:3000
```

**3. Configure Claude Code with MCP Servers**
```json
// ~/.config/claude/config.json or Claude Desktop settings
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"]
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"]
    },
    "git": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-git"]
    }
  }
}
```

### Repository Setup (via Coder Workspace)

**Create Coder Workspace Template First:**
```bash
# In your project root, create .coder/ directory with workspace template
mkdir -p .coder
# Add shtetl-workspace.tf (see template in "Coder Workspace Template" section above)
```

**Initialize Repositories via Coder:**
```bash
# Launch Coder workspace
coder create shtetl-dev --template shtetl

# Inside workspace, repositories are auto-cloned and configured
# The startup script handles:
# - Go module initialization
# - Vite + React + TypeScript setup
# - Expo + TypeScript setup
# - npm/go dependency installation
# - PostgreSQL + Redis startup
# - BMAD workflow initialization
```

**Manual Repository Setup (Fallback - if not using Coder):**

**Backend Repository: `shtetl-api`**
```bash
# Initialize Go module
mkdir shtetl-api && cd shtetl-api
go mod init github.com/yourusername/shtetl-api
```

**Web Frontend Repository: `shtetl-web`**
```bash
# Initialize with Vite + React + TypeScript
npm create vite@latest shtetl-web -- --template react-ts
cd shtetl-web
npm install
npm install @monaco-editor/react
npm install @clerk/clerk-react
```

**Mobile Repository: `shtetl-mobile`**
```bash
# Initialize with Expo + TypeScript
npx create-expo-app@latest shtetl-mobile --template blank-typescript
cd shtetl-mobile
npm install @clerk/clerk-expo
```

**⚠️ Note:** The Coder workspace template automates all of the above. These manual commands are documented for reference only - you should use Coder for actual development.

## Decision Summary

| Category | Decision | Version | Rationale |
| -------- | -------- | ------- | --------- |
| Backend Language | Go | 1.25.4 | Strong typing (AI-friendly), excellent performance, great for DSL parsers, explicit error handling |
| Frontend Framework | React | 19.2 | Component reusability, Monaco editor integration, large ecosystem |
| Build Tool (Web) | Vite | 7.2 | Fast dev server, modern bundling, excellent TypeScript support, ESM-only |
| Mobile Framework | React Native (Expo) | SDK 54 | Cross-platform, rapid development, easier deployment via EAS, React Native 0.81 |
| Primary Database | PostgreSQL | 18 | Relational integrity, JSONB for flexibility, improved I/O subsystem, excellent for multi-tenancy |
| Caching Layer | Redis | 7.4 | Session storage, schedule caching, rate limiting |
| ORM | GORM | v2 (latest) | Productivity for common queries, raw SQL for complex operations |
| Authentication | Clerk | latest | Organization management for Shuls, pre-built React components, strong Go SDK |
| Logging | zerolog | latest | Structured JSON logging, excellent performance, simple API |
| API Pattern (External) | REST | - | Simple, widely understood, works everywhere (mobile, web, automation) |
| API Pattern (Internal) | gRPC | - | High performance service-to-service communication, type safety |
| DSL Parser | Go PEG (TBD) | - | participle or pigeon library for custom DSL parsing |
| PDF Generation | React-PDF | latest | Hebrew RTL support, React component-based |
| Code Editor | Monaco Editor | latest | VS Code engine, custom language support, autocomplete |
| Dev Environment | Coder (local) | latest | Standardized workspaces, zero config drift, AI-optimized |
| Infrastructure as Code | AWS CDK | latest | TypeScript, type safety, AWS-native, better than Terraform for AWS-only |
| CI/CD | GitHub Actions | - | Free for open source, integrated with GitHub, easy CDK deployment |

## Technology Stack Details

### Core Technologies

**Backend Services (Go 1.25.4):**
- **Framework:** Standard library + gorilla/mux or fiber for REST
- **gRPC:** google.golang.org/grpc
- **Database:** GORM v2 with PostgreSQL driver (uses pgx v5)
- **Cache:** go-redis/redis
- **Auth:** github.com/clerk/clerk-sdk-go/v2
- **Logging:** github.com/rs/zerolog
- **Testing:** Standard testing package + testify

**Web Frontend (React 19.2 + TypeScript):**
- **Build Tool:** Vite
- **Editor:** @monaco-editor/react
- **Auth:** @clerk/clerk-react
- **HTTP Client:** fetch API or axios
- **State Management:** React Context + hooks (expand to Zustand if needed)
- **Styling:** TailwindCSS (to be added)
- **PDF Preview:** react-pdf for viewing generated PDFs

**Mobile (React Native + Expo):**
- **Navigation:** Expo Router
- **Auth:** @clerk/clerk-expo
- **HTTP Client:** fetch API
- **State Management:** React Context + hooks

**Infrastructure:**
- **Development:** Local Coder (Docker) - $0 cost
- **Production Database:** PostgreSQL 18 (AWS RDS)
- **Production Cache:** Redis 7.4 (AWS ElastiCache)
- **Production Deployment:** AWS Serverless (Lambda + API Gateway)
- **Containerization:** Docker for local development
- **IaC:** AWS CDK (TypeScript) for all AWS resource provisioning
- **CI/CD:** GitHub Actions for automated deployment 

### Multi-Tenant Strategy

- **Single Clerk Organization:** One Clerk organization for the entire Shtetl platform
- **User Metadata:** `shul_id` and `role` stored in Clerk user metadata
- **Data Isolation:** PostgreSQL with `shul_id` tenant identifier on all relevant tables
- **Row-Level Security:** Application-level enforcement via GORM scopes + JWT claims validation
- **User Roles (per shul):**
  - `platform_admin` - Platform administrator (global access)
  - `rabbinic_authority` - Zmanim publishers (can publish calendar streams)
  - `shul_admin` - Shul administrators/gaboim (manage their shul's minyanim)
  - `kehilla` - Community members (read-only access to their shul's schedules)

**Note:** "Kehilla" means "community" in Hebrew - refers to regular shul members who view schedules via mobile/web apps.

## Service Architecture

### Three-Service Design

```
┌─────────────────────┐
│  Zmanim Service     │ Port 8001 (gRPC)
│  - Calculations     │
│  - Calendar Streams │
└──────────┬──────────┘
           │ gRPC
           ↓
┌─────────────────────┐
│  Shul Service       │ Port 8002 (REST + gRPC)
│  - Shul Admin       │
│  - Scheduling       │
│  - PDF Generation   │
└──────────┬──────────┘
           │ Reads schedules
           ↓
┌─────────────────────┐
│  Kehilla Service    │ Port 8003 (REST)
│  - Public API       │
│  - Subscriptions    │
│  - Notifications    │
└─────────────────────┘
```

**Service 1: Zmanim Service**
- **Purpose:** Zmanim/calendar calculation engine for rabbinic authorities
- **Technology:** Go + gRPC
- **Database:** PostgreSQL (calculation formulas, streams, versions)
- **Key Capabilities:**
  - Zmanim DSL parsing and execution
  - Astronomical calculations (alot, netz, shkiah, etc.)
  - Hebrew calendar calculations (holidays, fast days, Rosh Chodesh)
  - Calendar stream publishing with version control
  - Audit trails for calculation changes (7-year retention)
- **Consumers:** Shul Service (via gRPC)

**Service 2: Shul Service**
- **Purpose:** Shul administration and minyan scheduling
- **Technology:** Go + REST + gRPC
- **Database:** PostgreSQL (shuls, minyanim, rules, primitives)
- **Key Capabilities:**
  - Multi-tenant Shul management
  - Minyan scheduling DSL parsing and execution
  - Tree-based rule configuration
  - Coverage validation (ensures 100% schedule coverage)
  - Hierarchical primitive system management
  - Hebrew RTL PDF generation
  - User/gabai management
- **Consumers:** Web admin UI, Kehilla Service

**Service 3: Kehilla Service**
- **Purpose:** Public-facing community API for kehilla members (congregants)
- **Technology:** Go + REST
- **Database:** PostgreSQL (subscriptions, notifications, user preferences)
- **Cache:** Redis (schedule queries, session data)
- **Key Capabilities:**
  - Schedule queries (today's times, weekly view)
  - Subscription management
  - SMS/push notification coordination
  - Alert delivery
  - Automation webhook triggers
- **Consumers:** Mobile app, public web, automation systems
- **Note:** "Kehilla" = community members in Hebrew

### Integration Points

**Zmanim Service → Shul Service:**
- Protocol: gRPC
- Purpose: Shul Service consumes published calendar streams
- Data Flow: Calendar stream ID → Daily zmanim + Hebrew calendar events

**Shul Service → Kehilla Service:**
- Protocol: Database reads (eventually gRPC or event-based)
- Purpose: Kehilla reads published schedules
- Data Flow: Shul ID → Minyan schedules with times

**External Integrations:**
- **Clerk API:** User authentication and organization management
- **SMS Provider:** (TBD - Twilio or similar)
- **Push Notifications:** (TBD - FCM for mobile)
- **Email:** (TBD - Resend or SendGrid)

## Novel Architectural Patterns

### Pattern 1: Dual DSL System

**Challenge:** Two completely different user audiences require specialized domain-specific languages.

**Solution:** Separate DSL implementations optimized for each domain.

**Zmanim Calculation DSL (Technical Interface):**
```
Purpose: Enable rabbinic authorities to define astronomical and calendar calculations
Users: Technical users with halachic and astronomical knowledge
Complexity: High - supports multiple calculation methodologies, formulas, opinions

Architecture:
  services/zmanim/internal/domain/dsl/
    ├── parser/          # PEG parser for DSL syntax
    ├── validator/       # Validate formula correctness
    ├── executor/        # Execute calculations
    ├── ast/             # Abstract syntax tree
    └── versioning/      # Track formula changes

Key Features:
  - Support for multiple halachic opinions (GRA, MGA, etc.)
  - Degree-based and time-based calculations
  - Geographic precision (latitude, longitude, elevation)
  - Astronomical algorithms (sunrise, sunset, twilight)
  - Hebrew calendar date calculations
  - Version control and change tracking
  - Validation against reference data (KosherJava, Hebcal)

DSL Syntax: To be determined through domain research with rabbinic authorities
```

**Minyan Scheduling DSL (Non-Technical Interface):**
```
Purpose: Enable gaboim to define minyan schedules using calendar primitives
Users: Non-technical synagogue administrators
Complexity: Medium - must be intuitive yet powerful enough for complex rules

Architecture:
  services/shul/internal/domain/dsl/
    ├── parser/          # PEG parser for scheduling syntax
    ├── validator/       # Coverage validation (100% requirement)
    ├── executor/        # Generate schedules from rules
    ├── tree_builder/    # Convert UI interactions → DSL
    └── primitives/      # Primitive resolution engine

Key Features:
  - Tree-based rule configuration (Minyan Type → Instance → Rules)
  - Conditional logic (when X then time Y)
  - Priority auto-calculation by specificity
  - Real-time coverage validation
  - Primitive-based conditions (RoshChodesh, FastDays, etc.)
  - Visual tree builder generates DSL behind the scenes

DSL Syntax: To be determined through usability testing with gaboim
```

**Critical Design Principles:**
1. **Complete Separation:** No shared parsing infrastructure - different audiences, different needs
2. **DSL Research Required:** Syntax must be validated with actual users before implementation
3. **Monaco Integration:** Both DSLs get custom Monaco language support (syntax highlighting, autocomplete)
4. **Version Control:** All DSL text is stored, versioned, and auditable
5. **Abstraction Layer:** Service architecture supports future DSL syntax changes without breaking APIs

**Implementation Approach:**
- Go PEG parser libraries (participle or pigeon)
- Custom Monaco language definitions
- Real-time validation as users type
- Preview/approval workflow before publishing

### Pattern 2: Hierarchical Primitive Cascade System

**Challenge:** Calendar primitives must cascade from global → regional → local while preventing invalid modifications.

**Solution:** Inherited primitive system with enforcement rules.

**Cascade Hierarchy:**
```
Global Primitives (Platform-controlled, immutable)
  ↓ inherited by
Country Primitives (Auto-selected based on Shul location)
  ↓ inherited by
State/Province Primitives (Auto-selected)
  ↓ inherited by
City Primitives (Auto-selected)
  ↓ inherited by
Shul-Specific Primitives (User-defined)
```

**Data Model:**
```sql
CREATE TABLE primitives (
  id UUID PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  type VARCHAR(20) NOT NULL, -- 'global', 'country', 'state', 'city', 'shul'
  scope_id VARCHAR(100),     -- country_code, state_code, city_id, shul_id
  definition JSONB,          -- Flexible rule definition
  inheritable BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE primitive_inheritance (
  id UUID PRIMARY KEY,
  primitive_id UUID REFERENCES primitives(id),
  inherits_from_id UUID REFERENCES primitives(id),
  UNIQUE(primitive_id, inherits_from_id)
);
```

**Resolution Logic:**
1. Shul registers with location (country, state, city)
2. System auto-loads all applicable inherited primitives
3. Monaco autocomplete shows available primitives in context
4. Shul can add custom primitives (e.g., "Shul Anniversary")
5. Enforcement: Lower levels CANNOT delete inherited primitives

**Examples:**
- **Global:** RoshChodesh, YomTov, FastDays, Chanukah, Purim
- **Country (UK):** BankHolidays, QueensBirthday
- **State (Greater Manchester):** RegionalHolidays
- **City (Manchester):** LocalEvents
- **Shul (Beis Mordechai):** ShulAnniversary, RabbiYahrtzeit

**Implementation:**
```go
// services/shul/internal/domain/primitives/resolver.go

type PrimitiveResolver struct {
    repo repository.PrimitiveRepository
}

func (r *PrimitiveResolver) LoadForShul(shulID string) ([]Primitive, error) {
    shul := r.repo.GetShul(shulID)

    primitives := []Primitive{}

    // Load in order: global → country → state → city → shul
    primitives = append(primitives, r.repo.GetGlobalPrimitives()...)
    primitives = append(primitives, r.repo.GetCountryPrimitives(shul.Country)...)
    primitives = append(primitives, r.repo.GetStatePrimitives(shul.State)...)
    primitives = append(primitives, r.repo.GetCityPrimitives(shul.City)...)
    primitives = append(primitives, r.repo.GetShulPrimitives(shulID)...)

    return primitives, nil
}
```

### Pattern 3: Coverage Validation Engine

**Challenge:** Ensure 100% schedule coverage - every non-optional minyan must have a time for every day in the season.

**Solution:** Real-time validation engine that evaluates all rules against all days.

**Architecture:**
```go
// services/shul/internal/domain/validation/coverage_validator.go

type CoverageValidator struct {
    hebrewCalendar *hebrew.Calendar
    primitiveResolver *primitives.Resolver
}

type ValidationResult struct {
    CoveragePercent float64
    MissingDays     []MissingDay
    Warnings        []string
}

type MissingDay struct {
    Date        time.Time
    MinyanType  string
    MinyanName  string
    Reason      string
}

func (v *CoverageValidator) Validate(
    minyanTree *MinyanTree,
    startDate time.Time,
    endDate time.Time,
) ValidationResult {
    result := ValidationResult{MissingDays: []MissingDay{}}

    // Generate all days in range (account for Hebrew calendar)
    allDays := v.generateDayRange(startDate, endDate)

    // For each non-optional minyan
    for _, minyan := range minyanTree.GetNonOptionalMinyanim() {
        for _, day := range allDays {
            // Evaluate rules in priority order
            matchedRule := v.findMatchingRule(minyan.Rules, day)

            if matchedRule == nil {
                result.MissingDays = append(result.MissingDays, MissingDay{
                    Date:       day,
                    MinyanType: minyan.Type,
                    MinyanName: minyan.Name,
                    Reason:     "No matching rule",
                })
            }
        }
    }

    totalDays := len(allDays) * len(minyanTree.GetNonOptionalMinyanim())
    coveredDays := totalDays - len(result.MissingDays)
    result.CoveragePercent = (float64(coveredDays) / float64(totalDays)) * 100

    return result
}
```

**Real-time UI Integration:**
- Monaco editor integration: Validate on every keystroke
- Visual feedback: "✓ 100% coverage" or "⚠️ 15 days missing times"
- Detailed report: Show which days are missing for which minyanim
- Prevent publishing until 100% coverage achieved

**Rule Priority Algorithm:**
```go
func (v *CoverageValidator) findMatchingRule(rules []Rule, day time.Time) *Rule {
    // Sort rules by specificity (most specific first)
    sortedRules := v.sortBySpecificity(rules)

    for _, rule := range sortedRules {
        if v.evaluateCondition(rule.Condition, day) {
            return &rule
        }
    }

    return nil // No match = coverage gap
}

func (v *CoverageValidator) sortBySpecificity(rules []Rule) []Rule {
    // Specificity score: more conditions = more specific
    // Example: "RoshChodesh AND Sunday" > "RoshChodesh" > "Sunday" > "weekdays"
    sort.Slice(rules, func(i, j int) bool {
        return rules[i].ConditionCount() > rules[j].ConditionCount()
    })
    return rules
}
```

## Project Structure

### Repository 1: shtetl-api (Backend)

```
shtetl-api/
├── services/
│   ├── zmanim/                      # Zmanim Service (Port 8001)
│   │   ├── cmd/
│   │   │   └── main.go              # Service entry point
│   │   ├── internal/
│   │   │   ├── handlers/            # gRPC handlers
│   │   │   │   ├── calculation_handler.go
│   │   │   │   └── stream_handler.go
│   │   │   ├── domain/              # Business logic
│   │   │   │   ├── calculator/      # Astronomical calculations
│   │   │   │   ├── calendar/        # Hebrew calendar logic
│   │   │   │   └── dsl/             # Zmanim DSL parser
│   │   │   ├── models/              # GORM models
│   │   │   │   ├── calculation.go
│   │   │   │   ├── stream.go
│   │   │   │   └── formula.go
│   │   │   └── repository/          # DB access layer
│   │   │       ├── calculation_repo.go
│   │   │       └── stream_repo.go
│   │   ├── proto/                   # gRPC definitions
│   │   │   └── zmanim.proto
│   │   └── Dockerfile
│   │
│   ├── shul/                        # Shul Service (Port 8002)
│   │   ├── cmd/
│   │   │   └── main.go
│   │   ├── internal/
│   │   │   ├── handlers/            # REST + gRPC handlers
│   │   │   │   ├── shul_handler.go
│   │   │   │   ├── minyan_handler.go
│   │   │   │   └── pdf_handler.go
│   │   │   ├── domain/
│   │   │   │   ├── scheduler/       # Minyan scheduling logic
│   │   │   │   ├── validation/      # Coverage validation
│   │   │   │   ├── dsl/             # Scheduling DSL parser
│   │   │   │   ├── pdf/             # PDF generation
│   │   │   │   └── primitives/      # Primitive resolver
│   │   │   ├── models/
│   │   │   │   ├── shul.go
│   │   │   │   ├── minyan.go
│   │   │   │   ├── rule.go
│   │   │   │   └── primitive.go
│   │   │   └── repository/
│   │   │       ├── shul_repo.go
│   │   │       ├── minyan_repo.go
│   │   │       └── primitive_repo.go
│   │   ├── api/                     # REST API specs (OpenAPI)
│   │   ├── proto/                   # gRPC definitions
│   │   │   └── shul.proto
│   │   └── Dockerfile
│   │
│   └── kehilla/                     # Kehilla Service (Port 8003)
│       ├── cmd/
│       │   └── main.go
│       ├── internal/
│       │   ├── handlers/            # REST handlers
│       │   │   ├── schedule_handler.go
│       │   │   ├── subscription_handler.go
│       │   │   └── notification_handler.go
│       │   ├── domain/
│       │   │   ├── schedules/       # Schedule queries
│       │   │   ├── subscriptions/   # Subscription management
│       │   │   └── notifications/   # SMS/push coordination
│       │   ├── models/
│       │   │   ├── subscription.go
│       │   │   └── notification.go
│       │   └── repository/
│       │       ├── subscription_repo.go
│       │       └── notification_repo.go
│       ├── api/                     # REST API specs
│       └── Dockerfile
│
├── pkg/                             # Shared packages
│   ├── auth/                        # Clerk JWT validation
│   │   ├── middleware.go
│   │   └── validator.go
│   ├── errors/                      # APIError types
│   │   └── errors.go
│   ├── logger/                      # Zerolog wrapper
│   │   └── logger.go
│   ├── primitives/                  # Shared primitive types
│   │   └── types.go
│   ├── hebrew/                      # Hebrew calendar utilities
│   │   ├── calendar.go
│   │   └── dates.go
│   └── middleware/                  # Shared middleware
│       ├── cors.go
│       └── request_id.go
│
├── proto/                           # Shared proto definitions
├── migrations/                      # Database migrations
│   ├── 001_initial_schema.up.sql
│   └── 001_initial_schema.down.sql
├── docker-compose.yml               # Local development
├── .env.example
├── go.mod
├── go.sum
└── README.md
```

### Repository 2: shtetl-web (React Frontend)

```
shtetl-web/
├── src/
│   ├── features/
│   │   ├── zmanim-builder/         # Zmanim Engine Builder UI
│   │   │   ├── components/
│   │   │   │   ├── FormulaEditor.tsx
│   │   │   │   ├── StreamPublisher.tsx
│   │   │   │   └── ValidationPanel.tsx
│   │   │   ├── editor/             # Monaco DSL editor
│   │   │   │   ├── ZmanimLanguage.ts
│   │   │   │   └── ZmanimEditor.tsx
│   │   │   └── api/
│   │   │       └── zmanimApi.ts
│   │   │
│   │   └── shul-admin/             # Shul Admin UI
│   │       ├── components/
│   │       │   ├── ShulDashboard.tsx
│   │       │   ├── MinyanManager.tsx
│   │       │   └── PDFPreview.tsx
│   │       ├── editor/             # Monaco DSL editor
│   │       │   ├── SchedulingLanguage.ts
│   │       │   └── SchedulingEditor.tsx
│   │       ├── tree-builder/       # Visual minyan tree UI
│   │       │   ├── MinyanTree.tsx
│   │       │   ├── RuleBuilder.tsx
│   │       │   └── CoverageIndicator.tsx
│   │       └── api/
│   │           └── shulApi.ts
│   │
│   ├── shared/
│   │   ├── components/
│   │   │   ├── Layout.tsx
│   │   │   └── Navigation.tsx
│   │   ├── hooks/
│   │   │   └── useAuth.ts
│   │   ├── api/
│   │   │   └── client.ts          # API client with Clerk auth
│   │   └── utils/
│   │       └── dates.ts
│   │
│   ├── App.tsx
│   └── main.tsx
│
├── public/
├── index.html
├── vite.config.ts
├── tsconfig.json
├── package.json
└── README.md
```

### Repository 3: shtetl-mobile (React Native)

```
shtetl-mobile/
├── src/
│   ├── screens/
│   │   ├── ScheduleScreen.tsx      # Today's minyan times
│   │   ├── WeeklyScreen.tsx        # Weekly schedule view
│   │   ├── SubscriptionsScreen.tsx # Manage alerts
│   │   ├── ShulsScreen.tsx         # Browse/follow shuls
│   │   └── SettingsScreen.tsx      # User preferences
│   │
│   ├── components/
│   │   ├── MinyanCard.tsx
│   │   ├── TimeDisplay.tsx
│   │   └── NotificationToggle.tsx
│   │
│   ├── services/
│   │   └── api/                    # Kehilla API client
│   │       ├── client.ts
│   │       ├── schedules.ts
│   │       └── subscriptions.ts
│   │
│   ├── hooks/
│   │   ├── useSchedule.ts
│   │   └── useSubscriptions.ts
│   │
│   └── navigation/
│       └── AppNavigator.tsx
│
├── app.json
├── package.json
├── tsconfig.json
└── README.md
```

## Data Architecture

### Core Domain Models

**Zmanim Service:**
```sql
-- Calendar streams published by rabbinic authorities
calendar_streams (
  id UUID PRIMARY KEY,
  name VARCHAR(200) NOT NULL,
  authority_id UUID NOT NULL,          -- Clerk user ID
  region VARCHAR(100),
  hebrew_year INT,
  methodology_doc TEXT,                 -- Markdown explaining opinions used
  dsl_source TEXT NOT NULL,             -- Original DSL text
  status VARCHAR(20),                   -- 'draft', 'published', 'archived'
  version INT DEFAULT 1,
  published_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
)

-- Daily zmanim calculated from streams
zmanim_values (
  id UUID PRIMARY KEY,
  stream_id UUID REFERENCES calendar_streams(id),
  date DATE NOT NULL,
  latitude DECIMAL(10, 8),
  longitude DECIMAL(11, 8),
  elevation INT,                        -- meters
  timezone VARCHAR(50),
  alot_hashachar TIMESTAMPTZ,
  misheyakir TIMESTAMPTZ,
  netz TIMESTAMPTZ,
  sof_zman_shma TIMESTAMPTZ,
  sof_zman_tefillah TIMESTAMPTZ,
  chatzot TIMESTAMPTZ,
  mincha_gedolah TIMESTAMPTZ,
  mincha_ketanah TIMESTAMPTZ,
  plag_hamincha TIMESTAMPTZ,
  shkiah TIMESTAMPTZ,
  tzait_hakochavim TIMESTAMPTZ,
  calculated_at TIMESTAMPTZ DEFAULT NOW()
)

-- Hebrew calendar events
hebrew_calendar_events (
  id UUID PRIMARY KEY,
  stream_id UUID REFERENCES calendar_streams(id),
  date DATE NOT NULL,
  hebrew_year INT,
  hebrew_month VARCHAR(20),
  hebrew_day INT,
  event_type VARCHAR(50),               -- 'yom_tov', 'fast_day', 'rosh_chodesh', etc.
  event_name VARCHAR(100),
  is_yom_tov BOOLEAN,
  is_fast_day BOOLEAN,
  created_at TIMESTAMPTZ DEFAULT NOW()
)
```

**Shul Service:**
```sql
-- Multi-tenant shuls
shuls (
  id UUID PRIMARY KEY,
  name VARCHAR(200) NOT NULL,
  slug VARCHAR(100) UNIQUE NOT NULL,    -- URL-friendly identifier
  country VARCHAR(50),
  state VARCHAR(50),
  city VARCHAR(100),
  timezone VARCHAR(50) NOT NULL,
  calendar_stream_id UUID,              -- Selected authoritative stream
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
)

-- User-Shul relationships (users can belong to multiple shuls)
user_shuls (
  id UUID PRIMARY KEY,
  user_id VARCHAR(100) NOT NULL,        -- Clerk user ID
  shul_id UUID REFERENCES shuls(id),
  role VARCHAR(50) NOT NULL,            -- 'shul_admin', 'kehilla'
  is_primary BOOLEAN DEFAULT false,     -- User's primary shul
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(user_id, shul_id)
)
-- Note: role 'kehilla' = community member (formerly 'congregant')

-- Minyan types and instances
minyanim (
  id UUID PRIMARY KEY,
  shul_id UUID REFERENCES shuls(id),
  type VARCHAR(50) NOT NULL,            -- 'shacharit', 'mincha', 'maariv'
  name VARCHAR(100),                    -- 'First Minyan', 'Second Minyan'
  location VARCHAR(100),                -- 'Ezrat Nashim', 'Main Hall'
  is_optional BOOLEAN DEFAULT false,
  display_order INT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
)

-- Scheduling rules (condition → time)
minyan_rules (
  id UUID PRIMARY KEY,
  minyan_id UUID REFERENCES minyanim(id),
  condition_dsl TEXT NOT NULL,          -- DSL expression (e.g., "RoshChodesh AND Sunday")
  time_value TIME,                      -- Static time (e.g., 08:15)
  time_offset_minutes INT,              -- Or offset from zman (e.g., +30 from netz)
  priority INT,                         -- Auto-calculated by specificity
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
)

-- Calendar primitives (hierarchical)
primitives (
  id UUID PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  type VARCHAR(20) NOT NULL,            -- 'global', 'country', 'state', 'city', 'shul'
  scope_id VARCHAR(100),                -- country_code, state_code, city_id, shul_id
  definition JSONB,                     -- Flexible rule definition
  inheritable BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
)

primitive_inheritance (
  id UUID PRIMARY KEY,
  primitive_id UUID REFERENCES primitives(id),
  inherits_from_id UUID REFERENCES primitives(id),
  UNIQUE(primitive_id, inherits_from_id)
)

-- Generated schedules (cached)
generated_schedules (
  id UUID PRIMARY KEY,
  shul_id UUID REFERENCES shuls(id),
  minyan_id UUID REFERENCES minyanim(id),
  date DATE NOT NULL,
  time TIMESTAMPTZ NOT NULL,
  generated_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(minyan_id, date)
)
```

**Kehilla Service:**
```sql
-- Kehilla member subscriptions
-- Note: "Kehilla" = community members in Hebrew
subscriptions (
  id UUID PRIMARY KEY,
  user_id VARCHAR(100) NOT NULL,       -- Clerk user ID (kehilla member)
  shul_id UUID REFERENCES shuls(id),
  minyan_id UUID,                       -- NULL = all minyanim
  notification_type VARCHAR(20),        -- 'sms', 'push', 'email'
  notification_time_offset INT,         -- Minutes before minyan (e.g., -30)
  active BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
)

-- Notification queue
notifications (
  id UUID PRIMARY KEY,
  subscription_id UUID REFERENCES subscriptions(id),
  user_id VARCHAR(100) NOT NULL,       -- Kehilla member
  shul_id UUID,
  minyan_id UUID,
  scheduled_for TIMESTAMPTZ NOT NULL,
  sent_at TIMESTAMPTZ,
  status VARCHAR(20),                   -- 'pending', 'sent', 'failed'
  message_text TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
)
```

### Multi-Tenant Isolation

**Application-Level Row-Level Security:**
```go
// GORM scopes for tenant isolation

func WithShulID(shulID string) func(db *gorm.DB) *gorm.DB {
    return func(db *gorm.DB) *gorm.DB {
        return db.Where("shul_id = ?", shulID)
    }
}

// Usage in repository:
func (r *MinyanRepository) GetByShul(shulID string) ([]Minyan, error) {
    var minyanim []Minyan
    err := r.db.Scopes(WithShulID(shulID)).Find(&minyanim).Error
    return minyanim, err
}
```

## API Contracts

### REST API Design

**Base URL:** `https://api.shtetl.com/api/v1`

**Authentication:** Clerk JWT in `Authorization: Bearer <token>` header

**Standard Response Formats:**

**Success:**
```json
{
  "data": { ... },
  "meta": {
    "timestamp": "2025-11-17T10:30:00Z",
    "version": "v1"
  }
}
```

**Error:**
```json
{
  "error": {
    "code": "SHUL_VALIDATION_COVERAGE_INCOMPLETE",
    "message": "Schedule coverage validation failed: 15 days missing times",
    "details": {
      "coverage_percent": 85.5,
      "missing_days": [...]
    }
  },
  "meta": {
    "timestamp": "2025-11-17T10:30:00Z"
  }
}
```

### Key Endpoints

**Kehilla Service (Public API):**
```
GET    /api/v1/schedules/{shulId}/today
GET    /api/v1/schedules/{shulId}/week
GET    /api/v1/schedules/{shulId}/date/{date}
POST   /api/v1/subscriptions
PUT    /api/v1/subscriptions/{id}
DELETE /api/v1/subscriptions/{id}
GET    /api/v1/shuls
GET    /api/v1/shuls/{id}
```

**Shul Service (Admin API):**
```
GET    /api/v1/shuls/{shulId}
PUT    /api/v1/shuls/{shulId}
GET    /api/v1/shuls/{shulId}/minyanim
POST   /api/v1/shuls/{shulId}/minyanim
PUT    /api/v1/minyanim/{id}
DELETE /api/v1/minyanim/{id}
POST   /api/v1/minyanim/{id}/rules
PUT    /api/v1/rules/{id}
DELETE /api/v1/rules/{id}
POST   /api/v1/minyanim/{id}/validate-coverage
POST   /api/v1/shuls/{shulId}/generate-pdf
GET    /api/v1/primitives
POST   /api/v1/shuls/{shulId}/primitives
```

**Zmanim Service (Internal gRPC):**
```protobuf
service ZmanimService {
  rpc GetCalendarStream(StreamRequest) returns (CalendarStream);
  rpc CalculateZmanim(ZmanimRequest) returns (ZmanimResponse);
  rpc PublishStream(PublishRequest) returns (PublishResponse);
}
```

## Security Architecture

### Authentication & Authorization

**Clerk Integration:**
- **Organizations = Shuls:** Each Shul is a Clerk organization
- **Roles:**
  - `rabbinic_authority` - Can publish zmanim streams
  - `shul_admin` - Can manage Shul and minyanim
  - `kehilla` - Community members, read-only access to schedules

**JWT Validation:**
```go
// pkg/auth/middleware.go

func ClerkAuthMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        token := c.GetHeader("Authorization")

        // Validate with Clerk
        claims, err := clerk.VerifyToken(token)
        if err != nil {
            c.AbortWithStatusJSON(401, errors.Unauthorized("Invalid token"))
            return
        }

        // Extract user metadata from JWT
        metadata := claims.PublicMetadata
        shulID := metadata["shul_id"].(string)
        role := metadata["role"].(string)

        // Add to context
        c.Set("user_id", claims.Subject)
        c.Set("shul_id", shulID)
        c.Set("role", role)
        c.Next()
    }
}
```

**Multi-Tenant Enforcement:**
```go
func RequireShulAccess(requiredShulID string) gin.HandlerFunc {
    return func(c *gin.Context) {
        userShulID := c.GetString("shul_id")
        role := c.GetString("role")

        // Platform admins can access any shul
        if role == "platform_admin" {
            c.Next()
            return
        }

        // Verify user belongs to the requested shul
        if userShulID != requiredShulID {
            c.AbortWithStatusJSON(403, errors.Forbidden("Access denied"))
            return
        }

        c.Next()
    }
}

func RequireRole(allowedRoles ...string) gin.HandlerFunc {
    return func(c *gin.Context) {
        userRole := c.GetString("role")

        for _, role := range allowedRoles {
            if userRole == role {
                c.Next()
                return
            }
        }

        c.AbortWithStatusJSON(403, errors.Forbidden("Insufficient permissions"))
    }
}
```

### Data Protection

- **Encryption at rest:** PostgreSQL encryption (provider-level)
- **Encryption in transit:** TLS/HTTPS for all APIs
- **API rate limiting:** Redis-based rate limiter
- **Audit logging:** All zmanim calculation changes (7-year retention)
- **Input validation:** All API inputs validated before processing
- **SQL injection protection:** GORM parameterized queries

## Performance Considerations

### Caching Strategy

**Redis Caching:**
```
Schedule Queries:
  Key: schedule:{shul_id}:{date}
  TTL: 24 hours
  Invalidation: On minyan rule changes

Calendar Stream Data:
  Key: stream:{stream_id}:{date}
  TTL: 7 days
  Invalidation: On stream republish

Primitive Lists:
  Key: primitives:{shul_id}
  TTL: 1 hour
  Invalidation: On primitive changes
```

**Database Indexing:**
```sql
-- Critical indexes for query performance
CREATE INDEX idx_minyanim_shul ON minyanim(shul_id);
CREATE INDEX idx_schedules_shul_date ON generated_schedules(shul_id, date);
CREATE INDEX idx_zmanim_stream_date ON zmanim_values(stream_id, date);
CREATE INDEX idx_subscriptions_user ON subscriptions(user_id);
```

### Scalability Approach

- **Horizontal scaling:** Stateless services behind load balancer
- **Database:** Read replicas for Kehilla Service queries
- **Caching:** Redis cluster for high availability
- **PDF Generation:** Async job queue (future: BullMQ or similar)
- **Notification delivery:** Message queue for SMS/push (future: SQS or RabbitMQ)

## Deployment Architecture

### Initial Deployment (AWS Serverless)

```
┌─────────────────────────────────────────┐
│         CloudFront (CDN)                │
│   - shtetl-web static assets           │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│      API Gateway (REST + WebSocket)     │
└────┬──────────┬────────────┬────────────┘
     │          │            │
┌────▼─────┐ ┌─▼────────┐ ┌─▼─────────┐
│ Lambda   │ │ Lambda   │ │ Lambda    │
│ Zmanim   │ │ Shul     │ │ Kehilla   │
└────┬─────┘ └─┬────────┘ └─┬─────────┘
     │         │            │
     └─────────┴────────────┘
               │
    ┌──────────▼───────────┐
    │   RDS PostgreSQL     │
    │   ElastiCache Redis  │
    └──────────────────────┘
```

### Future Deployment (Kubernetes Portable)

**Design Principles:**
- All services containerized (Docker)
- No AWS-specific code in business logic
- Abstract cloud services behind interfaces
- Terraform for infrastructure as code
- Helm charts for Kubernetes deployment

**Migration Path:**
```
Phase 1 (MVP): AWS Serverless (Lambda + RDS + ElastiCache)
  ↓
Phase 2: AWS ECS Containers (easier migration path)
  ↓
Phase 3: Kubernetes (self-hosted or GKE/EKS/AKS)
```

## Development Environment & Workflow

### Development Philosophy

**Shtetl is built AI-first from day 1** using the **BMAD Method v6** with **Claude Code** and **Coder** as foundational tools, not optional add-ons.

**Core Principles:**
- **🤖 AI-First Development:** Claude Code is the primary development tool, not a supplement
- **☁️ Cloud Development Environments:** Coder workspaces are the standard development environment from MVP onwards
- **📋 BMAD Workflow Orchestration:** All development phases follow BMAD v6 structured methodology
- **🔌 MCP Deep Integration:** Model Context Protocol connects AI agents to every tool (Git, PostgreSQL, filesystem, etc.)
- **🚀 Fast Onboarding:** New contributors productive in <5 minutes, not hours/days
- **♻️ Reproducible Environments:** Zero configuration drift - every workspace identical

**Why This Matters:**

Traditional development has a "works on my machine" problem. Shtetl eliminates this from day 1 by making **Coder + BMAD + Claude Code** the standard, not optional tooling. Every contributor - whether solo developer, open-source contributor, or future team member - uses identical, pre-configured environments.

This isn't just about convenience - it's about **consistency**. When AI agents implement features using `/bmad:bmm:workflows:dev-story`, they need identical contexts. When humans review code, they need identical environments. Coder + BMAD makes this automatic.

### Development Environment

**Standard Development Setup (Day 1):**

**What is Coder?**

Coder is an open-source platform for self-hosted cloud development environments (CDEs). It provisions standardized, secure, and scalable development workspaces on your own infrastructure.

**For Shtetl, Coder is NOT optional - it's the foundation.**

**Why Coder for Shtetl?**
- ✅ **Standardized environments** - Every developer gets identical setup
- ✅ **Fast onboarding** - New contributors can start coding in minutes, not days
- ✅ **AI-optimized** - Full IDE + AI agents in isolated, ephemeral environments
- ✅ **Cost-effective** - Auto-shutdown idle workspaces, run on Kubernetes or Docker
- ✅ **Self-hosted** - Runs on your infrastructure (AWS, on-premise, or hybrid)
- ✅ **Flexible IDEs** - Works with VS Code, JetBrains, Cursor, or any web-based IDE

**Coder Architecture for Shtetl:**

```
┌─────────────────────────────────────────┐
│  Coder Control Plane (Kubernetes/VM)   │
│  - Workspace provisioning              │
│  - Policy enforcement                  │
│  - IDE management                      │
└────────────┬────────────────────────────┘
             │
             ↓
┌─────────────────────────────────────────┐
│  Developer Workspace (Docker/K8s Pod)   │
│  ├── VS Code / Cursor / JetBrains      │
│  ├── Claude Code + MCP Servers         │
│  ├── Go 1.25.4                         │
│  ├── Node.js 20+                       │
│  ├── PostgreSQL (local or remote)      │
│  ├── Redis (local or remote)           │
│  ├── Git repositories (all 3 repos)    │
│  └── Docker-in-Docker (for testing)    │
└─────────────────────────────────────────┘
```

**Coder Workspace Template (Terraform):**

```hcl
# .coder/shtetl-workspace.tf

terraform {
  required_providers {
    coder = {
      source = "coder/coder"
    }
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}

resource "coder_agent" "main" {
  os   = "linux"
  arch = "amd64"

  startup_script = <<-EOT
    #!/bin/bash

    # Install Go 1.25.4
    wget https://go.dev/dl/go1.25.4.linux-amd64.tar.gz
    sudo tar -C /usr/local -xzf go1.25.4.linux-amd64.tar.gz

    # Install Node.js 20
    curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
    sudo apt-get install -y nodejs

    # Clone repositories
    git clone https://github.com/yourusername/shtetl-api /home/coder/shtetl-api
    git clone https://github.com/yourusername/shtetl-web /home/coder/shtetl-web
    git clone https://github.com/yourusername/shtetl-mobile /home/coder/shtetl-mobile

    # Start local PostgreSQL + Redis
    docker-compose -f /home/coder/shtetl-api/docker-compose.yml up -d

    # Setup environment
    cd /home/coder/shtetl-api && go mod download
    cd /home/coder/shtetl-web && npm install
    cd /home/coder/shtetl-mobile && npm install
  EOT
}

resource "docker_container" "workspace" {
  image = "codercom/enterprise-base:ubuntu"
  name  = "shtetl-workspace-${data.coder_workspace.me.owner}"

  env = [
    "CODER_AGENT_TOKEN=${coder_agent.main.token}",
  ]

  volumes {
    container_path = "/home/coder"
    volume_name    = docker_volume.home.name
  }
}

resource "docker_volume" "home" {
  name = "shtetl-home-${data.coder_workspace.me.owner}"
}
```

**Coder Deployment Options:**

**Setup Coder Locally:**
```bash
# Install Coder
curl -fsSL https://coder.com/install.sh | sh

# Start Coder server
coder server --address 0.0.0.0:3000

# Create workspace from template
coder templates create shtetl --directory .coder/
coder create shtetl-dev --template shtetl
```

**The Coder workspace automatically provisions:**
- Go 1.25.4
- Node.js 20+
- PostgreSQL 17 (local Docker container)
- Redis 7.4 (local Docker container)
- All 3 repositories cloned and configured
- BMAD v6 workflows available
- Claude Code + MCP servers pre-configured

**You access via:**
- Web browser: `http://localhost:3000`
- VS Code: Connect to Coder workspace
- Cursor: Connect to Coder workspace

### AI-Assisted Development with Claude Code & BMAD v6

**BMAD v6 Integration:**

Shtetl development leverages **BMAD (Building Method for AI Development) v6**, which provides:
- Workflow orchestration for AI agents
- Project structure and documentation standards
- Agent personas (PM, Architect, Dev, TEA, etc.)
- Phase-based methodology (Discovery → Planning → Solutioning → Implementation)

**BMAD is already installed in this project:**
```
shtetl/
├── .bmad/                  # BMAD v6 framework
│   ├── bmm/                # Building Method Module
│   │   ├── agents/         # Agent personas
│   │   ├── workflows/      # Workflow definitions
│   │   └── config.yaml     # Project configuration
│   └── core/               # Core BMAD utilities
├── docs/
│   ├── brief.md            # ✅ Product Brief (completed)
│   ├── architecture.md     # ✅ Architecture (completed)
│   └── bmm-workflow-status.yaml  # Workflow tracking
```

**Claude Code + MCP Servers:**

Claude Code operates through the **Model Context Protocol (MCP)**, which enables deep integration with development tools:

**Essential MCP Servers for Shtetl:**

```json
// claude_desktop_config.json or .claud/mcp.json

{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "POSTGRES_CONNECTION_STRING": "postgresql://localhost:5432/shtetl"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem"],
      "args": ["/home/coder/shtetl-api", "/home/coder/shtetl-web"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "git": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-git"]
    }
  }
}
```

**Development Workflow with Claude Code:**

```bash
# 1. Start in BMAD workflow context
/bmad:bmm:workflows:workflow-status  # Check current phase

# 2. Create a story for a feature (using BMAD PM agent)
/bmad:bmm:workflows:create-story

# 3. Implement story with Claude Code (using BMAD Dev agent)
/bmad:bmm:workflows:dev-story

# Claude Code will:
# - Read story acceptance criteria
# - Access architecture document (this file)
# - Query database schema via PostgreSQL MCP
# - Edit code files directly via Filesystem MCP
# - Run tests and validate
# - Commit changes via Git MCP
# - Update story status
```

**Example Claude Code Session:**

```
User: "Implement the minyan scheduling DSL parser according to story-003"

Claude Code:
[Reads: docs/architecture.md - Novel Pattern 1: Dual DSL System]
[Reads: stories/story-003.md - Acceptance criteria]
[Queries: PostgreSQL via MCP - Check minyan_rules schema]
[Edits: services/shul/internal/domain/dsl/parser.go]
[Runs: go test ./services/shul/internal/domain/dsl/...]
[Commits: "feat: implement minyan scheduling DSL parser"]
[Updates: stories/story-003.md status → "Ready for Review"]

✅ Story completed. Coverage validation tests passing.
```

**BMAD Workflow Integration:**

The BMAD v6 workflow system orchestrates AI agents through the development lifecycle:

**Phase 0-2 (Planning & Architecture):**
- `/bmad:bmm:workflows:product-brief` → Create product brief
- `/bmad:bmm:workflows:prd` → Generate PRD from brief
- `/bmad:bmm:workflows:architecture` → ✅ **Already completed (this document)**
- `/bmad:bmm:workflows:create-epics-and-stories` → Break down into stories

**Phase 3 (Implementation):**
- `/bmad:bmm:workflows:sprint-planning` → Initialize sprint tracking
- `/bmad:bmm:workflows:dev-story` → Implement each story with Claude Code
- `/bmad:bmm:workflows:code-review` → AI code review
- `/bmad:bmm:workflows:story-done` → Mark story complete

**Phase 4 (Quality & Delivery):**
- `/bmad:bmm:workflows:test-design` → Testability assessment
- `/bmad:bmm:workflows:implementation-readiness` → Gate check
- `/bmad:bmm:workflows:retrospective` → Learning review

### Development Workflow

**Day 1: Initial Setup**

1. **Install Coder locally:**
   ```bash
   curl -fsSL https://coder.com/install.sh | sh
   coder server --address 0.0.0.0:3000
   ```

2. **Create workspace:**
   ```bash
   coder create shtetl-dev --template shtetl
   ```

3. **Access workspace:**
   - Open `http://localhost:3000` in browser, OR
   - Connect VS Code/Cursor to workspace

4. **Start coding:**
   Everything is pre-configured (Go, Node.js, PostgreSQL, Redis, repos, BMAD)

**Daily Development:**

1. **Start workspace** (if not running)
2. **Use BMAD workflows:**
   ```bash
   /bmad:bmm:workflows:workflow-status  # Check current phase
   /bmad:bmm:workflows:dev-story        # Implement next story
   ```
3. **Claude Code + MCP** available for AI assistance
4. **Test locally** with local PostgreSQL/Redis
5. **Commit and push** to GitHub
6. **CI/CD deploys** to AWS automatically

**Deployment Workflow:**

1. **Develop in Coder** (local, $0 cost)
2. **Push to GitHub**
3. **GitHub Actions triggers:**
   - Runs tests
   - Builds Lambda functions
   - Runs `cdk deploy` to dev/prod
   - AWS resources created/updated automatically

### IDE & Editor Support

**Recommended IDEs (all work with Coder):**
- **VS Code** - Best Claude Code integration, largest extension ecosystem
- **Cursor** - AI-native fork of VS Code, excellent for pair programming with Claude
- **JetBrains GoLand** - Premium Go development experience
- **Neovim** - For terminal purists (requires MCP CLI setup)

**Claude Code works in all environments:**
- Local terminal
- Coder web terminal
- VS Code integrated terminal
- SSH into remote Coder workspace

### Cost & Infrastructure Considerations

**Local Development:**
- Cost: $0 (uses your machine)
- Setup time: 2-4 hours first time
- Maintenance: Manual updates, configuration drift

**Development (Local Coder):**
- Cost: $0 (runs on your machine via Docker)
- Setup time: 30 minutes first time
- Maintenance: Minimal, consistent environments

**Deployment (AWS via CDK):**
- **Dev Environment:** ~$40-60/month (smaller instances for testing)
- **Production Environment:** ~$60-100/month (production-grade instances)
- Setup time: 30-60 minutes first deployment via `cdk deploy`
- Maintenance: Automated via CI/CD (GitHub Actions)

### Infrastructure as Code (AWS CDK)

**AWS resources are defined using AWS CDK (TypeScript):**

All AWS infrastructure is defined in code and version-controlled:

```
infrastructure/
├── cdk/
│   ├── bin/
│   │   └── shtetl.ts        # CDK app entry point
│   ├── lib/
│   │   ├── database-stack.ts      # RDS PostgreSQL
│   │   ├── cache-stack.ts         # ElastiCache Redis
│   │   ├── lambda-stack.ts        # Lambda functions (3 services)
│   │   ├── api-stack.ts           # API Gateway
│   │   └── frontend-stack.ts      # CloudFront CDN
│   ├── cdk.json
│   ├── package.json
│   └── tsconfig.json
```

**Example CDK Stack (Database):**

```typescript
// infrastructure/cdk/lib/database-stack.ts

import * as cdk from 'aws-cdk-lib';
import * as rds from 'aws-cdk-lib/aws-rds';
import * as ec2 from 'aws-cdk-lib/aws-ec2';

export class DatabaseStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    const vpc = new ec2.Vpc(this, 'ShtetlVPC', {
      maxAzs: 2
    });

    const dbInstance = new rds.DatabaseInstance(this, 'ShtetlPostgres', {
      engine: rds.DatabaseInstanceEngine.postgres({
        version: rds.PostgresEngineVersion.VER_17
      }),
      instanceType: ec2.InstanceType.of(
        ec2.InstanceClass.T3,
        ec2.InstanceSize.MICRO
      ),
      vpc,
      multiAz: false,
      allocatedStorage: 20,
      databaseName: 'shtetl',
      removalPolicy: cdk.RemovalPolicy.SNAPSHOT
    });
  }
}
```

**Who creates resources:** You (or CI/CD pipeline) via `cdk deploy`

**When resources are created:**
- **Development Phase:** None - everything runs locally in Coder ($0 AWS cost)
- **First Deployment:** You manually run `cdk deploy` to provision AWS
- **Ongoing Updates:** GitHub Actions automatically deploys on `git push origin main`

**Resource Management:**
- **Manual:** `cdk diff` / `cdk deploy`
- **Automated:** CI/CD pipeline (GitHub Actions)
- **AI-Assisted:** Claude Code can generate/update CDK stacks via BMAD workflows

**Why CDK over Terraform:**
- TypeScript familiarity (same language as frontend)
- Better AWS service coverage and faster updates
- Type safety and IDE autocomplete
- Built-in best practices and constructs
- Easier Lambda deployment integration

## Implementation Patterns

### Naming Conventions

**Go (Backend):**
- **Files:** `snake_case.go` (e.g., `zmanim_calculator.go`)
- **Packages:** lowercase, single word (e.g., `package calculator`)
- **Types/Structs:** `PascalCase` (e.g., `type ZmanimRequest struct`)
- **Functions:** `PascalCase` for exported, `camelCase` for private
- **Database Tables:** `snake_case` plural (e.g., `calendar_streams`, `minyanim`)
- **Database Columns:** `snake_case` (e.g., `shul_id`, `created_at`)

**React/TypeScript (Frontend):**
- **Components:** `PascalCase.tsx` (e.g., `MinyanTreeBuilder.tsx`)
- **Files (non-components):** `camelCase.ts` (e.g., `apiClient.ts`)
- **Functions/Variables:** `camelCase`
- **CSS/Style Files:** Match component name (e.g., `MinyanTreeBuilder.module.css`)

**API Endpoints (REST):**
- **Pattern:** `/api/v1/{resource}/{id}/{action}`
- **Resources:** plural, kebab-case (e.g., `/api/v1/calendar-streams`)
- **Examples:**
  - `GET /api/v1/shuls/{shulId}/minyanim`
  - `POST /api/v1/calendar-streams`
  - `GET /api/v1/schedules/{shulId}/today`

### Code Organization

**Go Services - Layered Architecture:**
```
handlers/     → HTTP/gRPC handlers (thin, validation only)
  ↓
domain/       → Business logic (pure Go, no framework dependencies)
  ↓
repository/   → Database access (GORM queries)
  ↓
models/       → GORM models
```

**Dependency Rule:** Inner layers don't know about outer layers. Domain logic is framework-agnostic.

### Error Handling

**Structured Errors:**
```go
// pkg/errors/errors.go

type APIError struct {
    Code    string                 `json:"code"`
    Message string                 `json:"message"`
    Details map[string]interface{} `json:"details,omitempty"`
}

// Error code pattern: SERVICE_DOMAIN_ERROR
// Examples:
//   ZMANIM_CALCULATION_INVALID
//   SHUL_VALIDATION_COVERAGE_INCOMPLETE
//   KEHILLA_SUBSCRIPTION_DUPLICATE

func ValidationError(message string, details map[string]interface{}) APIError {
    return APIError{
        Code:    "VALIDATION_ERROR",
        Message: message,
        Details: details,
    }
}
```

**Error Wrapping:**
```go
// Use Go's standard error wrapping
if err != nil {
    return fmt.Errorf("failed to calculate zmanim: %w", err)
}
```

### Logging

**Structured Logging with zerolog:**
```go
// pkg/logger/logger.go

var log zerolog.Logger

func Init(serviceName string) {
    log = zerolog.New(os.Stdout).With().
        Timestamp().
        Str("service", serviceName).
        Logger()
}

// Usage:
log.Error().
    Str("tenant_id", shulID).
    Err(err).
    Msg("Coverage validation failed")
```

**Log Levels:**
- `Error` - Critical failures, requires attention
- `Warn` - Non-critical issues, degraded functionality
- `Info` - Important business events (minyan published, stream created)
- `Debug` - Detailed diagnostic information

### Date/Time Handling

**Critical Standards:**
- **Storage:** UTC timestamps in PostgreSQL (`TIMESTAMPTZ`)
- **API:** ISO 8601 format (`2025-11-17T10:30:00Z`)
- **Hebrew Dates:** Store as separate fields (`hebrew_year`, `hebrew_month`, `hebrew_day`) + Gregorian mapping
- **Zmanim Times:** Store in location's local time + timezone info
- **Go Library:** Standard `time.Time` + custom Hebrew calendar package

**Example:**
```go
type ZmanimValue struct {
    ID          uuid.UUID `gorm:"primaryKey"`
    StreamID    uuid.UUID
    Date        time.Time `gorm:"type:date"`
    Latitude    float64
    Longitude   float64
    Timezone    string
    Netz        time.Time `gorm:"type:timestamptz"` // UTC
    Shkiah      time.Time `gorm:"type:timestamptz"`
    CalculatedAt time.Time `gorm:"type:timestamptz;default:now()"`
}
```

### Testing Strategy

**Unit Tests:**
```go
// All business logic in domain/ packages
// Example: services/shul/internal/domain/validation/coverage_validator_test.go

func TestCoverageValidator_Validate100Percent(t *testing.T) {
    // Test full coverage scenario
}

func TestCoverageValidator_DetectMissingDays(t *testing.T) {
    // Test gap detection
}
```

**Integration Tests:**
```go
// API endpoints with test database
// Example: services/shul/internal/handlers/minyan_handler_test.go

func TestMinyanHandler_CreateMinyan(t *testing.T) {
    // Setup test DB
    // Create HTTP request
    // Assert response
}
```

**DSL Parser Tests:**
```go
// Comprehensive test cases for valid/invalid syntax
// Example: services/shul/internal/domain/dsl/parser_test.go

func TestSchedulingDSL_ParseValidRules(t *testing.T) {
    // Test various valid DSL syntax
}

func TestSchedulingDSL_RejectInvalidSyntax(t *testing.T) {
    // Test error handling
}
```

**Coverage Validation Tests:**
```go
// services/shul/internal/domain/validation/coverage_validator_test.go

func TestCoverageValidator_100PercentCoverage(t *testing.T) {
    // Ensure perfect coverage detection works
}

func TestCoverageValidator_IdentifyGaps(t *testing.T) {
    // Ensure gap detection is accurate
}
```

**Hebrew Rendering Tests:**
- PDF output visual regression tests
- RTL text rendering verification
- Font rendering validation

## Architecture Decision Records (ADRs)

### ADR-001: Multi-Repo vs Monorepo

**Decision:** Use multi-repo approach (3 separate repositories)

**Rationale:**
- Clean separation of concerns (backend, web, mobile)
- Independent deployment cycles
- Different technology stacks (Go, React, React Native)
- Easier for contributors to focus on specific areas
- Better for beginner skill level (simpler mental model)

**Consequences:**
- Need to coordinate API contract changes across repos
- Shared types may require duplication
- More complex local development setup

### ADR-002: PostgreSQL vs MongoDB

**Decision:** Use PostgreSQL with JSONB for flexible fields

**Rationale:**
- Data is more structured than initially thought
- Multi-tenant row-level security is critical
- JSONB provides flexibility where needed (primitives, DSL storage)
- Vendor-neutral - standard SQL works anywhere
- Better for future analytics and reporting
- Strong data integrity with foreign keys and constraints

**Consequences:**
- Need to design schema carefully for tree structures
- JSONB fields require careful indexing for performance
- Migration from document model thinking to relational

### ADR-003: Separate DSL Parsers

**Decision:** Completely separate DSL implementations for Zmanim and Minyan scheduling

**Rationale:**
- Different audiences (technical vs non-technical)
- Different complexity levels
- Different validation requirements
- No shared parsing logic worth abstracting
- Allows independent evolution of each DSL

**Consequences:**
- Some duplicate parser infrastructure
- Need to maintain two Monaco language definitions
- More initial development work

### ADR-004: Full DSL from MVP (No JSON Interim)

**Decision:** Implement complete DSL system in MVP, not simpler JSON config

**Rationale:**
- DSL is core differentiator of Shtetl
- Switching from JSON to DSL later would require data migration
- Monaco editor provides excellent DSL editing experience
- Users expect powerful, flexible rule system from day 1
- Validates core value proposition early

**Consequences:**
- Higher MVP complexity
- Requires extensive DSL design research with users
- Longer development timeline
- Higher risk, higher reward

### ADR-005: GORM with Raw SQL for Complex Queries

**Decision:** Use GORM as primary ORM, but write complex queries in raw SQL

**Rationale:**
- GORM provides productivity for CRUD operations
- Auto-migrations useful for rapid development
- Complex queries (coverage validation, primitive resolution) need SQL performance
- Raw SQL more readable for complex joins
- Hybrid approach balances productivity and performance

**Consequences:**
- Team needs to know when to use GORM vs raw SQL
- Some queries duplicated in GORM and SQL
- Need discipline to not over-rely on GORM for everything

### ADR-006: Clerk for Authentication

**Decision:** Use Clerk for authentication and user management

**Rationale:**
- **User metadata support:** Store `shul_id` and `role` in Clerk user metadata
- **Pre-built UI components:** React and mobile SDKs with customizable auth flows
- **JWT tokens:** Secure, verifiable tokens with custom claims for multi-tenancy
- **Go SDK:** Strong backend integration for token verification
- **Multiple auth providers:** Email, Google, Apple, phone (SMS)
- **Developer experience:** Excellent DX, fast integration
- **Pricing:** Per-user pricing works for SaaS model

**Multi-Tenant Implementation:**
- Single Clerk organization for entire Shtetl platform
- User metadata: `{ shul_id: "uuid", role: "shul_admin" }`
- JWT claims include shul_id for backend validation
- Application enforces data isolation via shul_id

**Example User Structures:**
```json
// Shul Admin (Gabai)
{
  "user_id": "user_2abc123",
  "email": "rabbi@shul.com",
  "public_metadata": {
    "shul_id": "shul_123",
    "role": "shul_admin",
    "shul_name": "Beis Mordechai Manchester"
  }
}

// Kehilla Member (Community Member/Congregant)
{
  "user_id": "user_3xyz789",
  "email": "member@example.com",
  "public_metadata": {
    "shul_id": "shul_123",
    "role": "kehilla",
    "shul_name": "Beis Mordechai Manchester"
  }
}
```
**Note:** "Kehilla" = community members in Hebrew

**Consequences:**
- Vendor dependency for critical auth system
- Need fallback plan if pricing becomes prohibitive ($0.02/MAU after 10K)
- Must abstract Clerk behind interfaces for potential migration
- Application-level multi-tenancy (not Clerk Organizations)

### ADR-007: AWS CDK over Terraform

**Decision:** Use AWS CDK (TypeScript) for infrastructure as code instead of Terraform

**Rationale:**
- **TypeScript consistency:** Same language as frontend (React/TypeScript)
- **Type safety:** IDE autocomplete, compile-time checks, fewer runtime errors
- **AWS-native:** Better service coverage, faster updates when AWS releases new features
- **Constructs:** Higher-level abstractions (L2/L3 constructs) = less boilerplate
- **Lambda integration:** Easier to bundle and deploy Go Lambda functions
- **Developer familiarity:** Frontend developers can contribute to infrastructure
- **CDK Diff:** Better diff preview than Terraform plan
- **No state management:** CloudFormation handles state, no S3 backend config needed

**Context:**
Shtetl is AWS-only (no multi-cloud requirement). Terraform's multi-cloud abstraction is unnecessary complexity. CDK's AWS-specific focus provides better DX for AWS workloads.

**Example Comparison:**

Terraform (HCL):
```hcl
resource "aws_lambda_function" "zmanim" {
  filename         = "function.zip"
  function_name    = "shtetl-zmanim"
  role            = aws_iam_role.lambda.arn
  handler         = "bootstrap"
  runtime         = "provided.al2"
  source_code_hash = filebase64sha256("function.zip")
}
```

CDK (TypeScript):
```typescript
new lambda.Function(this, 'ZmanimService', {
  runtime: lambda.Runtime.GO_1_X,
  handler: 'bootstrap',
  code: lambda.Code.fromAsset('../../services/zmanim'),
  environment: {
    DB_HOST: database.instanceEndpoint.hostname
  }
});
```

**Consequences:**
- Team must learn CDK (but TypeScript is already known)
- AWS-only lock-in (acceptable - no multi-cloud plans)
- Better infrastructure debugging via TypeScript
- Easier onboarding for TypeScript developers
- Infrastructure code lives in same language ecosystem as frontend

**Risk Mitigation:**
- CDK can export CloudFormation templates (portable)
- If multi-cloud needed later, can migrate to Terraform/Pulumi
- CDK is open-source and widely adopted

### ADR-008: AI-First Development with BMAD v6 + Coder

**Decision:** Adopt AI-first development approach using BMAD Method v6, Claude Code, and local Coder workspaces

**Rationale:**
- **BMAD v6** provides structured workflow for AI agent orchestration
- **Claude Code** enables context-aware AI assistance throughout development lifecycle
- **Coder** ensures consistent, reproducible development environments for all contributors
- **MCP Integration** connects AI agents deeply with tools (Git, PostgreSQL, filesystem)
- Validates modern AI-assisted development practices for open-source project
- Reduces onboarding time for contributors from days to minutes
- Enables distributed team to work with identical environments

**Context:**
Traditional development workflows struggle with:
- Environment configuration drift ("works on my machine")
- Long onboarding times for new contributors (2-4+ hours setup)
- Inconsistent AI tooling across team members
- Manual context-switching between documentation and code

BMAD v6 solves this by:
- Providing phase-based methodology (Discovery → Planning → Solutioning → Implementation)
- Agent personas that maintain consistent project understanding
- Workflow tracking and documentation generation
- Integration with Claude Code for implementation

Coder solves environment issues by:
- Terraform-based workspace templates (identical environments)
- Auto-shutdown of idle workspaces (cost optimization)
- Support for any IDE (VS Code, Cursor, JetBrains, Neovim)
- Self-hosted on developer's infrastructure (no vendor lock-in)

**Implementation:**
- **Day 0 (Project Setup):** Initialize BMAD v6, create Coder workspace template
- **Phase 0-2:** Use BMAD workflows for planning and architecture (✅ completed)
- **Phase 3:** Use `/bmad:bmm:workflows:dev-story` with Claude Code for implementation
- **Day 1 onwards:** All development happens in Coder workspaces with Claude Code + BMAD
- **Contributors:** "Open in Coder" button in README → productive in 5 minutes
- **Documentation:** This architecture document serves as primary AI agent context

**Success Metrics:**
- New contributor time-to-first-commit: <10 minutes (vs hours traditionally)
- Environment consistency: 100% (vs configuration drift)
- AI agent context accuracy: Measured by successful story completion without human intervention
- Development velocity: Track story completion rate with AI assistance

**Consequences:**
- Requires BMAD v6 installation and maintenance
- Team must learn BMAD workflow commands and agent personas
- Claude Code subscription costs (~$20-40/user/month)
- Coder infrastructure costs (or free self-hosted)
- Higher initial learning curve, but faster long-term development
- Dependency on Anthropic's Claude API availability
- Need fallback documentation for manual development if AI unavailable

**Risk Mitigation:**
- BMAD is open-source and customizable
- Coder is open-source and self-hostable (no vendor lock-in)
- Architecture document (this file) remains human-readable for manual development
- Traditional development workflow still fully supported

### ADR-009: Visual Template Designer for Print-Ready Timetables

**Decision:** Implement a visual, no-code template designer system for generating print-ready timetables rather than fixed PDF layouts or code-based template customization.

**Rationale:**
- **Non-Technical Users:** Gaboim (synagogue administrators) are non-technical and cannot edit code or complex configurations
- **Per-Shul Customization:** Each synagogue has unique layout preferences (logos, colors, fonts, column arrangements, branding)
- **Multiple Time Periods:** System must support daily, weekly, monthly, and seasonal (bi-annual) timetables without code changes
- **Hebrew RTL Support:** Print layouts must correctly render Hebrew text with right-to-left directionality
- **Community Standards:** PDFs must match familiar timetable formats used by Jewish communities (FR38-FR39)
- **Immediate Feedback:** Users need instant preview of changes before generating final PDFs

**Context:**

From PRD requirements:
- **FR38:** Administrators can generate formatted PDF timetables with Hebrew RTL text support
- **FR39:** PDF output matches community-standard layouts (multi-minyan display, location markers)
- **FR40:** Administrators can export schedules to multiple formats (PDF, CSV, JSON)
- **NFR-ACCESS-6 through NFR-ACCESS-9:** Hebrew RTL text rendering requirements
- **User Persona:** Gaboim are non-technical users who need complete control without complexity (PRD line 19)

Traditional approaches fail because:
- **Fixed Templates:** Each shul has unique preferences; one-size-fits-all doesn't work
- **Code-Based Customization:** Gaboim can't edit React/CSS code
- **Designer Tools External:** Requiring Canva/Adobe introduces friction and export issues
- **No Hebrew Support:** Most template systems don't handle RTL text properly

**Architecture Design:**

**1. Visual Template Designer (Web UI)**

```typescript
// Frontend: shtetl-web/src/features/shul-admin/pdf/

interface TemplateDesignerConfig {
  // Page Setup (dropdowns)
  pageSize: 'A4' | 'Letter' | 'Legal';
  orientation: 'Portrait' | 'Landscape';

  // Header Section (toggles + uploads)
  header: {
    showShulName: boolean;
    showLogo: boolean;
    logoUrl?: string;
    showDateRange: boolean;
    customText?: string;
  };

  // Layout Section (visual selection)
  layout: {
    style: 'Grid' | 'List' | 'Compact';
    columnsPerWeek: 7 | 14;
    showWeekends: 'Separate' | 'Inline' | 'Hidden';
    dayHeaderSize: 'Small' | 'Medium' | 'Large';
  };

  // Content Display (checkboxes)
  content: {
    showHebrewDates: boolean;
    showZmanim: boolean;
    showMinyanLocations: boolean;
    groupMinyanBy: 'Type' | 'Time' | 'Location';
    highlightHolidays: boolean;
  };

  // Typography (visual pickers)
  typography: {
    fontFamily: 'David Libre' | 'Frank Ruehl' | 'Arial';
    minyanFontSize: 'Small (10pt)' | 'Medium (12pt)' | 'Large (14pt)';
    timeFontSize: 'Small' | 'Medium' | 'Large';
    boldTimes: boolean;
  };

  // Colors (color pickers)
  colors: {
    headerBackground: string;
    holidayHighlight: string;
    fastDayHighlight: string;
    weekdayBackground: string;
    shabbatBackground: string;
  };
}
```

**2. Preset Templates (80% Coverage)**

Provide 3-5 professionally designed presets that cover most use cases:
- **Classic Grid:** Traditional weekly grid, 7 columns, portrait A4
- **Compact List:** Space-efficient list format, fits 2-3 weeks per page
- **Monthly Overview:** Full month on one landscape page
- **Daily Detail:** One day per page with shiurim and events
- **Seasonal Calendar:** Multi-week view for High Holiday season

Users start with preset → customize colors/fonts/logo → save as custom template.

**3. Backend Storage & API**

```sql
-- Store custom templates per synagogue
CREATE TABLE shul_pdf_templates (
  id UUID PRIMARY KEY,
  shul_id UUID REFERENCES shuls(id),
  name VARCHAR(100) NOT NULL,
  config JSONB NOT NULL,  -- Full TemplateDesignerConfig
  is_default BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

```go
// services/shul/internal/domain/pdf/generator.go

type PDFGenerator struct {
    templates map[string]*Template
}

func (g *PDFGenerator) GenerateTimetable(
    shulID string,
    dateRange DateRange,
    config TemplateDesignerConfig,
) ([]byte, error) {
    // 1. Fetch schedule data
    schedule := g.scheduleRepo.GetSchedule(shulID, dateRange)

    // 2. Select layout engine based on config
    var layout Layout
    switch config.Layout.Style {
    case "Grid":
        layout = NewGridLayout(config)
    case "List":
        layout = NewListLayout(config)
    case "Compact":
        layout = NewCompactLayout(config)
    }

    // 3. Render PDF with react-pdf or Go library
    pdf := layout.Render(schedule, config)

    return pdf.Bytes(), nil
}
```

**4. Technical Implementation**

**Option A: react-pdf (Frontend Generation) - Recommended for MVP**
- Instant preview without server roundtrip
- Better Hebrew RTL support (built-in)
- Easier iteration on templates
- PDFDownloadLink component for direct download

**Option B: Backend PDF Generation (Go) - For Mobile/Automation**
- API endpoint: `POST /api/v1/shuls/{id}/generate-pdf`
- Required for mobile admin apps
- Required for scheduled/automated generation
- Use github.com/jung-kurt/gofpdf or similar

**Hybrid Approach:**
- Web UI uses react-pdf for live preview + download
- Mobile/automation uses Go backend API
- Both share same TemplateDesignerConfig format

**5. User Experience Flow**

```
1. Admin clicks "Generate Timetable"
2. Modal opens with template selector:
   - "Weekly Grid (Most Common)"
   - "Monthly Overview"
   - "Custom Template" (if previously saved)
3. Select template → Designer opens:
   - Left panel: Configuration controls
   - Right panel: Live PDF preview
4. User adjusts:
   - Upload shul logo
   - Pick brand colors
   - Toggle zmanim display
   - Adjust font sizes
5. Changes update preview in real-time
6. Click "Save Template" (optional, for reuse)
7. Click "Download PDF"
8. PDF generated and downloaded
```

**6. Project Structure**

```
shtetl-web/src/features/shul-admin/pdf/
├── components/
│   ├── TemplateDesigner.tsx       # Main designer UI
│   ├── TemplateSelector.tsx       # Preset selection
│   ├── ConfigurationPanel.tsx     # Left-side controls
│   ├── LivePreview.tsx            # Right-side preview
│   └── TemplateLibrary.tsx        # Saved templates
├── templates/
│   ├── WeeklyGridTemplate.tsx     # react-pdf component
│   ├── MonthlyTemplate.tsx
│   ├── DailyTemplate.tsx
│   └── CompactListTemplate.tsx
├── layouts/
│   ├── GridLayout.tsx             # Reusable layout engines
│   ├── ListLayout.tsx
│   └── CompactLayout.tsx
├── hooks/
│   ├── useTemplateConfig.ts       # Load/save configs
│   └── usePDFGeneration.ts        # Generate PDFs
└── api/
    └── pdfApi.ts                  # Backend API calls
```

**Backend:**
```
services/shul/internal/domain/pdf/
├── generator.go           # Main PDF generator
├── layouts/
│   ├── grid_layout.go
│   ├── list_layout.go
│   └── compact_layout.go
├── renderers/
│   ├── hebrew_text.go    # RTL text handling
│   └── calendar_grid.go
└── templates/
    └── config.go          # TemplateDesignerConfig struct
```

**Implementation:**

**MVP Scope:**
- 2-3 preset templates (Weekly Grid, Monthly Overview)
- Basic customization: logo upload, color selection, font size
- react-pdf frontend generation with live preview
- Save/load custom templates per shul

**Post-MVP Enhancements:**
- Advanced layout builder (drag-and-drop sections)
- More preset templates (daily, seasonal, compact)
- Backend Go PDF generation for mobile/automation
- Template sharing marketplace (shuls share templates)
- Multi-page scheduling (e.g., Elul through Sukkot)
- Custom page breaks and section dividers

**Consequences:**

**Positive:**
- ✅ Empowers non-technical gaboim to create professional timetables
- ✅ Each shul gets unique branded layouts without developer intervention
- ✅ Instant visual feedback reduces errors and improves UX
- ✅ Reusable templates save time for recurring timetable generation
- ✅ Aligns with NFR-USE-1: Gaboim can create schedules within 30 minutes
- ✅ Supports all time periods (daily/weekly/monthly) through single system

**Negative:**
- ⚠️ More complex than fixed templates (higher initial development cost)
- ⚠️ Need to maintain template rendering engine across changes
- ⚠️ Visual designer UI requires significant frontend work
- ⚠️ Testing requires validating many configuration combinations

**Technical Challenges:**
- **Hebrew RTL Rendering:** Must handle bidirectional text correctly in PDFs
- **Print-to-Screen Fidelity:** Preview must exactly match final PDF output
- **Configuration Complexity:** Need to balance flexibility vs. overwhelming users
- **Mobile PDF Generation:** Backend API required for mobile admins

**Risk Mitigation:**
- Start with 2-3 presets to validate approach before building full designer
- Use react-pdf library (proven Hebrew RTL support)
- Provide "Reset to Default" option if users break layouts
- Comprehensive testing with real synagogue layouts from design partners
- Fallback to backend generation if browser performance issues arise

**Testing Strategy:**
- Visual regression tests for preset templates
- Hebrew text rendering validation (no mojibake, correct RTL)
- Cross-browser PDF generation testing
- User testing with actual gaboim from design partner shuls
- Performance testing: large timetables (100+ days, 10+ minyanim)

**Success Criteria:**
- 80%+ of gaboim can generate branded timetable within 15 minutes
- Generated PDFs match community standards (validated by design partners)
- Hebrew text renders correctly in all browsers and PDF viewers
- Users save and reuse custom templates (indicates value)
- Zero "can you help me design this" support requests after initial training

**Related Requirements:**
- FR38, FR39, FR40 (PDF generation requirements)
- NFR-ACCESS-6 through NFR-ACCESS-9 (Hebrew RTL support)
- NFR-USE-1 (30-minute learning curve for gaboim)
- NFR-USE-8 (WYSIWYG preview)

### ADR-010: GitHub Projects for Story Management with BMAD Integration

**Decision:** Use GitHub Projects as the primary project management tool, with BMAD workflows creating and syncing stories to GitHub Issues/Projects via GitHub CLI (`gh`).

**Rationale:**
- **Centralized Tracking:** GitHub Projects provides visual kanban boards, roadmaps, and progress tracking
- **Team Collaboration:** Multiple contributors can see project status without accessing local files
- **Issue Integration:** Stories become GitHub Issues with full discussion, labels, assignees
- **GitHub CLI Available:** `gh` CLI already installed (version 2.45.0), enables automation
- **BMAD Compatibility:** BMAD creates markdown stories locally; can sync to GitHub via post-creation hook
- **No Vendor Lock-in:** Stories exist as both markdown files (portable) AND GitHub Issues (collaborative)

**Context:**

BMAD v6 currently:
- Creates stories as markdown files: `docs/sprint-artifacts/story-001.md`
- Tracks status in: `docs/sprint-artifacts/sprint-status.yaml`
- Workflows: `/bmad:bmm:workflows:create-story`, `/bmad:bmm:workflows:dev-story`, etc.

GitHub Projects provides:
- Visual kanban board with drag-and-drop status updates
- Roadmap view with timelines and milestones
- Filtering by epic, assignee, labels, status
- Comments and discussion threads per story
- Integration with pull requests and code reviews

**Challenge:** BMAD doesn't natively support GitHub Projects - need to build integration layer.

**Architecture Design:**

**1. Dual-Source-of-Truth Strategy**

```
Local Markdown (BMAD)          GitHub Issues (Projects)
├── story-001.md       ←sync→  Issue #1
├── story-002.md       ←sync→  Issue #2
└── sprint-status.yaml ←sync→  Project Board
```

**Local markdown remains canonical source** for story content (acceptance criteria, technical specs, dev notes).

**GitHub Issues track status** (To Do, In Progress, Done) and collaboration (comments, assignees).

**2. GitHub CLI Integration Script**

Create a helper script that BMAD workflows can call:

```bash
#!/bin/bash
# scripts/sync-story-to-github.sh

STORY_FILE=$1
STORY_KEY=$(basename $STORY_FILE .md)

# Extract story metadata from markdown
TITLE=$(grep "^# " $STORY_FILE | head -1 | sed 's/^# //')
EPIC=$(grep "^Epic:" $STORY_FILE | cut -d: -f2 | xargs)
DESCRIPTION=$(sed -n '/## Description/,/## /p' $STORY_FILE | tail -n +2 | head -n -1)

# Create or update GitHub Issue
ISSUE_NUMBER=$(gh issue list --label "$STORY_KEY" --json number --jq '.[0].number')

if [ -z "$ISSUE_NUMBER" ]; then
  # Create new issue
  gh issue create \
    --title "$TITLE" \
    --body "$DESCRIPTION\n\n[Full Story](../blob/main/$STORY_FILE)" \
    --label "story,$EPIC,$STORY_KEY" \
    --project "Shtetl Development"
else
  # Update existing issue
  gh issue edit $ISSUE_NUMBER \
    --title "$TITLE" \
    --body "$DESCRIPTION\n\n[Full Story](../blob/main/$STORY_FILE)"
fi
```

**3. BMAD Workflow Integration**

Modify BMAD workflows to sync after story creation/updates:

```yaml
# .bmad/bmm/config.yaml (add)
sync_to_github: true
github_project_name: "Shtetl Development"
```

**Workflow Hook Points:**

```
/bmad:bmm:workflows:create-story
  → Creates story-001.md
  → Calls: scripts/sync-story-to-github.sh story-001.md
  → Result: GitHub Issue #1 created in "To Do" column

/bmad:bmm:workflows:story-ready
  → Marks story as "in-progress" in sprint-status.yaml
  → Calls: scripts/update-story-status.sh story-001 "In Progress"
  → Result: Issue #1 moved to "In Progress" column

/bmad:bmm:workflows:story-done
  → Marks story as "done"
  → Result: Issue #1 moved to "Done" column
```

**4. GitHub Projects Configuration**

**Create Project:**
```bash
# One-time setup
gh project create \
  --owner yourusername \
  --title "Shtetl Development" \
  --format "Board"
```

**Columns:**
- **Backlog** - Drafted stories not yet started
- **Ready** - Stories ready for development
- **In Progress** - Active development
- **Review** - Code review / testing
- **Done** - Completed stories

**Labels:**
- `story` - All user stories
- `epic-1`, `epic-2`, etc. - Epic grouping
- `story-001`, `story-002`, etc. - Story identifiers
- `phase-mvp`, `phase-2`, etc. - Release phases

**5. Sync Strategy (Bidirectional)**

**Direction 1: BMAD → GitHub**
- BMAD workflows create/update markdown files
- Post-workflow script syncs to GitHub Issues: `scripts/sync-story-to-github.sh`
- Issue body links back to markdown file for full details
- Triggers: After `/bmad:bmm:workflows:create-story`, `story-ready`, `story-done`

**Direction 2: GitHub → BMAD (Automated via GitHub Actions)**
- When issue moved between columns in GitHub Projects
- When issue closed/reopened in GitHub
- **When issue edited in GitHub** (title, description changes)
- GitHub Action detects change via webhook
- Action updates `sprint-status.yaml` for status changes
- Action updates `story-XXX.md` for content edits (title, description)
- Action commits changes back to repository
- Comments/discussions stay in GitHub (not synced to markdown)

**Sync Implementation:**

```yaml
# .github/workflows/sync-github-to-bmad.yml
name: Sync GitHub to BMAD

on:
  issues:
    types: [closed, reopened, labeled, unlabeled, edited]
  project_card:
    types: [moved, created, deleted]

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: pip install pyyaml

      - name: Sync status changes
        if: github.event.action != 'edited'
        run: |
          STORY_KEY=$(gh issue view ${{ github.event.issue.number }} \
            --json labels --jq '.labels[] | select(.name | startswith("story-")) | .name')

          COLUMN=$(gh issue view ${{ github.event.issue.number }} \
            --json projectCards --jq '.projectCards[0].column.name')

          # Map column to status
          case $COLUMN in
            "Ready") STATUS="ready" ;;
            "In Progress") STATUS="in-progress" ;;
            "Review") STATUS="review" ;;
            "Done") STATUS="done" ;;
            *) STATUS="backlog" ;;
          esac

          python scripts/update-sprint-status.py "$STORY_KEY" "$STATUS"

      - name: Sync content edits
        if: github.event.action == 'edited'
        run: |
          STORY_KEY=$(gh issue view ${{ github.event.issue.number }} \
            --json labels --jq '.labels[] | select(.name | startswith("story-")) | .name')

          TITLE=$(gh issue view ${{ github.event.issue.number }} --json title --jq '.title')
          BODY=$(gh issue view ${{ github.event.issue.number }} --json body --jq '.body')

          # Update story markdown file with new title/description
          python scripts/update-story-content.py "$STORY_KEY" "$TITLE" "$BODY"

      - name: Commit changes
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add docs/sprint-artifacts/
          git commit -m "chore: sync story changes from GitHub (#${{ github.event.issue.number }})" || exit 0
          git push
```

**Python Helper Scripts:**

```python
#!/usr/bin/env python3
# scripts/update-sprint-status.py
# Updates story status in sprint-status.yaml

import sys
import yaml

story_key = sys.argv[1]
new_status = sys.argv[2]

with open('docs/sprint-artifacts/sprint-status.yaml', 'r') as f:
    data = yaml.safe_load(f)

# Update status for matching story
for story in data.get('development_status', []):
    if story.get('story_key') == story_key:
        story['status'] = new_status
        break

with open('docs/sprint-artifacts/sprint-status.yaml', 'w') as f:
    yaml.dump(data, f, default_flow_style=False)
```

```python
#!/usr/bin/env python3
# scripts/update-story-content.py
# Updates story markdown file with edits from GitHub Issue

import sys
import re

story_key = sys.argv[1]
new_title = sys.argv[2]
new_body = sys.argv[3]

story_file = f'docs/sprint-artifacts/{story_key}.md'

# Read existing story file
with open(story_file, 'r') as f:
    content = f.read()

# Update title (first H1 header)
content = re.sub(r'^# .*$', f'# {new_title}', content, count=1, flags=re.MULTILINE)

# Update description section
# Extract description from GitHub issue body (before "---" separator)
description = new_body.split('---')[0].strip()

# Replace description section
content = re.sub(
    r'(## Description\n\n).*?(\n\n## )',
    rf'\1{description}\2',
    content,
    flags=re.DOTALL
)

# Write updated content back
with open(story_file, 'w') as f:
    f.write(content)

print(f'Updated {story_file} with changes from GitHub')
```

**Sync Scope and Limitations:**

**What Syncs from GitHub → BMAD:**
- ✅ **Title edits** → Updates `# Title` in story markdown
- ✅ **Description edits** → Updates `## Description` section
- ✅ **Status changes** (column moves) → Updates `sprint-status.yaml`
- ✅ **Issue close/reopen** → Updates status to "done" or reopens

**What Does NOT Sync from GitHub → BMAD:**
- ❌ **Acceptance Criteria** → Too structured, cannot parse from GitHub issue body
- ❌ **Technical Specifications** → Complex markdown sections, stays in BMAD only
- ❌ **Dev Notes** → Development learnings recorded in BMAD, not synced
- ❌ **Code Review Comments** → Stays in BMAD story file
- ❌ **Task Lists** → BMAD tracks subtasks, GitHub shows high-level only
- ❌ **Issue Comments** → Collaboration stays in GitHub, not written to markdown

**Rationale for Partial Sync:**
BMAD story files have rich, structured sections (Acceptance Criteria, Technical Approach, Dependencies, Dev Notes, Review sections) that cannot be reliably parsed from free-form GitHub issue text. Attempting full bidirectional sync of complex content would lead to data loss or corruption.

**Recommended Usage Pattern:**
1. **Create stories in BMAD** (`/bmad:bmm:workflows:create-story`) → Full structured template
2. **Collaborate in GitHub Projects** → Visual kanban, comments, discussions, assignees
3. **Quick edits in GitHub** → Title/description changes sync back to BMAD
4. **Technical edits in BMAD** → Update acceptance criteria, technical specs, dev notes locally
5. **Re-sync to GitHub** → Run `scripts/sync-story-to-github.sh` to push BMAD changes

**Conflict Resolution:**
- **BMAD markdown files are authoritative** for structured content (acceptance criteria, technical details, dev notes)
- **GitHub Projects is authoritative** for status tracking (backlog, in-progress, done) and collaboration (comments, assignees)
- If both updated simultaneously: GitHub status wins, BMAD technical content wins
- GitHub Actions will overwrite BMAD title/description if edited in GitHub
- To prevent overwrites: Edit technical details in BMAD, keep title/description edits in GitHub minimal

**6. Story Lifecycle Example**

```
1. PM: /bmad:bmm:workflows:create-story
   → Creates: docs/sprint-artifacts/story-001.md
   → Syncs to: GitHub Issue #1 (Backlog column)
   → Labels: story, epic-1, story-001

2. Dev: /bmad:bmm:workflows:story-ready
   → Updates: sprint-status.yaml (status: ready)
   → Syncs to: GitHub Issue #1 → moves to "Ready" column

3. Dev: /bmad:bmm:workflows:dev-story
   → Updates: sprint-status.yaml (status: in-progress)
   → Syncs to: GitHub Issue #1 → moves to "In Progress"
   → Assigns: Current user as assignee

4. Dev commits code:
   → Commit message: "feat: implement story-001 (#1)"
   → Links PR to Issue #1 automatically

5. Dev: /bmad:bmm:workflows:story-done
   → Updates: story-001.md (marks DoD complete)
   → Syncs to: GitHub Issue #1 → moves to "Done"
   → Closes: Issue #1 (optional)
```

**7. Project Structure**

```
shtetl/
├── .github/
│   └── workflows/
│       └── sync-stories.yml      # CI automation (optional)
├── scripts/
│   ├── sync-story-to-github.sh   # Sync single story
│   ├── update-story-status.sh    # Update issue status
│   └── sync-all-stories.sh       # Bulk sync (one-time)
├── docs/
│   └── sprint-artifacts/
│       ├── story-001.md
│       ├── story-002.md
│       └── sprint-status.yaml
└── .bmad/
    └── bmm/
        └── config.yaml           # sync_to_github: true
```

**Implementation:**

**MVP Scope:**
- Manual sync script: `scripts/sync-story-to-github.sh`
- One-way sync: BMAD → GitHub Issues
- Status updates: Ready, In Progress, Done
- Labels: story, epic, story-key

**Post-MVP Enhancements:**
- Automated sync via git hooks or CI/CD
- Bidirectional sync (GitHub status → sprint-status.yaml)
- GitHub Actions workflow to auto-sync on commit
- Rich issue templates matching story structure
- Epic-level GitHub Milestones
- Automated PR linking via story keys

**Example Script Implementation:**

```bash
#!/bin/bash
# scripts/sync-story-to-github.sh

set -e

STORY_FILE=$1

if [ ! -f "$STORY_FILE" ]; then
  echo "Error: Story file not found: $STORY_FILE"
  exit 1
fi

# Extract metadata from markdown frontmatter or headers
STORY_KEY=$(basename $STORY_FILE .md)
TITLE=$(grep -m1 "^# " $STORY_FILE | sed 's/^# //')
EPIC=$(grep "^Epic:" $STORY_FILE | cut -d: -f2 | xargs || echo "unassigned")
STATUS=$(grep "^Status:" $STORY_FILE | cut -d: -f2 | xargs || echo "backlog")

# Extract description (first paragraph after title)
DESCRIPTION=$(awk '/^# /,/^## /' $STORY_FILE | tail -n +2 | head -n -1)

# Build issue body with link to full story
ISSUE_BODY="$DESCRIPTION

---

**Full Story:** [View in repository](../blob/main/$STORY_FILE)
**Epic:** $EPIC
**Story Key:** $STORY_KEY
"

# Check if issue already exists
ISSUE_NUMBER=$(gh issue list --label "$STORY_KEY" --json number --jq '.[0].number')

if [ -z "$ISSUE_NUMBER" ]; then
  echo "Creating new GitHub Issue for $STORY_KEY..."
  gh issue create \
    --title "$TITLE" \
    --body "$ISSUE_BODY" \
    --label "story,$EPIC,$STORY_KEY" \
    --project "Shtetl Development"
  echo "✓ Created Issue for $STORY_KEY"
else
  echo "Updating existing GitHub Issue #$ISSUE_NUMBER for $STORY_KEY..."
  gh issue edit $ISSUE_NUMBER \
    --title "$TITLE" \
    --body "$ISSUE_BODY"
  echo "✓ Updated Issue #$ISSUE_NUMBER"
fi

# Update status column based on story status
case $STATUS in
  "ready")
    gh issue edit $ISSUE_NUMBER --add-project "Shtetl Development" --column "Ready"
    ;;
  "in-progress")
    gh issue edit $ISSUE_NUMBER --add-project "Shtetl Development" --column "In Progress"
    ;;
  "done")
    gh issue edit $ISSUE_NUMBER --add-project "Shtetl Development" --column "Done"
    gh issue close $ISSUE_NUMBER
    ;;
  *)
    gh issue edit $ISSUE_NUMBER --add-project "Shtetl Development" --column "Backlog"
    ;;
esac
```

**Consequences:**

**Positive:**
- ✅ Visual project management via GitHub Projects
- ✅ Team collaboration with comments and discussions
- ✅ Markdown stories remain portable and version-controlled
- ✅ Integration with PRs and code review workflow
- ✅ No additional tool subscriptions (GitHub already used)
- ✅ Works with BMAD workflows (minimal changes needed)

**Negative:**
- ⚠️ Requires GitHub CLI (`gh`) installation
- ⚠️ Manual script execution unless automated via hooks
- ⚠️ Potential sync drift if status updated in both places
- ⚠️ Additional maintenance overhead for sync scripts

**Technical Challenges:**
- **Sync Conflicts:** If status changed in GitHub and BMAD simultaneously
- **Label Management:** Keeping epic/story labels consistent
- **Issue Templates:** Ensuring GitHub Issue format matches story structure
- **Bulk Operations:** Syncing many existing stories at once

**Risk Mitigation:**
- Start with one-way sync (BMAD → GitHub) to avoid conflicts
- Use story-key label as unique identifier for issue matching
- Provide bulk sync script for initial migration: `scripts/sync-all-stories.sh`
- Document sync process clearly in project README
- Make sync opt-in via config flag: `sync_to_github: true`

**Testing Strategy:**
- Test sync with sample stories in test repository
- Verify label creation and assignment
- Test status transitions (Backlog → Ready → In Progress → Done)
- Validate issue linking with commit messages and PRs
- Test edge cases: missing metadata, duplicate issues

**Success Criteria:**
- 100% of stories created by BMAD appear in GitHub Projects
- Status updates sync within 1 minute (manual) or on commit (automated)
- Team can manage project entirely from GitHub Projects UI
- Markdown files remain complete record of story technical details
- No duplicate issues created for same story

**Alternative Considered:**

**Option: Abandon BMAD Stories, Use GitHub Issues Only**
- **Rejected because:**
  - Lose rich markdown formatting and technical detail
  - Stories not portable outside GitHub
  - BMAD workflows deeply integrated with markdown files
  - Harder for AI agents to read/update GitHub Issues vs markdown

**Chosen hybrid approach** preserves BMAD's strengths (detailed technical docs) while gaining GitHub's strengths (collaboration and visibility).

**Related Tools:**
- GitHub CLI: `gh` (already installed: v2.45.0)
- BMAD Workflows: create-story, story-ready, dev-story, story-done
- Git Hooks: Can trigger sync on commit (future enhancement)

---

_Generated by BMad Architecture Workflow v1.0_
_Date: 2025-11-17_
_Updated: 2025-11-17 (Added AI-First Development, Print Template Designer, and GitHub Projects Integration)_
_For: Shtetl Platform_
_Project Type: Greenfield, Multi-Tenant SaaS_
_Development Methodology: BMAD v6 with AI-Assisted Development_
