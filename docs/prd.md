# shtetl - Product Requirements Document

**Author:** BMad
**Date:** 2025-11-17
**Version:** 1.0

---

## Executive Summary

Shtetl is a free, open-source platform that transforms how Jewish communities manage communications and schedules. The platform creates a centralized hub where **rabbinic authorities** publish authoritative Jewish calendar calculations (zmanim and dates), **synagogue administrators** configure minyan schedules using those authoritative streams, and **congregants** access accurate times through mobile apps, SMS, and automated alerts.

The core challenge: orthodox community management sits at the intersection of multi-tenant SaaS, precision time-based scheduling, and halachic accuracy requirements. Each synagogue has unique scheduling rules involving calendar primitives (holidays, fast days, special occasions), multiple minyanim per prayer type, and complex conditional logic—yet administrators (gaboim) are non-technical users who need complete control.

Shtetl solves this by enabling extreme customization without complexity explosion.

### What Makes This Special

**Extreme Customization Without Complexity** - Shtetl enables each synagogue to configure highly complex, unique minyan scheduling rules through an intuitive tree-based interface with real-time validation, guaranteeing 100% schedule coverage while serving non-technical administrators.

Unlike traditional scheduling systems that are either too rigid (form-only, no flexibility) or too complex (full programming required), Shtetl balances power and usability through:

1. **Dual DSL System** - Two specialized domain-specific languages optimized for different audiences: a technical interface for rabbinic authorities creating calculation algorithms, and a non-technical interface for gaboim building minyan schedules
2. **Hierarchical Primitive Cascade** - Calendar primitives inherit from global → country → state → city → shul levels, with enforcement preventing invalid modifications
3. **100% Coverage Validation** - Real-time validation engine ensures every non-optional minyan has a time for every day, preventing incomplete schedules
4. **Tree-Based Organization** - Clear mental model (Minyan Type → Instance → Rules) with visual drag-and-drop interface backed by powerful DSL rules

---

## Project Classification

**Technical Type:** SaaS B2B Platform
**Domain:** General (Religious Community Management)
**Complexity:** Medium

Shtetl is a multi-tenant SaaS platform serving Jewish communities worldwide. While the domain involves specialized knowledge (Jewish calendar, halachic timekeeping, orthodox community practices), it operates as a **general domain** product without regulatory compliance requirements. The halachic authority structure is a voluntary, traditional governance model rather than legal regulation.

**Technical Characteristics:**
- Multi-tenant architecture with complete synagogue autonomy
- Dual administrative interfaces (technical for authorities, non-technical for admins)
- Tree-based rule configuration with DSL backing
- Real-time validation and coverage guarantees
- Mobile-first congregant experience
- Open-source with community-driven development

**Product Classification:**
- **Platform Type:** B2B SaaS (organizations as customers)
- **User Segments:** 3 distinct personas with different needs
- **Pricing Model:** Free/open-source with optional managed hosting
- **Go-to-Market:** Community-driven adoption through design partners

**Reference Documents:**
- **Product Brief:** docs/brief.md
- **Domain Brief:** docs/domain-brief.md
- **Architecture:** docs/architecture.md
- **Technical Research:** docs/research-technical-2025-11-17.md

---

## Success Criteria

Success for Shtetl means communities experience **reliable, accurate schedules** that reduce administrative burden while maintaining halachic integrity.

**For Rabbinic Authorities:**
- Success means publishing authoritative calendar streams once annually that serve hundreds of subscribing communities
- Calculation methodologies are documented transparently with halachic sources cited
- Versioning and audit trails prevent confusion about which calculations are current

**For Synagogue Administrators (Gaboim):**
- Success means configuring complex minyan schedules independently without technical assistance
- 100% schedule coverage guaranteed - never publishing an incomplete timetable
- Generating formatted PDFs matching community standards (Hebrew RTL text, proper layouts)
- Reducing schedule maintenance from hours per week to minutes per update

**For Congregants (Kehilla Members):**
- Success means finding current minyan times within 30 seconds on mobile or web
- Receiving personalized wake-up alerts linked to actual davening times
- Trusting published times are accurate (no "panic calls" minutes before minyan)
- Accessing schedules offline when needed

**Platform Success Metrics:**
- At least 5 synagogues actively maintain schedules through the platform within 6 months
- 70%+ of active kehilla members subscribe to personalized minyan or shiur alerts
- At least one rabbinic authority publishes a calendar stream through Zmanim Provider Builder
- Automation hooks power at least one real-world facility action (e.g., HVAC scheduling) per participating synagogue

**Quality Indicators:**
- Zero halachic accuracy breaches (incorrect zmanim published)
- 60%+ of surveyed users report no longer calling gabbaim for schedule information
- 80%+ of users can find current minyan times within 30 seconds
- Gaboim report 60%+ reduction in time spent on manual notifications

These success criteria focus on **meaningful outcomes** rather than vanity metrics, connecting directly to the product's core value proposition of eliminating fragmented communications while maintaining halachic accuracy.

---

## Product Scope

### MVP - Minimum Viable Product

The MVP focuses on **core scheduling capabilities** that enable 3-5 synagogues to replace manual production of all timetables.

**Two Distinct Administrative UIs:**

**1. Zmanim Provider Builder (Rabbinic Authority Technical Interface)**
- Purpose: Enable recognized halachic authorities to create and publish comprehensive Jewish calendar calculation engines
- Target Users: Technically sophisticated users (rabbinic authorities with astronomical knowledge, technical advisors)
- Scope: Complete Jewish calendar calculation system encompassing both **times** and **dates/events**

