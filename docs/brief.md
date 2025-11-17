# Shtetl - Comprehensive Product & Domain Brief

**Generated:** 2025-11-16
**Domain:** Orthodox Community Management + Multi-Tenant Scheduling
**Complexity:** High
**Version:** 1.0

---

## Executive Summary

Shtetl is a free, open-source platform that unifies Jewish community communications into a centralized hub. The platform enables:

1. **Rabbinic authorities** to create and publish comprehensive Jewish calendar calculation engines (zmanim and dates)
2. **Synagogue administrators (gaboim)** to configure minyan schedules using authoritative calendar streams
3. **Congregants (mispallelim)** to access accurate schedules through mobile apps, SMS, and automated alerts

**Core Domain Challenge:** Shtetl operates at the intersection of orthodox community management, multi-tenant SaaS, and precision time-based scheduling. The complexity stems from enabling extreme customization (each Shul has unique requirements) while maintaining halachic accuracy and serving non-technical users.

**Key Innovation:** Two distinct administrative UIs, each with its own specialized DSL:
- **Zmanim Engine Builder** - Technical interface for creating calculation algorithms
- **Shul Admin UI** - Non-technical interface for building minyan schedules

Beyond scheduling, Shtetl offers comprehensive Shul management tools for tracking membership, aliyos, simchas, and yahrzeits. By providing each Shul with its own controlled space while enabling unified access through mobile apps, SMS updates, text-to-speech call-in numbers, MCP integrations, and automation hooks, Shtetl eliminates fragmented communication channels.

---

## Problem Statement

Local Jewish communities struggle to coordinate through a fragmented network of communication channels—WhatsApp broadcasts, hallway announcements, SMS, and email threads—to track minyan schedules, shiur rotations, and communal services.

**Current Pain Points:**
- Each Shul maintains its own version of truth, typically in printed formats posted on walls but inaccessible when needed on-the-go
- Schedules change weekly or even daily, making it challenging to stay current and set reliable reminders
- Gabbaim spend countless hours maintaining spreadsheets, calendars, and messaging systems
- Panicked calls minutes before Shacharis asking whether minyan starts at 6:20 or 6:30
- Lack of standardized APIs limits community developers from building automated solutions
- Demands grow for smart features like automated climate control before minyanim, schedule-based alerts, and location-aware notifications

**Domain-Specific Challenges:**
- **Extreme Customization:** Each Shul has unique scheduling rules involving calendar primitives (Rosh Chodesh, holidays, Selichot, fast days), multiple minyanim per prayer type, and complex conditional logic
- **Calculation Complexity:** Different halachic opinions for zmanim calculations, astronomical precision requirements, geographic variations
- **Non-Technical Users:** Gaboim need complete control but lack technical expertise
- **Critical Accuracy:** Publishing incorrect zmanim is a serious halachic breach that undermines trust

---

## Proposed Solution

Shtetl is a modular, open-source platform that preserves Shul autonomy while enabling opt-in participation in a shared community data network.

### Hierarchical Architecture

```
Zmanim Engine Builder (Rabbinic Authorities)
    ↓ publishes Calendar Streams
Regional Authoritative Zmanim/Calendar Data
    ↓ consumed by Shuls
Shul Admin UI (Gaboim)
    ↓ builds Minyan Schedules
Published Timetables & Alerts
    ↓ distributed to
Congregants (Mobile Apps, SMS, Web)
```

### Core Capabilities

**For Rabbinic Authorities:**
- Create comprehensive Jewish calendar calculation engines
- Publish authoritative zmanim streams for regions/communities
- Support multiple halachic opinions and calculation methodologies

**For Synagogue Administrators:**
- Select authoritative calendar stream to follow
- Configure minyan schedules using tree-based rule system
- Generate formatted timetables (PDF, web) with Hebrew RTL text
- Apply local customizations while maintaining base accuracy

**For Congregants:**
- Access accurate schedules through mobile apps, SMS alerts, email notifications
- Subscribe to specific minyanim or shiur categories
- Receive personalized wake-up alarms linked to actual davening times
- Automation triggers for facility control (HVAC, lighting)

### Technical Approach

- **Multi-Tenant SaaS** with complete Shul autonomy
- **Tree-based rule configuration** with real-time validation
- **Dual DSL system** - one for calculations, one for scheduling
- **Monaco editor** with intelligent autocomplete
- **React-PDF** for Hebrew document generation
- **Delegation to authoritative sources** (KosherJava/Hebcal as inspiration)