Core Capabilities:
- Zmanim & Calendar Calculation DSL for defining astronomical and calendar algorithms
- Daily time calculations (Alot HaShachar, Netz, Shkiah, Tzait HaKochavim, Chatzot, Mincha times, Plag HaMincha, Sof Zman Shma/Tefillah, Shaot Zmaniyot, and all halachically significant times)
- Hebrew calendar & date calculations (Rosh Chodesh, Molad, Yom Tov dates, fast days, Chanukah, Purim, Special Shabbatot, Chol HaMoed, Sefira, Three Weeks/Nine Days, Bein HaZemanim, yahrtzeit calculations, leap year determination)
- Support for multiple calculation methodologies and halachic opinions (GRA, MGA, Baal Hatanya, etc.)
- Geographic precision (latitude, longitude, elevation, atmospheric refraction, time zones)
- Calendar stream publishing with version control and methodology documentation
- Formula documentation with halachic sources and astronomical basis
- Validation & testing tools to verify calculations against known reference data

**2. Shul Admin UI (Synagogue Schedule Management Interface)**
- Purpose: Enable gaboim to configure minyan schedules using published calendar streams
- Target Users: Non-technical synagogue administrators (gaboim)

Core Capabilities:
- Minyan Scheduling DSL with tree-based rule configuration (Minyan Type → Minyan Instance → Conditional Rules)
- Hierarchical primitive system with intelligent autocomplete:
  - **Global Primitives** (platform-controlled): Yom Tov, Fast Days, Rosh Chodesh, Special Shabbatot, Chanukah, Purim, etc.
  - **Country Primitives** (auto-selected): UK Bank Holidays, US Federal Holidays, Israel observances
  - **State/Province/City Primitives** (auto-selected based on location)
  - **Shul-Specific Primitives** (user-defined): Shul anniversary, local yahrzeits, custom events
- Primitive inheritance rules (Global → Country → State → City → Shul) with enforcement
- Real-time validation ensuring 100% schedule coverage
- Downshift formula input with intelligent autocomplete showing available primitives based on context
- Base calendar selection (choose authoritative calendar stream to follow)
- Local customization (apply Shul-specific offsets and adjustments to base zmanim)
- Hebrew RTL PDF generation (formatted timetables matching community standards)

**Additional MVP Core Features:**
- **Shiur & Event Listings:** Publish learning sessions and communal events with categories, speakers, and location metadata
- **User Subscriptions & Alerts:** Allow kehilla members to subscribe to specific minyanim or shiur categories and receive push/email notifications
- **Automation Hooks:** Integrate with MCP servers/Zapier to trigger facilities actions (e.g., HVAC on/off) based on schedule data
- **Mobile App Access:** iOS/Android front ends for browsing schedules, configuring reminders, and receiving alerts

**MVP Success Criteria:**
- At least 5 Shuls actively maintain their schedules through the platform
- Congregants rely on the mobile app or alerts for daily minyan attendance
- Automation hooks are powering at least one real-world facility action per participating Shul
- At least one rabbinic authority publishes calendar stream through Zmanim Provider Builder
- Feedback indicates improved schedule confidence

**Explicitly Out of Scope for MVP:**
- Multi-language translations beyond core English interface
- Comprehensive Shul portal (membership management, aliyos, yahrzeits)
- Full-featured mobile app with offline capabilities
- Public APIs & event hooks (webhook system)
- Shared learning libraries
- Template marketplace

### Growth Features (Post-MVP)

**Phase 2 Features:**
- **Comprehensive Shul Portal:** Complete membership management with contact information, payment tracking, aliyos scheduling, shaylos submission and tracking, yahrzeit management, and event coordination
- **Full-Featured Mobile App:** Comprehensive mobile application supporting all portal features including membership management, aliyos scheduling, shaylos submission, yahrzeit tracking, and event coordination with offline capabilities
- **Public APIs & Event Hooks:** Well-structured, publicly accessible APIs and event hooks making Shtetl data easily available to AI systems, MCP servers, Zapier/Make integrations, and third-party developers
- **Shared Learning Libraries:** Catalog shiur recordings, source sheets, and educational content linked to schedules and speakers
- **Multi-Language Support:** Hebrew/Yiddish interface and content localization for diverse communities
- **Template Marketplace:** Shuls share minyan schedule templates, rabbinic authorities share calculation methodologies

**Advanced Features (Phase 3):**
- Advanced Zmanim Builder features (more calculation methodologies, formula testing frameworks)
- Advanced Minyan Scheduling DSL features (calculated times, complex constraints, seasonal variations)
- Template customization system (colors, logos, custom layouts)
- Multi-season management (quarterly/annual planning)
- Collaboration features (multiple gaboim editing simultaneously)
- Analytics dashboard (attendance patterns, engagement metrics)

### Vision (Future)

**Long-term Vision:**

Shtetl transforms into the vibrant digital heartbeat of global Jewish life — a living ecosystem where every Shul and organization connects seamlessly through intelligent APIs and community-driven innovation.

**Vision Capabilities:**
- Find a minyan based on time and location anywhere in the world within seconds
- Subscribe to events from any shul and get automatic reminders via SMS, app notification, etc.
- Rabbinic authorities publish authoritative calendar calculations that automatically flow to subscribing communities
- Every Jewish community becomes a connected node in a worldwide network of shared collaboration, automated efficiency, and meaningful data
- Local autonomy is preserved while enabling global collaboration
- Community developers build rich integrations, automation, and custom features on top of Shtetl's open APIs
- Shtetl becomes the canonical source of truth for Jewish community schedules globally

---

## Innovation & Novel Patterns

Shtetl introduces **three novel architectural patterns** that differentiate it from traditional scheduling systems:

**1. Dual DSL System for Different Audiences**

Most systems force all users through a single interface. Shtetl recognizes that rabbinic authorities (technical users creating calculation algorithms) and gaboim (non-technical users building schedules) have fundamentally different needs.

Solution: Two completely separate DSL implementations optimized for each domain:
- **Zmanim Calculation DSL** - Highly technical, supports multiple halachic opinions, astronomical precision, geographic variations, version control for formulas
- **Minyan Scheduling DSL** - Non-technical, visual tree builder generates DSL behind the scenes, primitive-based conditions, real-time validation

**2. Hierarchical Primitive Cascade with Enforcement**

Challenge: Calendar primitives (holidays, special days) must cascade from global → regional → local while preventing invalid modifications.

Solution: Inherited primitive system where:
- Global Primitives (Yom Tov, Rosh Chodesh, fast days) - platform-controlled, immutable
- Country/State/City Primitives - auto-selected based on Shul location
- Shul-Specific Primitives - user-defined (anniversaries, local events)
- **Enforcement Rule:** Lower levels can add but NEVER remove inherited primitives
- Downshift autocomplete shows only available primitives based on context

This ensures consistency while enabling local customization.

**3. Real-Time Coverage Validation Engine**

Traditional scheduling systems allow incomplete configurations. Gaboim discover missing days only after publishing.

Solution: Validation engine that evaluates all rules against all days in real-time:
- Generates all days in range (accounting for Hebrew calendar)
- For each non-optional minyan, evaluates rules in priority order
- Reports missing coverage immediately: "⚠️ 15 days missing times for Shacharit First Minyan"
- **Prevents publishing** until 100% coverage achieved
- Visual feedback shows exactly which days need rules

This guarantees complete schedules and prevents errors.

### Validation Approach

**Validation Strategy:**

1. **Halachic Accuracy Validation**
   - Use established libraries as reference (KosherJava, Hebcal)
   - Import from authoritative rabbinic sources
   - Preview + approval workflow before publishing
   - Cross-reference multiple sources during testing
   - Audit trail for all changes (7-year retention)

2. **Coverage Validation (Automated)**
   - 100% of non-optional minyanim have times for all season days
   - Real-time validation as users edit rules
   - Detailed report showing which days/minyanim are missing
   - No publishing allowed until coverage = 100%

3. **Technical Validation**
   - Syntax validation for both DSLs
   - Unreachable rule detection (rules that can never match)
   - Priority conflict warnings
   - Output validation (visual regression testing against golden masters)
   - Security validation (tenant isolation, no data leakage)
   - Hebrew rendering validation (RTL text, proper fonts, no mojibake)

4. **Domain Expert Validation (Design Partner Program)**
   - **Beis Mordechai (Manchester)** - Primary design partner for Shul Admin UI
   - **2-3 additional Shuls** with diverse patterns (Chassidic, Yeshivish, Modern Orthodox, Sephardic)
   - **1-2 rabbinic authorities** for Zmanim Provider Builder pilot
   - Side-by-side validation: Original PDF/data vs. Generated output
   - Gabbai/authority usability testing for independent maintenance

---

## SaaS B2B Specific Requirements

### Multi-Tenancy Architecture

Shtetl implements **shared database multi-tenancy** with complete synagogue (Shul) autonomy:

**Tenant Model:**
- Each **Shul** is an independent tenant with complete data isolation
- **Shared infrastructure** - all tenants use same database, application servers, and codebase
- **Row-Level Security** - Application-level enforcement via tenant_id (shul_id) on all queries
- **Zero marginal cost** per Shul - adding tenants doesn't require new infrastructure

**Tenant Isolation:**
- Every database table has `shul_id` column for tenant data
- GORM scopes automatically filter queries by authenticated user's shul_id
- JWT claims include shul_id from Clerk user metadata
- Application middleware validates shul_id on every request
- Platform admins can access multiple tenants for support

**Tenant Configuration Hierarchy:**
```
Global Primitives (Platform-controlled, all tenants inherit)
    ↓
Country Primitives (Auto-selected based on Shul location)
    ↓
State/Province/City Primitives (Auto-selected)
    ↓
Shul-Specific Configuration (Independent per tenant)
    ├── Selected Calendar Stream (which rabbinic authority to follow)
    ├── Local Customizations (offsets, special rules)
    ├── Minyan Tree (unique schedule structure)
    └── Custom Primitives (Shul-specific events)
```

**Tenant Onboarding:**
1. Administrator creates Shul account (organization signup)
2. System auto-loads applicable primitives based on location (country, state, city)
3. Administrator selects base calendar stream (rabbinic authority allegiance)
4. Administrator builds minyan tree and configures rules
5. Validation engine ensures 100% coverage before publishing
6. Congregants (kehilla members) can view published schedules

### Authentication & Authorization

**Authentication Provider:** Clerk