---

## Domain Context

### Regulatory Landscape

**Halachic Authority Structure:**
This is NOT government regulation but rather a hierarchical authority system:

- **Regional Rabbinic Authorities** - One or more recognized poskim (halachic decisors) in each geographic area publish authoritative zmanim calculations annually
- **Shul Allegiance** - Individual synagogues choose which rabbinic authority to follow based on their denominational/halachic alignment (Chassidic, Yeshivish, Modern Orthodox, Sephardic)
- **Local Adaptation** - Shuls may apply minor adjustments based on local minhag (custom) or practical considerations

**Critical Insight:** This is a **soft governance model** where authority is voluntary, traditional, and relationship-based. However, publishing incorrect zmanim would be considered a serious halachic breach.

### Key Stakeholders

1. **Regional Rabbinic Authorities / Zmanim Publishers**
   - Publish annual base zmanim calculations for their region
   - Serve as authoritative sources that Shuls reference
2. **Shul Administrators (Gaboim)**
   - NON-TECHNICAL users who need simple, intuitive interfaces
   - Responsible for configuring their Shul's schedules
   - Need to generate timetables in various formats
   - Must maintain accuracy while managing complexity
3. **Congregants (Mispallelim)**
   - Rely on published times being correct
   - Access through mobile apps, SMS, printed timetables
   - Expect consistency across channels
4. **Community Developers**
   - May build integrations, automation, custom features
   - Need clear APIs and extension points

---

## Target Users

### Primary User Segment: Congregation Members
- **Demographic/Firmographic Profile:** Mispallelim in Jewish communities of all sizes, including regular members, guests, travelers, and anyone seeking to daven in any minyan
- **Current Behaviors & Workflows:** Currently juggle multiple communication channels including WhatsApp groups, printed bulletins, personal inquiries to gabbaim
- **Needs & Pain Points:** Need consistent access to accurate zmanim, minyan times, and shiur schedules in real-time; experience frequent frustration with fragmented communications
- **Goals:** Easily find and attend suitable minyanim with personalized wake-up alarms linked to actual davening times, efficiently manage learning commitments, reduce administrative overhead for gaboim through automation

---

## Goals & Success Metrics

### Business Objectives
- Enable 3-5 Shuls to replace manual production of all timetables within 6 months of launch
- Increase Shul efficiency by centralizing 80% of communal schedule updates into Shtetl within 6 months
- Reduce administrator workload by cutting time spent on manual notifications by 60% per participating Shuls
- Foster open-source adoption with a vibrant community of volunteer developers contributing features and integrations

### User Success Metrics
- At least 70% of active kehilla members (congregants) subscribe to personalized minyan or shiur alerts
- 80% of users can find current minyan times within 30 seconds of opening the app
- 60% of surveyed users report they no longer need to call gabbaim for schedule information

### Key Performance Indicators (KPIs)
- **Shul Onboarding Rate:** Number of active Shul spaces created vs. outreach targets (goal: 15 by month 6)
- **User Engagement:** Monthly active users across mobile app, SMS, and web channels (goal: 70% of registered members)
- **Contribution Velocity:** Pull requests merged per quarter from community contributors

---

## MVP Scope

### Core Features (Must Have)

#### Two Distinct Administrative UIs

**1. Zmanim Engine Builder (Rabbinic Authority Technical Interface)**

- **Purpose:** Enable recognized halachic authorities and their technical advisors to create and publish comprehensive Jewish calendar calculation engines including all times, dates, and events
- **Target Users:** Technically sophisticated users (rabbinic authorities with astronomical knowledge, technical advisors, experts in halachic timekeeping)
- **Scope:** Complete Jewish calendar calculation system encompassing both **times** and **dates/events**

**Zmanim & Calendar Calculation DSL:** Highly technical domain-specific language for defining astronomical and calendar algorithms