**User Roles (per Shul):**
- **platform_admin** - Platform administrator (global access, can view/manage all Shuls for support)
- **rabbinic_authority** - Zmanim publishers (can create/publish calendar streams)
- **shul_admin** - Shul administrators/gaboim (manage their Shul's minyanim, full write access to their tenant)
- **kehilla** - Community members/congregants (read-only access to their Shul's schedules)

**Authorization Model:**
- **Single Clerk organization** for entire Shtetl platform (not per-Shul organizations)
- User metadata stores: `{ shul_id: "uuid", role: "shul_admin" }`
- JWT claims include shul_id for backend validation
- Application middleware enforces data isolation via shul_id

**Permission Matrix:**

| Role | Zmanim Provider Builder | Shul Admin UI | Kehilla API | Cross-Tenant Access |
|------|----------------------|---------------|-------------|---------------------|
| **platform_admin** | Full access | Full access (all Shuls) | Full access | Yes (support) |
| **rabbinic_authority** | Full access | N/A | Read own streams | No |
| **shul_admin** | Read streams | Full access (own Shul) | Read own Shul | No |
| **kehilla** | N/A | N/A | Read own Shul | No |

**Access Control Rules:**
- Users can belong to multiple Shuls (e.g., traveler subscribing to multiple communities)
- Users can have different roles in different Shuls (admin in one, kehilla in another)
- Rabbinic authorities can publish streams consumed by any Shul
- Platform admins bypass tenant isolation for support (with audit logging)

### Subscription Tiers

**MVP Tier Structure:**
- **Free/Open-Source:** Self-hosted deployment, community support
- **Managed Hosting (Future):** Paid tier with hosting, backups, support, SLA

MVP focuses on free/open-source model. Subscription tiers deferred to post-MVP based on community feedback.

### Integration Requirements

**Essential Integrations (MVP):**
- **Clerk API** - User authentication and organization management
- **SMS Provider** (TBD - Twilio or similar) - SMS alerts for congregants
- **Push Notifications** (TBD - FCM) - Mobile app notifications
- **Email** (TBD - Resend or SendGrid) - Email alerts and notifications

**Future Integrations (Post-MVP):**
- **MCP Servers** - Model Context Protocol integration for AI assistants
- **Zapier/Make** - Automation workflows (e.g., HVAC scheduling based on minyan times)
- **Calendar Sync** - Google Calendar, Apple Calendar, Outlook integration
- **SMS Broadcast** - Group messaging for emergency notifications
- **Community Tools** - Slack, Discord, WhatsApp integrations

### Platform Support

**Web Application (Admin Interfaces):**
- Modern browsers: Chrome, Firefox, Safari, Edge (last 2 versions)
- Responsive design: Desktop (1920x1080+), Tablet (768x1024+), Mobile web (375x667+)
- Formula input requires JavaScript enabled
- Hebrew text rendering requires Unicode font support

**Mobile Application (Congregant Access):**
- iOS 14+ (iPhone, iPad)
- Android 10+ (phones, tablets)
- React Native with Expo for cross-platform development
- Offline support for viewing cached schedules
- Push notification support

**Backend APIs:**
- REST API for all communication (web/mobile clients and internal service-to-service)
- Lambda-compatible architecture (start with Lambda, container escape hatch available)
- PostgreSQL 18 database
- Redis 8.4 caching layer

**Accessibility:**
- WCAG 2.1 Level AA compliance (goal for web interfaces)
- Screen reader support for congregant-facing features
- High contrast mode support
- Keyboard navigation support

### API Specification

**Base URL:** `https://api.shtetl.com/api/v1`

**Authentication:** Clerk JWT in `Authorization: Bearer <token>` header

**Key Endpoint Categories:**

**Kehilla Service (Public API for Congregants):**
```
GET    /api/v1/schedules/{shulId}/today         - Get today's minyan times
GET    /api/v1/schedules/{shulId}/week          - Get weekly schedule
GET    /api/v1/schedules/{shulId}/date/{date}   - Get specific date schedule
POST   /api/v1/subscriptions                    - Create subscription
PUT    /api/v1/subscriptions/{id}               - Update subscription
DELETE /api/v1/subscriptions/{id}               - Delete subscription
GET    /api/v1/shuls                            - Browse/search shuls
GET    /api/v1/shuls/{id}                       - Get shul details
```

**Shul Service (Admin API for Gaboim):**
```
GET    /api/v1/shuls/{shulId}                   - Get shul configuration
PUT    /api/v1/shuls/{shulId}                   - Update shul settings
GET    /api/v1/shuls/{shulId}/minyanim          - List all minyanim
POST   /api/v1/shuls/{shulId}/minyanim          - Create minyan
PUT    /api/v1/minyanim/{id}                    - Update minyan
DELETE /api/v1/minyanim/{id}                    - Delete minyan
POST   /api/v1/minyanim/{id}/rules              - Add rule to minyan
PUT    /api/v1/rules/{id}                       - Update rule
DELETE /api/v1/rules/{id}                       - Delete rule
POST   /api/v1/minyanim/{id}/validate-coverage  - Validate schedule coverage
POST   /api/v1/shuls/{shulId}/generate-pdf      - Generate timetable PDF
GET    /api/v1/primitives                       - Get available primitives
POST   /api/v1/shuls/{shulId}/primitives        - Create custom primitive
```

**Zmanim Service (Internal REST API):**
```
GET    /api/v1/zmanim/streams/{id}              - Get calendar stream by ID
POST   /api/v1/zmanim/calculate                 - Calculate zmanim for date/location
POST   /api/v1/zmanim/streams                   - Publish new calendar stream
PUT    /api/v1/zmanim/streams/{id}              - Update calendar stream
GET    /api/v1/zmanim/streams/{id}/audit        - Get audit trail (7-year retention)
```

**Standard Response Format:**
```json
{
  "data": { ... },
  "meta": {
    "timestamp": "2025-11-17T10:30:00Z",
    "version": "v1"
  }
}
```

**Error Response Format:**
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

---

## User Experience Principles

Shtetl serves three distinct user personas with fundamentally different needs. UX design must balance **power and simplicity** across these audiences.

### Core UX Philosophy

**"Complexity Hidden, Power Revealed"**

- **For Non-Technical Users (Gaboim):** Interface should feel like dragging cards, not writing code
- **For Technical Users (Authorities):** Provide IDE-like experience with autocomplete, validation, documentation
- **For Congregants (Kehilla):** Show only what's needed, when it's needed - zero cognitive load

### Design Principles by Persona

**1. Rabbinic Authorities (Zmanim Provider Builder)**

Visual Personality: **Technical, Precise, Academic**
- Dark theme optimized for long formula editing sessions
- Downshift formula input with context-aware autocomplete and inline documentation
- Side-by-side preview (formula → calculated output)
- Version diff view for comparing calculation methods
- Clear attribution to halachic sources (footnotes, citations)

Key Interactions:
- Formula editing with autocomplete (astronomical functions, halachic opinions)
- Real-time validation showing calculation accuracy vs. reference libraries
- Calendar stream publishing workflow with methodology documentation
- Version control interface (commit, rollback, compare versions)

**2. Synagogue Administrators (Shul Admin UI)**

Visual Personality: **Clean, Organized, Confidence-Building**
- Light theme with clear visual hierarchy
- Tree structure displayed as expandable cards
- Drag-and-drop for reordering rules
- Green checkmarks (✓) for validated sections, yellow warnings (⚠️) for gaps
- "Coverage: 85%" indicator always visible during editing

Key Interactions:
- Visual tree builder (click to add minyan → fill in details → add rules)
- Rule editor with primitive suggestions (type "Rosh" → autocomplete shows "RoshChodesh")
- Coverage validation panel showing missing days in plain language: "15 days need times for Shacharit First Minyan"
- PDF preview before publishing (see exactly what congregants will see)
- One-click "Clone from template" for new seasons

**3. Congregants (Kehilla Members)**

Visual Personality: **Minimal, Fast, Trustworthy**
- Mobile-first design (thumb-friendly tap targets)
- Today's times immediately visible (no scrolling)
- Large, readable fonts for times (especially for older users)
- Simple toggle switches for subscriptions
- Offline indicator when viewing cached data

Key Interactions:
- Pull to refresh (get latest schedule updates)
- Tap minyan card → toggle notification subscription
- Swipe left/right to navigate days
- Search by Shul name or location
- Subscribe to multiple Shuls (travelers, community members visiting family)

### Critical UX Flows

**Flow 1: Gabbai Creates First Minyan Schedule**
1. Land on empty tree → "Add Your First Minyan" prompt
2. Click "Shacharit" → Card appears with "Add Minyan Instance"
3. Name it "First Minyan (Ezrat Nashim)" → Click "Add Rules"
4. Type "RoshChodesh" → Autocomplete suggests, user selects → Enter time "8:15"
5. Type "Sunday" → Enter time "8:00" → Type "weekdays" → Enter time "7:30"
6. Coverage indicator shows "25% - 180 days missing times"
7. Keep adding rules until "100% - Ready to publish"
8. Click "Generate PDF" → Preview matches familiar format → "Publish"

**Flow 2: Congregant Subscribes to Alerts**
1. Open app → See today's minyan times for primary Shul
2. Tap "Shacharit First Minyan 7:30am" card
3. Toggle "Wake me up 30 minutes before" → Done
4. Receive push notification at 7:00am: "Shacharit at 7:30am (Beis Mordechai)"

**Flow 3: Authority Publishes Calendar Stream**
1. Open Zmanim Provider Builder → "Create New Stream"
2. Name: "Rabbi Cohen Manchester 5786" → Select methodology: "GRA calculations"
3. Formula input opens with template suggestions
4. Define alot calculation: `alot = sunrise - degrees(16.1)` (with autocomplete)
5. Continue defining all zmanim → Click "Validate"
6. System compares output vs. KosherJava → Shows accuracy report
7. Document methodology: "Using GRA opinion with standard refraction"
8. Publish stream → Available for Shuls to select

### Accessibility Considerations

- **Keyboard Navigation:** All admin interfaces fully keyboard-accessible
- **Screen Readers:** Semantic HTML, ARIA labels for tree structure, form validation feedback
- **High Contrast:** Support browser high contrast mode for congregant mobile app
- **Font Scaling:** Respect system font size preferences (critical for older users)
- **Hebrew RTL:** Proper bidirectional text support (Hebrew names mixed with English UI)

---

## Functional Requirements

This section defines **WHAT capabilities** the product must have. Every capability discussed in vision, scope, domain requirements, and project-specific sections is represented as a Functional Requirement below.

**Coverage Check:** FRs cover Zmanim Provider Builder, Shul Admin UI, Kehilla API, mobile apps, and all integrations discussed in MVP scope.

### User Account & Access Management

**FR1:** Users can create accounts with email or social authentication (Google, Apple)
**FR2:** Users can log in securely and maintain sessions across devices
**FR3:** Users can reset passwords via email verification
**FR4:** Users can update profile information and preferences
**FR5:** Platform administrators can manage user roles and permissions per Shul
**FR6:** Users can belong to multiple Shuls with different roles in each
**FR7:** Users can designate a primary Shul for default views

### Zmanim Provider Builder (Rabbinic Authority Interface)

**FR8:** Rabbinic authorities can create new calendar calculation streams
**FR9:** Authorities can define daily zmanim calculations using Zmanim Calculation DSL
**FR10:** System supports multiple halachic calculation opinions (GRA, MGA, Baal Hatanya, custom)
**FR11:** Authorities can define all Hebrew calendar dates and events (holidays, fast days, special occasions)
**FR12:** System accounts for geographic parameters (latitude, longitude, elevation, time zone, atmospheric refraction)
**FR13:** Authorities can document calculation methodologies with halachic source citations
**FR14:** System validates calculations against reference libraries (KosherJava, Hebcal)
**FR15:** Authorities can publish calendar streams with version control
**FR16:** System maintains version history and changelogs for formula changes
**FR17:** Authorities can compare calculation outputs between versions
**FR18:** Published streams are discoverable by Shuls (searchable by region, methodology, authority)
**FR19:** System maintains 7-year audit trail of all calculation changes
**FR20:** Authorities can export calculation streams to standard formats

### Shul Administration (Minyan Scheduling Interface)

**FR21:** Shul administrators can create new Shul accounts with location information
**FR22:** Administrators can select authoritative calendar stream to follow
**FR23:** Administrators can apply local offsets and customizations to base zmanim
**FR24:** Administrators can create minyan types (Shacharit, Mincha, Maariv, custom)
**FR25:** Administrators can create minyan instances under each type (First Minyan, Second Minyan, etc.)
**FR26:** Administrators can configure minyan metadata (location, optional flag, display order)
**FR27:** Administrators can create scheduling rules using Minyan Scheduling DSL
**FR28:** System provides Downshift formula input with autocomplete for rule DSL
**FR29:** System auto-suggests calendar primitives based on Shul's hierarchical context
**FR30:** Administrators can define custom Shul-specific primitives (anniversaries, local events)
**FR31:** System enforces primitive inheritance rules (cannot delete inherited global/regional primitives)
**FR32:** System validates schedule coverage in real-time as rules are edited
**FR33:** System generates detailed coverage reports showing missing days/minyanim
**FR34:** System prevents publishing until 100% coverage achieved for non-optional minyanim
**FR35:** Administrators can manually override auto-calculated rule priority via drag-and-drop
**FR36:** Administrators can mark minyanim as optional (conditional existence based on rules)
**FR37:** Administrators can preview generated schedules before publishing
**FR38:** Administrators can generate formatted PDF timetables with Hebrew RTL text support
**FR39:** PDF output matches community-standard layouts (multi-minyan display, location markers)
**FR40:** Administrators can export schedules to multiple formats (PDF, CSV, JSON)
**FR41:** System maintains audit trail of all schedule changes with user attribution
**FR42:** Administrators can clone minyan configurations for new seasons

### Hierarchical Primitive System

**FR43:** System maintains global calendar primitives (Yom Tov, Fast Days, Rosh Chodesh, Special Shabbatot, etc.)
**FR44:** System auto-loads country primitives based on Shul location
**FR45:** System auto-loads state/province primitives based on Shul location
**FR46:** System auto-loads city primitives based on Shul location
**FR47:** Primitives cascade with inheritance (Global → Country → State → City → Shul)
**FR48:** Lower-level administrators can add but not remove inherited primitives
**FR49:** Downshift autocomplete displays only applicable primitives based on context
**FR50:** System validates primitive references in rules (no orphaned references)

### Congregant (Kehilla) Access

**FR51:** Congregants can browse available Shuls by location or name
**FR52:** Congregants can view today's minyan times for any Shul
**FR53:** Congregants can view weekly schedule for any Shul
**FR54:** Congregants can view schedule for specific date
**FR55:** Congregants can search for minyanim by type, time, or location
**FR56:** Congregants can follow multiple Shuls
**FR57:** Congregants can subscribe to specific minyanim for notifications
**FR58:** Congregants can configure notification timing (e.g., 30 minutes before minyan)
**FR59:** Congregants can choose notification methods (push, SMS, email)
**FR60:** Congregants can manage all subscriptions from single dashboard
**FR61:** System sends personalized alerts based on subscriptions
**FR62:** Mobile app caches schedules for offline viewing
**FR63:** Congregants can access schedules via web browser (mobile-responsive)
**FR64:** Congregants can share schedule links with others

### Shiur & Event Management

**FR65:** Administrators can create shiur (learning session) entries
**FR66:** Administrators can categorize shiurim (Gemara, Halacha, Hashkafa, etc.)
**FR67:** Administrators can specify shiur metadata (speaker, location, recurring schedule)
**FR68:** Administrators can create communal event entries
**FR69:** Congregants can subscribe to shiur categories for alerts
**FR70:** Congregants can view shiur and event calendars

### Automation & Integration

**FR71:** System exposes webhook endpoints for schedule update notifications
**FR72:** System can trigger automation actions via webhook when minyan times are published
**FR73:** Administrators can configure automation rules (e.g., HVAC on 30 minutes before Shacharit)
**FR74:** System integrates with Clerk for authentication and user management
**FR75:** System sends SMS notifications via integrated SMS provider
**FR76:** System sends push notifications to mobile apps via FCM
**FR77:** System sends email notifications via integrated email provider

### Multi-Tenancy & Data Isolation

**FR78:** System isolates Shul data by tenant (shul_id) on all database operations
**FR79:** Users can only access data for Shuls they belong to (except platform admins)
**FR80:** Platform administrators can access any Shul for support purposes (with audit logging)
**FR81:** System validates tenant access on every API request via JWT claims
**FR82:** System prevents cross-tenant data leakage through API queries
**FR83:** Each Shul maintains completely independent minyan tree configuration

### Mobile Application

**FR84:** Mobile app provides iOS support (iOS 14+)
**FR85:** Mobile app provides Android support (Android 10+)
**FR86:** Mobile app displays today's minyan times on home screen
**FR87:** Mobile app supports pull-to-refresh for latest schedule updates
**FR88:** Mobile app provides swipe navigation for browsing days
**FR89:** Mobile app caches schedules for offline viewing
**FR90:** Mobile app displays offline indicator when viewing cached data
**FR91:** Mobile app supports push notifications for subscribed minyanim
**FR92:** Mobile app allows in-app subscription management
**FR93:** Mobile app supports multiple Shul profiles per user

### System Administration

**FR94:** Platform administrators can view all Shuls and users
**FR95:** Platform administrators can manage global primitives
**FR96:** Platform administrators can access any Shul for support (with audit trail)
**FR97:** System logs all administrative actions with timestamps and user attribution
**FR98:** System maintains 7-year retention for audit logs related to zmanim calculations
**FR99:** System provides health monitoring and error alerting
**FR100:** System supports database backups and disaster recovery

---

## Non-Functional Requirements

### Performance

**Halachic Accuracy (Critical - Non-Negotiable)**

**NFR-ACC-1:** Zmanim calculations must be accurate to within **±1 minute** for standard latitudes (±72°)
**NFR-ACC-2:** Hebrew calendar date calculations must be 100% accurate (no errors in holiday detection, leap years, molad calculations)
**NFR-ACC-3:** All calculations must be validated against reference libraries (KosherJava, Hebcal) during testing
**NFR-ACC-4:** System must delegate calculations to established astronomical libraries (not custom implementations)
**NFR-ACC-5:** Publishing incorrect zmanim is considered a critical failure requiring immediate rollback and notification

**API Response Times**

**NFR-PERF-1:** Schedule queries (today's times) must respond within **500ms** at p95
**NFR-PERF-2:** Coverage validation must complete within **2 seconds** for full season (365 days)
**NFR-PERF-3:** PDF generation must complete within **5 seconds** for standard timetable (8 weeks)
**NFR-PERF-4:** Autocomplete suggestions must appear within **100ms** of keystroke
**NFR-PERF-5:** Mobile app launch must display cached schedule within **1 second**

**Database Query Performance**

**NFR-PERF-6:** Schedule lookups by Shul + date must complete within **50ms** (cached queries)
**NFR-PERF-7:** Primitive resolution (Global → Country → State → City → Shul) must complete within **100ms**
**NFR-PERF-8:** Rule evaluation for single day must complete within **10ms**

**Caching Strategy**

**NFR-PERF-9:** Generated schedules must be cached in Redis for **24 hours** with auto-invalidation on changes
**NFR-PERF-10:** Calendar stream data must be cached for **7 days** (rarely changes)
**NFR-PERF-11:** Primitive lists must be cached for **1 hour** per Shul

### Security

**Authentication & Authorization**

**NFR-SEC-1:** All API endpoints must validate JWT tokens from Clerk on every request
**NFR-SEC-2:** JWT tokens must include shul_id claims for tenant isolation validation
**NFR-SEC-3:** Session tokens must expire after **24 hours** of inactivity
**NFR-SEC-4:** Password reset tokens must expire after **1 hour**
**NFR-SEC-5:** Multi-factor authentication (MFA) must be available for platform administrators

**Data Protection**

**NFR-SEC-6:** All data must be encrypted at rest (PostgreSQL encryption)
**NFR-SEC-7:** All API traffic must use TLS 1.3+ (HTTPS only)
**NFR-SEC-8:** Database credentials must be stored in secure environment variables (never in code)
**NFR-SEC-9:** Clerk API keys must be rotated every **90 days**
**NFR-SEC-10:** Personal data (email, phone) must be hashed or encrypted in database

**Tenant Isolation**

**NFR-SEC-11:** Every database query must include shul_id filter (enforced by GORM scopes)
**NFR-SEC-12:** API middleware must validate user's shul_id matches requested shul_id (except platform admins)
**NFR-SEC-13:** Cross-tenant data leakage is a critical security failure requiring immediate patching
**NFR-SEC-14:** Platform admin access to other tenants must be logged with full audit trail

**Input Validation**

**NFR-SEC-15:** All API inputs must be validated and sanitized (SQL injection protection)
**NFR-SEC-16:** DSL parsing must prevent code injection attacks
**NFR-SEC-17:** File uploads (if implemented) must validate file types and scan for malware
**NFR-SEC-18:** API rate limiting must prevent brute force attacks (**100 requests/minute per user**)

**Audit & Compliance**

**NFR-SEC-19:** All zmanim calculation changes must be logged with **7-year retention**
**NFR-SEC-20:** All admin actions must be logged with user attribution and timestamp
**NFR-SEC-21:** Audit logs must be immutable (append-only, no deletion)
**NFR-SEC-22:** Security incidents must trigger alerts to platform administrators

### Scalability

**Horizontal Scaling**

**NFR-SCALE-1:** All services must be stateless (no session data stored in memory)
**NFR-SCALE-2:** System must support horizontal scaling via load balancer
**NFR-SCALE-3:** Each service must handle **10,000 requests/minute** at production scale
**NFR-SCALE-4:** Database read replicas must be used for congregant schedule queries

**Tenant Scalability**

**NFR-SCALE-5:** System must support **1,000+ Shuls** on shared infrastructure without performance degradation
**NFR-SCALE-6:** Adding new tenant must have **zero marginal infrastructure cost**
**NFR-SCALE-7:** Each Shul must support **10,000+ congregants** viewing schedules
**NFR-SCALE-8:** System must handle **100,000+ concurrent mobile app users** at peak (Friday afternoon)

**Data Growth**

**NFR-SCALE-9:** Database must support **10 years** of historical schedule data per Shul
**NFR-SCALE-10:** Audit logs must support **7 years** of retention without query performance degradation
**NFR-SCALE-11:** Calendar streams must support **unlimited versions** with efficient storage (delta compression)

**Notification Scale**

**NFR-SCALE-12:** System must send **1 million+ notifications per day** at peak
**NFR-SCALE-13:** Notification delivery must be queued and processed asynchronously (message queue)
**NFR-SCALE-14:** Failed notification delivery must be retried with exponential backoff

### Reliability & Availability

**Uptime Requirements**

**NFR-REL-1:** API services must maintain **99.5% uptime** (excluding planned maintenance)
**NFR-REL-2:** Mobile app must function offline using cached data (graceful degradation)
**NFR-REL-3:** Database must have automated backups every **24 hours** with point-in-time recovery
**NFR-REL-4:** Critical failures must trigger alerts within **5 minutes**

**Error Handling**

**NFR-REL-5:** API errors must return structured error responses with actionable error codes
**NFR-REL-6:** Frontend must display user-friendly error messages (not raw errors)
**NFR-REL-7:** System must gracefully handle external service failures (SMS provider, email provider)
**NFR-REL-8:** Validation errors must show specific guidance ("Day X missing time for Minyan Y")

**Data Integrity**

**NFR-REL-9:** Database transactions must be ACID-compliant (no data corruption)
**NFR-REL-10:** Schedule changes must be atomic (all-or-nothing publishing)
**NFR-REL-11:** Coverage validation must prevent publishing incomplete schedules (cannot be bypassed)
**NFR-REL-12:** Audit logs must survive system crashes (write-ahead logging)

### Accessibility

**WCAG Compliance**

**NFR-ACCESS-1:** Web interfaces must meet WCAG 2.1 Level AA standards
**NFR-ACCESS-2:** All interactive elements must be keyboard-accessible
**NFR-ACCESS-3:** Screen readers must be able to navigate tree structure and forms
**NFR-ACCESS-4:** Form validation errors must be announced to screen readers
**NFR-ACCESS-5:** Color must not be the only means of conveying information (use icons + text)

**Internationalization**

**NFR-ACCESS-6:** System must support Hebrew RTL text rendering in PDFs and web
**NFR-ACCESS-7:** Hebrew text mixed with English must render correctly (bidirectional text)
**NFR-ACCESS-8:** Unicode fonts must support Hebrew characters without mojibake
**NFR-ACCESS-9:** System must support UTF-8 encoding throughout

**Mobile Accessibility**

**NFR-ACCESS-10:** Mobile app must respect system font size preferences
**NFR-ACCESS-11:** Tap targets must be at least **44x44 pixels** (thumb-friendly)
**NFR-ACCESS-12:** High contrast mode must be supported for visually impaired users

### Usability

**Learning Curve**

**NFR-USE-1:** Gaboim must be able to create first minyan schedule within **30 minutes** (with tutorial)
**NFR-USE-2:** Congregants must be able to subscribe to alerts within **2 minutes** of app launch
**NFR-USE-3:** System must provide contextual help and tooltips for all DSL keywords
**NFR-USE-4:** Coverage validation must provide actionable guidance (not just error counts)

**Error Prevention**

**NFR-USE-5:** System must warn before deleting minyanim or rules (confirmation dialog)
**NFR-USE-6:** Formula input must validate and highlight errors in real-time
**NFR-USE-7:** Coverage validation must prevent accidental publishing of incomplete schedules
**NFR-USE-8:** PDF preview must match final output exactly (WYSIWYG)

### Integration

**Third-Party Service Reliability**

**NFR-INT-1:** System must handle Clerk API downtime gracefully (cached JWT validation)
**NFR-INT-2:** SMS/Email provider failures must be logged and retried (not lost)
**NFR-INT-3:** External service timeouts must not block critical operations
**NFR-INT-4:** Push notification failures must be logged for debugging

**API Versioning**

**NFR-INT-5:** Public APIs must maintain backward compatibility for **1 year** after deprecation
**NFR-INT-6:** API version must be included in URL path (`/api/v1/...`)
**NFR-INT-7:** Breaking changes must be announced **90 days** in advance

### Maintainability

**Code Quality**

**NFR-MAINT-1:** Go backend code must maintain **80%+ test coverage**
**NFR-MAINT-2:** Critical paths (coverage validation, zmanim calculations) must have **95%+ test coverage**
**NFR-MAINT-3:** All DSL parsers must have comprehensive test suites covering edge cases
**NFR-MAINT-4:** Code must follow idiomatic Go patterns (pass golangci-lint)

**Documentation**

**NFR-MAINT-5:** All API endpoints must be documented in OpenAPI/Swagger format
**NFR-MAINT-6:** DSL syntax must be documented with examples and halachic sources
**NFR-MAINT-7:** Architecture decisions must be recorded in ADR (Architecture Decision Records)
**NFR-MAINT-8:** Database schema must be documented with ER diagrams

**Monitoring & Observability**

**NFR-MAINT-9:** All services must emit structured logs (JSON format with zerolog)
**NFR-MAINT-10:** Critical operations must be instrumented with metrics (latency, error rate, throughput)
**NFR-MAINT-11:** System must provide health check endpoints for monitoring
**NFR-MAINT-12:** Error logs must include request IDs for distributed tracing

---

## Summary

**Shtetl** is a free, open-source multi-tenant SaaS platform that transforms Jewish community communications by centralizing minyan schedules, zmanim, and alerts. The platform serves three distinct user personas—rabbinic authorities, synagogue administrators (gaboim), and congregants (kehilla members)—each with specialized interfaces optimized for their needs.

**Core Value Proposition:**
**"Extreme Customization Without Complexity"** - Enable each synagogue to configure highly complex, unique scheduling rules through intuitive interfaces while guaranteeing 100% schedule coverage and maintaining halachic accuracy.

**Key Innovations:**
1. **Dual DSL System** - Separate technical and non-technical domain-specific languages
2. **Hierarchical Primitive Cascade** - Global → regional → local inheritance with enforcement
3. **Real-Time Coverage Validation** - Prevents publishing incomplete schedules

**Total Functional Requirements:** 100 FRs covering all MVP capabilities
**Total Non-Functional Requirements:** 90+ NFRs covering performance, security, scalability, reliability, accessibility, usability, integration, and maintainability

**MVP Success Criteria:**
- 5+ Shuls actively maintaining schedules
- 70%+ kehilla member subscription rate
- 1+ rabbinic authority publishing calendar streams
- Zero halachic accuracy breaches

**Reference Documents:**
- Product Brief: docs/brief.md
- Domain Brief: docs/domain-brief.md
- Architecture: docs/architecture.md
- Technical Research: docs/research-technical-2025-11-17.md

---

_This PRD captures the essence of Shtetl - a platform that eliminates fragmented Jewish community communications while preserving local autonomy and maintaining critical halachic accuracy._

_Created through collaborative discovery between BMad and AI facilitator using the BMad Method v6._