**Daily Time Calculations (Zmanim):**
- Alot HaShachar (dawn) - multiple opinions (16.1°, 18°, 19.8°, etc.)
- Misheyakir (earliest time for Tallit/Tefillin) - multiple opinions
- Netz (sunrise) - with elevation and refraction adjustments
- Sof Zman Shma (latest Shema) - GRA, MGA, and other opinions
- Sof Zman Tefillah (latest Shacharit) - multiple calculation methods
- Chatzot (solar noon/midday)
- Mincha Gedolah (earliest Mincha) - half hour after Chatzot
- Mincha Ketanah (preferred Mincha) - 9.5 shaot zmaniyot
- Plag HaMincha (proportional afternoon division) - 10.75 shaot zmaniyot
- Shkiah (sunset) - with elevation and refraction
- Tzait HaKochavim (nightfall) - multiple opinions (3 stars, degrees below horizon)
- Rabbeinu Tam time - 72 minutes, or degree-based calculations
- Shaot Zmaniyot (proportional hours) - multiple calculation methods
- And all other halachically significant times throughout the day

**Hebrew Calendar & Date Calculations:**
- Rosh Chodesh (new month) determination
- Molad (lunar conjunction) calculations
- Yom Tov (holiday) dates - Pesach, Shavuot, Rosh Hashana, Yom Kippur, Sukkot, etc.
- Fast days - Tisha B'Av, 17th of Tammuz, Tzom Gedaliah, 10th of Tevet, Ta'anit Esther, Fast of the Firstborn
- Chanukah, Purim, Shushan Purim
- Special Shabbatot - Shabbat HaGadol, Shabbat Shuva, Shabbat Zachor, Shabbat Parah, etc.
- Chol HaMoed (intermediate festival days)
- Sefira counting (Counting of the Omer)
- Three Weeks, Nine Days restrictions
- Bein HaZemanim (seminary break periods) - community-specific
- Yahrtzeit calculations (Hebrew date anniversaries)
- Hebrew leap year determination (19-year cycle)
- Tekufot (seasonal transition points)
- Birkat HaChama (sun blessing - 28-year cycle)
- Any other calendar events and special days

**Technical Capabilities:**
- **Support for Multiple Calculation Methodologies:** Accommodate different halachic opinions and astronomical approaches unknown at design time
- **Geographic Precision:** Account for latitude, longitude, elevation, atmospheric refraction, time zones
- **Extensible Formula System:** Allow experts to define new calculation methods as halachic understanding evolves
- **API Integration Reference:** Use KosherJava API and Hebcal API as inspiration for comprehensive coverage of calculations needed
- **Multi-language Support:** DSL keywords and documentation in English, Hebrew, Yiddish
- **Validation & Testing:** Built-in tools to verify calculations against known reference data and existing APIs
- **Version Control:** Track formula changes and allow comparison between calculation versions
- **Formula Documentation:** Inline comments explaining halachic sources and astronomical basis

**Calendar Stream Publishing:**
- Create named calculation streams (e.g., "Rabbi X's Manchester Calendar 5786")
- Complete set of daily zmanim for entire Hebrew year
- All holidays, fast days, and special occasions with proper dates
- Methodology documentation (which opinions/calculations used for each time/date)
- Geographic applicability (regions where calculations are valid)
- Seasonal adjustments and special cases
- Version history and changelog

**Publication Workflow:**
- Annual/seasonal publication with version control
- Audit trails and peer review capabilities
- Export to standard formats

**Expert Collaboration:**
- Interface designed for consultation with industry experts in astronomical halacha (posek zmanim specialists) and calendar authorities

---

**2. Shul Admin UI (Synagogue Schedule Management Interface)**

- **Purpose:** Enable gaboim to configure their Shul's minyan schedules using published calendar streams as base data
- **Target Users:** Non-technical synagogue administrators (gaboim)

**Minyan Scheduling DSL:** Custom domain-specific language for defining minyan rules and schedules
- Multi-language support for DSL keywords (English, Hebrew, Yiddish, etc.)
- Tree-based rule configuration (Minyan Type → Minyan Instance → Conditional Rules)
- **Hierarchical Primitive System** with intelligent autocomplete:
  - **Global Primitives** (always available, platform-controlled): Yom Tov, Fast Days, Rosh Chodesh, Special Shabbatot, Chanukah, Purim, Chol HaMoed, Lag BaOmer, Tu B'Shvat, Tu B'Av, Sefira, Three Weeks, Nine Days, Selichot, Bein HaZemanim, etc.
  - **Country Primitives** (auto-selected based on Shul's country): UK Bank Holidays, US Federal Holidays, Israel observances, etc.
  - **State/Province Primitives** (auto-selected based on Shul's state/province): State holidays, regional observances
  - **City Primitives** (auto-selected based on Shul's city): City-specific holidays, local commemorations
  - **Shul-Specific Primitives** (user-defined): Shul anniversary, local yahrzeits, custom events
- **Primitive Inheritance Rules:**
  - Primitives cascade: Global → Country → State → City → Shul
  - Lower levels can add but not remove inherited primitives
  - Cannot override or delete platform-controlled global primitives (ensures consistency)
  - Can optionally hide country/state/city primitives if not applicable
- Real-time validation ensuring 100% schedule coverage
- Monaco editor with intelligent autocomplete showing available primitives based on context

**Key Features:**
- **Base Calendar Selection:** Choose which authoritative calendar stream to follow
- **Local Customization:** Apply Shul-specific offsets and adjustments to base zmanim
- **Tree Structure Configuration:**
  ```
  Shul
  ├── Shacharit
  │   ├── First Minyan (Ezrat Nashim)
  │   │   ├── Rule: RoshChodesh and Sunday → 8:15
  │   │   ├── Rule: RoshChodesh → 8:15
  │   │   ├── Rule: Sunday → 8:30
  │   │   └── Rule: weekdays → 7:30
  │   ├── Second Minyan (Main Hall)
  │   └── Third Minyan (Optional - Holidays only)
  ├── Mincha
  └── Maariv
  ```
- **Validation Engine:** Real-time coverage validation - ensures every non-optional minyan has time for every day of the season
- **PDF Generation:** Produce formatted timetables matching community standards (Hebrew RTL text, multi-minyan layouts like "8:30* / 8:00 / 7:40")

**Key Distinction:** The Zmanim Engine Builder creates the *source calculation streams*, while the Shul Admin UI *consumes* those streams and builds minyan schedules from them. Each has its own specialized DSL optimized for its purpose.

---

#### Additional Core Features

- **Shiur & Event Listings:** Publish learning sessions and communal events with categories, speakers, and location metadata
- **User Subscriptions & Alerts:** Allow kehilla members (congregants) to subscribe to specific minyanim or shiur categories and receive push/email notifications
- **Automation Hooks:** Integrate with MCP servers/Zapier to trigger facilities actions (e.g., HVAC on/off) based on schedule data
- **Mobile App Access:** Provide iOS/Android front ends for browsing schedules, configuring reminders, and receiving alerts

### Out of Scope for MVP
- Anything other than the zmanim features detailed above
- Multi-language translations beyond core English interface

### MVP Success Criteria
Shtetl MVP is successful when:
- At least five Shuls actively maintain their schedules through the platform
- Congregants rely on the mobile app or alerts for daily minyan attendance
- Automation hooks are powering at least one real-world facility action per participating Shul
- Feedback indicates improved schedule confidence
- At least one rabbinic authority publishes calendar stream through Zmanim Engine Builder

---

## Architecture & Technical Approach

### Critical Architectural Decision: Dual DSL + Tree Structure

**1. Zmanim Calculation DSL (Technical)**
- For rabbinic authorities to define astronomical and calendar algorithms
- Highly technical, extensible, version-controlled
- Visual, with autocomplete, validation-first
- Outputs: Complete calendar streams (daily zmanim + all dates/events for year)

**2. Minyan Scheduling DSL (Non-Technical)**
- For gaboim to define minyan rules
- Visual, with autocomplete, validation-first
- Consumes: Published calendar streams
- Outputs: Minyan schedules with 100% coverage guarantee

### Tree-Based Rule Configuration Model

```
Shul
├── Minyan Type (Shacharit, Mincha, Maariv)
│   ├── Minyan Instance (First, Second, Third)
│   │   ├── Metadata (location, optional flag)
│   │   └── Rules (condition → time mapping)
│   │       ├── Priority auto-calculated by specificity
│   │       └── Manual override via drag-and-drop
```

### Key Architectural Components

1. **Primitive System** - Global and community-specific calendar primitives (RoshChodesh, FastDays, Selichot, BankHolidays, etc.)
2. **Validation Engine** - Ensures every non-optional minyan has time for every day in season
3. **Rule Resolution** - Auto-prioritized by specificity (most specific condition wins)
4. **Coverage Calculator** - Real-time feedback on which days are missing times
5. **Calendar Stream Registry** - Published streams from rabbinic authorities, searchable by region/methodology

### Technology Stack

- **Frontend:** React + Monaco Editor (DSL IDE with autocomplete)
- **Mobile:** React Native for cross-platform mobile apps
- **Backend:** Golang with modular service architecture, PEG parser for DSLs
  - **Rationale:** Strong typing makes codebase more AI-friendly for AI-assisted development and maintenance; superior performance for concurrent operations; excellent for building robust DSL parsers; explicit error handling improves code quality
- **Database:** MongoDB (tree structure, flexible schema), Redis for caching
  - **Rationale:** Vendor-neutral database choice (vs. DynamoDB) enables future migration to Kubernetes/self-hosted infrastructure when AWS costs become prohibitive; flexible schema suits evolving requirements; excellent for hierarchical data structures (tree-based minyan configurations, primitive cascading)
- **Calendar:** Hebrew calendar libraries for primitive evaluation
- **PDF Generation:** React-PDF (Hebrew RTL support)
- **Authentication:** Clerk for user management and authentication
- **Hosting/Infrastructure:** AWS Serverless with Terraform for infrastructure as code
  - **Strategy:** Start with AWS Serverless for rapid MVP deployment and low initial costs; design for portability to enable future Kubernetes migration when economics favor it
  - **Portability Design:** Containerize all services, abstract cloud-specific services behind interfaces, use MongoDB instead of DynamoDB for database portability
- **DevOps:** GitHub Repo/Projects/Actions for CI/CD and project management
- **Integrations:** Zapier/MCP, Slack, Notion for automation and workflow integration

### Multi-Tenant Strategy
- Shared database with tenant isolation via Shul ID
- Row-Level Security (RLS) for data isolation
- Each Shul maintains independent minyan tree
- Primitives can be global (RoshChodesh) or community-specific (Selichot dates)
- Zero marginal cost per Shul

---

## Development Approach

### MVP Development Timeline (8 Weeks)

**Phase 1: Rules Engine (Weeks 1-4)**
- Build tree-based UI for creating minyan structure
- Implement rule editor with condition → time mapping
- Build validation engine with coverage calculator
- Import base zmanim data (CSV → MongoDB)
- Calendar primitive system
- Output: Working rule configuration system for Shul Admin UI

**Phase 2: PDF Renderer & Zmanim Builder Foundation (Weeks 5-6)**
- Execute rules to generate full season schedule
- Implement PDF template matching Beis Mordechai example
- Hebrew text support (RTL rendering)
- Basic Zmanim Engine Builder UI (formula input, validation)
- Output: Generated PDF timetable + foundation for authority calculations

**Phase 3: Testing & Pilot (Weeks 7-8)**
- Test with real Shul data (Beis Mordechai)
- Visual regression testing (output matches input)
- Coverage validation testing
- User acceptance testing with gabbai
- Pilot Zmanim Engine Builder with one rabbinic authority

### Post-MVP Expansion
- Each additional Shul: ~1-2 weeks (assuming similar patterns)
- Advanced Zmanim Builder features: 2-3 weeks
- Advanced Minyan Scheduling DSL features (calculated times, constraints): 2-3 weeks
- Template customization system: 3-4 weeks
- Mobile app integration: 4-6 weeks
- Comprehensive Shul portal (membership, aliyos, yahrzeits): 8-12 weeks

### Required Expertise
- **Backend:** Go, MongoDB, parser design (PEG/ANTLR), Hebrew calendar libraries
- **Frontend:** React, tree UI, Monaco editor, drag-and-drop
- **Domain:** Jewish calendar, astronomical halacha, Shul operations, Hebrew typography
- **Testing:** Visual regression, RTL validation, UAT with gaboim and rabbinic authorities

---

## Safety & Risk Considerations

### Critical: Accuracy is Non-Negotiable

Publishing incorrect zmanim is considered a serious halachic breach that could:
- Cause people to daven at wrong times (missing zmanim windows)
- Undermine trust in the entire platform
- Damage the reputation of the Shul and rabbinic authority
- Lead to abandonment of the system

**Therefore:**
- Calculations must be delegated to authoritative sources or created by recognized experts using Zmanim Engine Builder
- Use KosherJava/Hebcal as reference/inspiration (not necessarily direct dependencies)
- Any user customizations must be clearly additive/transparent (offsets, not formula replacement)
- Validation and preview mechanisms are essential
- Audit trails for who changed what and when (7-year retention)
- Preview + approval workflow before publishing

### Identified Risks & Mitigation

**1. DSL Complexity Creep**
- **Risk:** DSLs become too complex for target users
- **Mitigation:** Start minimal, add features only when 3+ users request, maintain "simple mode" for 80% of use cases

**2. Hebrew Text Rendering Issues**
- **Risk:** RTL text, Unicode, special characters render incorrectly in PDFs
- **Mitigation:** Use battle-tested libraries (React-PDF), golden master test suite, test with real Shul PDFs from day one

**3. Zmanim Calculation Accuracy**
- **Risk:** Incorrect zmanim due to calculation errors or data import issues
- **Mitigation:** Validation tools, reference data comparison, preview workflows, audit trails

**4. Adoption Resistance**
- **Risk:** Gaboim prefer familiar tools (Excel, Word) over new system
- **Mitigation:** Design partner program (2-3 Shuls), white-glove onboarding, demonstrate time savings, export to Excel/PDF preserves fallback

**5. Scale Complexity**
- **Risk:** Supporting 50+ Shuls with vastly different patterns reveals architecture limitations
- **Mitigation:** Modular architecture, plugin system, template marketplace, monitor patterns proactively

---

## Validation Strategy

### Halachic Accuracy Validation
- Use established libraries as reference (KosherJava, Hebcal)
- Import from authoritative rabbinic sources
- Preview + approval workflow before publishing
- Cross-reference multiple sources during testing
- Audit trail for all changes

### Technical Validation
- **Coverage:** 100% of non-optional minyanim have times for all season days
- **Rules:** Syntax validation, unreachable rule detection, priority conflict warnings
- **Output:** Visual regression testing against golden masters
- **Security:** Tenant isolation validation, no data leakage
- **Hebrew Rendering:** RTL text, proper fonts, no mojibake

### Domain Expert Validation

**Design Partner Program:**
- **Beis Mordechai (Manchester)** - Primary design partner for Shul Admin UI
- **2-3 additional Shuls** with diverse patterns (Chassidic, Yeshivish, Modern Orthodox, Sephardic)
- **1-2 rabbinic authorities** for Zmanim Engine Builder pilot
- Side-by-side validation: Original PDF/data vs. Generated output
- Gabbai/authority usability testing for independent maintenance

---

## Post-MVP Vision

### Phase 2 Features
- **Comprehensive Shul Portal:** Complete membership management with contact information, payment tracking, aliyos scheduling, shaylos submission and tracking, yahrzeit management, and event coordination
- **Full-Featured Mobile App:** Comprehensive mobile application supporting all portal features including membership management, aliyos scheduling, shaylos submission, yahrzeit tracking, and event coordination with offline capabilities
- **Public APIs & Event Hooks:** Well-structured, publicly accessible APIs and event hooks making Shtetl data easily available to AI systems, MCP servers, Zapier/Make integrations, and third-party developers
- **Shared Learning Libraries:** Catalog shiur recordings, source sheets, and educational content linked to schedules and speakers
- **Multi-Language Support:** Hebrew/Yiddish interface and content localization for diverse communities
- **Template Marketplace:** Shuls share minyan schedule templates, rabbinic authorities share calculation methodologies

### Long-term Vision

Shtetl transforms into the vibrant digital heartbeat of global Jewish life — a living ecosystem where every Shul and organization connects seamlessly through intelligent APIs and community-driven innovation. Imagine a world where:

- You can find a minyan based on time and location anywhere in the world within seconds
- Subscribe to events from any shul and get automatic reminders via SMS, app notification, etc.
- Rabbinic authorities publish authoritative calendar calculations that automatically flow to subscribing communities
- Every Jewish community becomes a connected node in a worldwide network of shared collaboration, automated efficiency, and meaningful data
- Local autonomy is preserved while enabling global collaboration

---

## Constraints & Assumptions

### Constraints
- **Budget:** Bootstrap/open-source funding; rely on volunteer contributions and modest operational budget for hosting and core maintenance (~$300/month for MVP)
- **Timeline:** Aim to release MVP within 6-12 months, aligned with upcoming High Holiday season when scheduling pressures peak
- **Resources:** Core team of a few developers plus volunteer contributors for integrations; Shul admins providing domain input
- **Technical:** Must accommodate diverse Shul data formats and handle zmanim calculations with halachic accuracy across time zones

### Key Assumptions
- Shuls are willing to centralize schedules if they retain autonomy over their space and branding
- Rabbinic authorities are willing to publish calendar streams through Zmanim Engine Builder
- Community members will adopt mobile/apps and alert subscriptions if information remains accurate and timely
- Volunteer developers will contribute integrations if the project offers clear APIs and governance

---

## Industry Standards & References

### Zmanim Calculation Standards
- **KosherJava Zmanim API** - Open source (LGPL 2.1), industry-standard library with 157+ zmanim calculations
- **Hebcal API** - REST API alternative for zmanim calculations
- **Hebrew Calendar Libraries** - Accurate Hebrew date calculations, holiday detection

### Document Generation Standards
- **React-PDF** - Modern, open-source PDF generation for web/mobile
- **Monaco Editor** - VS Code's editor engine for DSL/code editing with autocomplete
- **Right-to-Left (RTL) Text Support** - Proper Hebrew text rendering

### Multi-Tenant SaaS Patterns
- Row-Level Security (RLS) for tenant isolation
- Shared database with tenant_id column approach
- Tiered configuration inheritance (global → regional → Shul → instance)

### References
- [KosherJava Zmanim API](https://kosherjava.com/zmanim-project/) - Open source zmanim library (LGPL 2.1)
- [Hebcal API](https://www.hebcal.com/) - REST API for zmanim calculations
- [React-PDF](https://react-pdf.org/) - PDF generation library with RTL support
- [Monaco Editor](https://microsoft.github.io/monaco-editor/) - VS Code editor engine
- Multi-Tenant SaaS Architecture Patterns (Microsoft Azure docs)
- Document Generation Best Practices (Windward, Docupilot)

---

## Appendices

### Research Summary

**Web Research Conducted:**
1. Multi-tenant SaaS configuration patterns → Shared database with RLS recommended
2. Template builders for non-technical users → Monaco editor + constrained customization
3. Zmanim calculation APIs → KosherJava (157+ calculations), Hebcal REST API

**Key Technical Findings:**
- React-PDF supports Hebrew RTL text rendering
- Monaco editor provides autocomplete and syntax highlighting out-of-box
- Go PEG parsers (participle) suitable for DSL implementation

### Critical User Insights

1. **"Each synagogue has their own calculations, formulas, logic. If the system isn't capable of dealing with all these complexities, it just won't be used."**
   - Led to dual DSL approach: one for calculations, one for scheduling

2. **"We need primitives like Selichot days, Rosh Chodesh, holidays. Each community would have their own primitives."**
   - Informed primitive system design (global + community-specific)

3. **"Tree structure: Shacharit → First Minyan → Rules for Sunday, Rosh Chodesh, etc. With validation that creates minyan time for whole year."**
   - Defined core architecture: tree + coverage validation

4. **"Zmanim Engine Builder needs to handle all different algorithms not only for times but for days such as Yom Tov, fast days and all other events that make up a Jewish calendar."**
   - Expanded scope to complete calendar calculation system, not just daily times

### Example: Beis Mordechai Manchester

**Provided PDF shows:**
- Hebrew RTL text throughout
- Multi-minyan display format: "8:30* / 8:00 / 7:40" (asterisk for Ezrat Nashim)
- Weekly parsha-based layout
- Multiple minyanim per prayer type with location markers
- Informed PDF rendering requirements and layout structure

---

## Next Steps

1. **Build Initial Team:** Assemble initial leadership team including developers, business analysts, architects, DevOps, and QA specialists
2. **Establish Project Structure:** Create weekly meeting cadence and organizational structure allowing developers to balance work and other responsibilities
3. **Prepare Specifications:** Use BMad methodology to create comprehensive PRD, architecture, and UX design
4. **Setup Contribution Infrastructure:** Prepare Git repositories and development tooling to be contribution-ready
5. **Onboard Design Partners:** Recruit 2-3 Shuls + 1-2 rabbinic authorities as design partners to validate requirements
6. **Establish Collaboration Framework:** Set up tooling and processes for effective collaboration between core team, community contributors, Shul partners, and rabbinic authorities

---

_This comprehensive brief integrates product vision, domain research, and technical architecture. It should be referenced during PRD creation, architecture design, UX design, and implementation planning._
