# Domain Brief - shtetl

Generated: 2025-11-16
Domain: Orthodox Community Management + Multi-Tenant Scheduling
Complexity: High

## Executive Summary

Shtetl faces a unique domain challenge at the intersection of orthodox community management, multi-tenant SaaS, and precision time-based scheduling. The core complexity stems from enabling each synagogue (Shul) to configure highly customized minyan (prayer service) schedules while maintaining halachic accuracy and serving non-technical administrators (gaboim).

**Key Domain Findings:**

1. **Hierarchical Authority Structure** - Regional rabbinic authorities publish base zmanim (prayer times) annually, which individual Shuls reference and customize based on their denominational allegiance and local customs.

2. **Extreme Customization Requirements** - Each Shul has unique scheduling rules involving calendar primitives (Rosh Chodesh, holidays, Selichot, fast days, bank holidays), multiple minyanim per prayer type, and complex conditional logic that must cover every day of the year.

3. **Critical Accuracy Constraint** - Publishing incorrect zmanim is considered a serious halachic breach. The system must delegate calculations to authoritative sources.

4. **Non-Technical User Mandate** - Gaboim are not technical users but need complete control over complex rule configurations. Solution: Hierarchical tree structure (Minyan Type → Instance → Rules) with validation-first design and intelligent DSL editor.

5. **Document Generation Challenge** - Output must match existing PDF timetables with Hebrew RTL text, multi-minyan formatting (8:30* / 8:00 / 7:40), and location markers, while being generated from web/mobile platforms without Word licensing.

**Recommended Architecture:** Tree-based rule configuration with real-time validation, Monaco editor with autocomplete for DSL rules, delegated zmanim calculations, and React-PDF for Hebrew document generation. MVP timeline: 8 weeks to working system with Beis Mordechai Manchester as design partner.

## Domain Overview

### Industry Context

**Primary Domain:** Religious Community Management with Complex Multi-Tenant Scheduling

Shtetl operates in a unique intersection of:
- **Orthodox Community Software** - Serving Jewish communities with religiously-mandated scheduling requirements
- **Multi-Tenant SaaS** - Each Shul (synagogue) operates as an independent tenant with complete autonomy
- **Scientific Calculation Systems** - Zmanim (Jewish prayer times) are astronomically calculated and halachically interpreted
- **Document Generation & Publishing** - Producing formatted timetables in various layouts (PDF, Word, A4/A5, web)

### Regulatory Landscape

**Halachic Authority Structure:**
This is NOT government regulation but rather a hierarchical authority system:

- **Regional Rabbinic Authorities** - One or two recognized poskim (halachic decisors) in each geographic area publish authoritative zmanim calculations annually
- **Shul Allegiance** - Individual synagogues choose which rabbinic authority to follow based on their denominational/halachic alignment (e.g., Chassidic, Yeshivish, Modern Orthodox, Sephardic traditions)
- **Local Adaptation** - Shuls may apply minor adjustments to the authoritative zmanim based on local minhag (custom) or practical considerations

**Critical Insight:** This is a **soft governance model** where authority is voluntary, traditional, and relationship-based rather than legally enforced. However, publishing incorrect zmanim would be considered a serious halachic breach that could undermine trust in the entire platform.

### Key Stakeholders

1. **Regional Rabbinic Authorities / Zmanim Publishers**
   - Publish annual base zmanim calculations for their region
   - Serve as authoritative sources that Shuls reference
   - May or may not be direct users of Shtetl, but their calculations must be representable

2. **Shul Administrators (Gaboim)**
   - NON-TECHNICAL users who need simple, intuitbut write complex queries in raw SQL when needepbut write complex queries in raw SQL when needekive interfaces
   - Responsible for configuring their Shul's zmanim (selecting base source + applying local rules)
   - Need to generate timetables in various formats (weekly, biannual, custom layouts)
   - Must maintain accuracy while managing complexity

3. **Congregants (Mispallelim)**
   - Rely on published times being correct
   - Access through mobile apps, SMS, printed timetables
   - Expect consistency across channels

4. **Community Developers**
   - May build integrations, automation, custom features
   - Need clear APIs and extension points

## Critical Concerns

### The Core Domain Challenge

**"What keeps me up at night is creating a product for many different synagogues where each has their own calculations, requirements, and needs for timetables. They all have different formulas and logic. The administrations aren't very technical so they need really good UX. Each one wants output in different formats. If the system isn't capable of dealing with all these complexities, it just won't be used."** - User feedback

### Multi-Tenant Customization Requirements

1. **Calculation Flexibility**
   - Each Shul needs to select their base zmanim source (which rabbinic authority they follow)
   - Apply local customizations/offsets to the base calculations
   - Support multiple different calculation methodologies simultaneously across tenants
   - CRITICAL: Formula complexity is unknown and shouldn't need to be - delegate to authoritative sources

2. **Non-Technical User Experience**
   - Gaboim are NOT technical users
   - Must provide intuitive, template-based configuration
   - Visual, no-code approach to customization
   - Mistake-proof interfaces (can't accidentally break calculations)

3. **Output Format Diversity**
   - Different layouts: A4, A5, letter size
   - Different frequencies: weekly, biannual, custom ranges
   - Different content: zmanim only, zmanim + shiurim, full schedules
   - Multiple output formats: PDF, Word, web, mobile
   - Each Shul may need multiple template variants

### Technical Constraints

**Hierarchical Data Structure:**
The zmanim system must support a clear hierarchy:
```
Regional Rabbinic Authority (publishes base zmanim annually)
    ↓ (Shul selects allegiance)
Shul Base Configuration (selected authority + location)
    ↓ (Shul applies local rules)
Shul Published Zmanim (what congregants see)
```

**Template System Requirements:**
- Non-technical users must be able to create/modify templates
- Start with one working example, expand iteratively
- Use familiar interfaces (Word-like or drag-and-drop)
- Support conditional content and dynamic data insertion

### Safety/Risk Considerations

**CRITICAL: Accuracy is Non-Negotiable**

Publishing incorrect zmanim is considered a serious halachic breach that could:
- Cause people to daven at wrong times (missing zmanim windows)
- Undermine trust in the entire platform
- Damage the reputation of the Shul
- Lead to abandonment of the system

**Therefore:**
- Calculations must be delegated to authoritative sources (don't build custom astronomy engine)
- Any user customizations must be clearly additive/transparent (offsets, not formula replacement)
- Validation and preview mechanisms are essential
- Audit trails for who changed what and when

## Regulatory Requirements

**Halachic Accuracy Requirements:**
- Zmanim calculations must be delegated to authoritative rabbinic sources
- System must support hierarchical authority structure (regional authority → Shul customization)
- Audit trails required for all time changes
- Publishing incorrect zmanim is considered a serious halachic breach

**No Government Regulation:**
This is a voluntary, community-based system with soft governance through traditional rabbinic authority rather than legal compliance requirements.

## Industry Standards

**Zmanim Calculation Standards:**
- **KosherJava Zmanim API** - Open source (LGPL 2.1), industry-standard library with 157+ zmanim calculations
- **Hebcal API** - REST API alternative for zmanim calculations
- **Hebrew Calendar Libraries** - Accurate Hebrew date calculations, holiday detection
- **Local Formulas** - Regional authority able to setup their own calculations using an easy UI+DSL

**Document Generation Standards:**
- **React-PDF** - Modern, open-source PDF generation for web/mobile
- **Monaco Editor** - VS Code's editor engine for DSL/code editing with autocomplete
- **Right-to-Left (RTL) Text Support** - Proper Hebrew text rendering

**Multi-Tenant SaaS Patterns:**
- Row-Level Security (RLS) for tenant isolation
- Shared database with tenant_id column approach
- Tiered configuration inheritance (global → regional → Shul → instance)

## Practical Implications

### Architecture Impact

**Critical Architectural Decision: Hierarchical Tree Structure for Minyan Rules**

The system architecture is built around a **tree-based rule configuration model**:

```
Shul
├── Minyan Type (Shacharit, Mincha, Maariv)
│   ├── Minyan Instance (First Minyan, Second Minyan, Third Minyan)
│   │   ├── Metadata (location, optional flag)
│   │   └── Rules (condition → time mapping)
│   │       ├── Rule: RoshChodesh and Sunday → 8:15
│   │       ├── Rule: RoshChodesh → 7:40
│   │       ├── Rule: Sunday → 8:00
│   │       └── Rule: weekdays → 7:30
```

**Key Architectural Components:**

1. **Tree Data Model** - Minyan types contain minyan instances, which contain rules
2. **Primitive System** - Global and community-specific calendar primitives (RoshChodesh, FastDays, Selichot, BankHolidays, etc.)
3. **Validation Engine** - Ensures every non-optional minyan has time for every day in season
4. **Rule Resolution** - Auto-prioritized by specificity (most specific condition wins)
5. **Coverage Calculator** - Real-time feedback on which days are missing times

**Technology Stack:**
- **Frontend:** React + Monaco Editor (DSL IDE with autocomplete)
- **Backend:** Go with PEG parser for rule DSL
- **Database:** MongoDB (tree structure, flexible schema)
- **Calendar:** Hebrew calendar libraries for primitive evaluation
- **PDF Generation:** React-PDF or Go-based solution

**Multi-Tenant Strategy:**
- Shared database with tenant isolation via Shul ID
- Each Shul maintains independent minyan tree
- Primitives can be global (RoshChodesh) or community-specific (Selichot dates)

### Development Impact

**MVP Development Approach:**

**Phase 1: Rules Engine (Weeks 1-4)**
- Build tree-based UI for creating minyan structure
- Implement rule editor with condition → time mapping
- Build validation engine with coverage calculator
- Import base zmanim data (CSV → MongoDB)
- Output: Working rule configuration system

**Phase 2: PDF Renderer (Weeks 5-6)**
- Execute rules to generate full season schedule
- Implement PDF template matching Beis Mordechai example
- Hebrew text support (RTL rendering)
- Output: Generated PDF timetable

**Phase 3: Testing & Pilot (Weeks 7-8)**
- Test with real Shul data (Beis Mordechai)
- Visual regression testing (output matches input)
- Coverage validation testing
- User acceptance testing with gabbai

**Development Complexity:**
- **Moderate:** Tree structure is conceptually simpler than flat rule system
- **Key Risk:** DSL parser and validation logic complexity
- **Mitigation:** Start with simple rule syntax, expand iteratively

**Required Expertise:**
- Go backend development (parser, rule engine)
- React development (tree UI, Monaco integration)
- Hebrew text rendering (RTL, Unicode, fonts)
- PDF generation (layout, formatting, multi-page)

### Timeline Impact

**8-Week MVP Timeline:**
- Week 1-2: Tree structure UI + basic rule editor
- Week 3-4: Validation engine + coverage calculator
- Week 5-6: PDF generation from tree data
- Week 7-8: Testing + first Shul pilot (Beis Mordechai)

**Post-MVP Expansion:**
- Each additional Shul: ~1-2 weeks (assuming similar patterns)
- Advanced DSL features (calculated times, constraints): 2-3 weeks
- Template customization system: 3-4 weeks
- Mobile app integration: 4-6 weeks

**No Time Estimates for Business Planning:**
As per project requirements, avoid giving time estimates for stakeholder planning. Development speed with AI assistance makes traditional estimates unreliable.

### Cost Impact

**Open Source Strategy = Minimal Licensing Costs:**
- All core libraries are open source (MIT/LGPL)
- No Word licenses required
- No proprietary template engines

**Infrastructure Costs (Estimated):**
- AWS Serverless hosting: ~$50-200/month (depending on usage)
- MongoDB Atlas: Free tier → ~$50/month for production
- Domain + SSL: ~$20/year
- Total: < $300/month for MVP

**Development Costs:**
- Bootstrap/volunteer-based development model
- Core team + community contributors
- Modest operational budget for hosting

**Per-Shul Costs:**
- Zero marginal cost (multi-tenant shared infrastructure)
- Scales efficiently with tenant count

## Domain Patterns

### Established Patterns

**Multi-Tenant SaaS Configuration Patterns:**
- **Tiered Configuration Inheritance** - Global defaults → Regional → Shul → Instance overrides
- **Row-Level Security (RLS)** - Shared database with tenant isolation via tenant_id column
- **Shared Database, Shared Schema** - Recommended pattern for multi-tenant applications (most cost-effective)

**Document Generation Patterns:**
- **Template + Component Architecture** - Fixed templates with configurable components
- **Form-Based Configuration** - Constrained customization via forms instead of free-form editors
- **No-Code/Low-Code Builders** - Visual editors with smart defaults (Craft.js, GrapesJS patterns)

**Calendar/Scheduling System Patterns:**
- **Rule-Based Scheduling** - Condition → Action mappings with priority resolution
- **Hierarchical Tree Structures** - Nested categories for complex scheduling logic
- **Validation-First Design** - Real-time coverage validation ensures completeness
- **Primitive Systems** - Reusable calendar primitives (holidays, special days) for rule conditions

### Innovation Opportunities

**Novel Approach: Hierarchical Tree + DSL Hybrid**

Shtetl's architecture combines established patterns in a novel way:

```
Tree Structure (Visual Organization)
    +
DSL Rules (Flexible Expression)
    +
Real-Time Validation (Coverage Guarantee)
    =
Non-Technical User Empowerment
```

**Key Innovation:** Most scheduling systems are either:
- Too rigid (form-only, no flexibility) OR
- Too complex (full programming required)

Shtetl balances this by:
1. **Tree structure** provides visual organization and clear mental model
2. **Simple DSL** allows flexible rule expression without coding
3. **Validation engine** prevents incomplete configurations
4. **Monaco editor** provides IDE-like experience for non-developers

**Competitive Differentiation:**
- **Open source** (vs. proprietary Shul management software)
- **Multi-tenant** (shared infrastructure, zero marginal cost per Shul)
- **Extreme customization** (each Shul's unique rules) without complexity explosion
- **Validation-first** (guarantees complete coverage)

## Risk Assessment

### Identified Risks

**1. DSL Complexity Creep**
- **Risk:** As Shuls request features, DSL becomes too complex for non-technical users
- **Likelihood:** High
- **Impact:** High (defeats core value proposition)

**2. Hebrew Text Rendering Issues**
- **Risk:** RTL text, Unicode, special characters render incorrectly in PDFs
- **Likelihood:** Medium
- **Impact:** High (unusable output)

**3. Zmanim Calculation Accuracy**
- **Risk:** Incorrect zmanim due to calculation errors or data import issues
- **Likelihood:** Low (using established libraries)
- **Impact:** Critical (halachic breach, trust loss)

**4. Validation Logic Gaps**
- **Risk:** Validation misses edge cases, Shuls discover missing days after publishing
- **Likelihood:** Medium
- **Impact:** Medium (reputational damage)

**5. Adoption Resistance**
- **Risk:** Gaboim prefer familiar tools (Excel, Word) over new system
- **Likelihood:** Medium
- **Impact:** High (no users = failed product)

**6. Scale Complexity**
- **Risk:** Supporting 50+ Shuls with vastly different patterns reveals architecture limitations
- **Likelihood:** Medium
- **Impact:** Medium (requires refactoring)

### Mitigation Strategies

**1. DSL Complexity Creep → Phased Feature Rollout**
- Start with minimal DSL (fixed times + basic conditions)
- Add features only when 3+ Shuls request same capability
- Maintain "simple mode" UI for 80% of use cases
- Advanced features behind "power user" mode

**2. Hebrew Rendering → Early Testing + Established Libraries**
- Use battle-tested libraries (React-PDF with RTL support)
- Create golden master test suite (visual regression)
- Test with real Shul PDFs from day one
- Fallback: Generate HTML → Puppeteer → PDF if React-PDF fails

**3. Zmanim Accuracy → Delegate + Validate**
- NEVER build custom astronomical calculations
- Use KosherJava/Hebcal (established, tested libraries)
- Import authoritative sources (rabbinic authority CSVs)
- Preview + approval workflow before publishing
- Audit trail for all changes

**4. Validation Gaps → Comprehensive Test Coverage**
- Test against full Hebrew calendar (all holidays, edge cases)
- Include leap years, daylight saving transitions
- Real-world validation: Pilot with 3-5 Shuls before general release
- Continuous monitoring: Alert if validation fails in production

**5. Adoption Resistance → Design Partner Program**
- Recruit 2-3 "design partner" Shuls for MVP
- Co-create with gaboim, not for them
- White-glove onboarding for first 10 Shuls
- Export to Excel/PDF preserves fallback option
- Demonstrate time savings (quantify hours saved)

**6. Scale Complexity → Modular Architecture**
- Design for extensibility from day one
- Plugin system for custom rule types
- Template marketplace (Shuls share configurations)
- Monitor patterns, extract common configurations into templates
- Refactor proactively based on usage data

## Validation Strategy

### Compliance Validation

**Halachic Accuracy - Non-Negotiable:**
- Use only KosherJava/Hebcal libraries for calculations
- Import from authoritative rabbinic sources
- Preview + approval workflow before publishing
- Audit trail for all changes (7-year retention)

### Technical Validation

- **Coverage:** 100% of non-optional minyanim have times for all season days
- **Rules:** Syntax validation, unreachable rule detection
- **Output:** Visual regression testing against golden masters
- **Security:** Tenant isolation validation, no data leakage

### Domain Expert Validation

**Design Partner Program:**
- Beis Mordechai (Manchester) - Primary partner
- 2-3 additional Shuls with diverse patterns
- Side-by-side validation: Original PDF vs. Generated PDF
- Gabbai usability testing for independent maintenance

## Key Decisions

1. **Tree Structure + DSL Hybrid** - Clear mental model vs. flat rules
2. **Fixed Templates** - Constrained customization vs. full flexibility
3. **Delegate Zmanim** - Use established libraries vs. custom calculations
4. **Coverage-First** - 100% validation requirement before PDF generation
5. **Auto-Priority + Override** - Smart defaults with manual escape hatch
6. **Shared Database** - Multi-tenant with Row-Level Security

## Recommendations

### Must Have (MVP Critical)

1. Hierarchical tree UI (Minyan Type → Instance → Rules)
2. Real-time validation engine with coverage calculator
3. Primitive system (built-in + custom)
4. Simple rule DSL with Monaco editor + autocomplete
5. PDF generation matching Beis Mordechai layout
6. Zmanim CSV import

### Should Have (Important)

1. Calculated times (offset from zmanim)
2. Optional minyan conditional logic
3. Audit trail
4. Template cloning

### Consider (Future)

1. Advanced DSL (constraints, seasonal variations)
2. Template customization (colors, logos)
3. Multi-season management
4. Collaboration features

### Development Sequence

- **Weeks 1-2:** Foundation (MongoDB, API, basic tree UI)
- **Weeks 3-4:** Rules + validation engine
- **Weeks 5-6:** PDF generation + Hebrew rendering
- **Weeks 7-8:** Testing + Beis Mordechai pilot

### Required Expertise

- **Backend:** Go, MongoDB, parser design, Hebrew calendar
- **Frontend:** React, tree UI, Monaco editor, drag-and-drop
- **Domain:** Jewish calendar, Shul operations, Hebrew typography
- **Testing:** Visual regression, RTL validation, UAT with gaboim

## PRD Integration Guide

### Summary for PRD

**Domain:** Religious Community Management + Multi-Tenant Scheduling
**Complexity:** High (extreme customization + non-technical users + critical accuracy)

**Core Challenge:** Enable each Shul to configure complex, unique minyan scheduling rules through an intuitive interface while guaranteeing 100% schedule coverage and halachic accuracy.

**Solution:** Hierarchical tree-based rule system with validation-first design.

### Requirements to Incorporate

1. **Hierarchical Minyan Configuration System** - Tree structure with real-time validation
2. **Calendar Primitive System** - Global + community-specific special days
3. **Rule-Based Scheduling Engine** - Condition → time mappings with auto-priority
4. **PDF Generation** - Hebrew RTL support, multiple templates per Shul
5. **Zmanim Data Management** - Import from authoritative sources
6. **Multi-Tenant Architecture** - Complete Shul autonomy with shared infrastructure

### Architecture Considerations

- Use shared database with Row-Level Security (not database-per-tenant)
- Delegate zmanim calculations to KosherJava/Hebcal libraries
- Build validation engine before PDF renderer
- Design for extensibility (plugin system for custom rule types)
- Plan for template marketplace in Phase 2

### Development Considerations

- Start with simple DSL, expand based on real usage
- Hebrew text rendering is critical path - test early
- Visual regression testing essential for PDF output
- Design partner program critical for validation
- Balance flexibility vs. complexity carefully

## References

### Regulations Researched

- Halachic Authority Structure (voluntary, traditional governance model)
- No government regulations apply

### Standards Referenced

- [KosherJava Zmanim API](https://kosherjava.com/zmanim-project/) - Open source zmanim library (LGPL 2.1)
- [Hebcal API](https://www.hebcal.com/) - REST API for zmanim calculations
- [React-PDF](https://react-pdf.org/) - PDF generation library with RTL support
- [Monaco Editor](https://microsoft.github.io/monaco-editor/) - VS Code editor engine

### Additional Resources

- Multi-Tenant SaaS Architecture Patterns (Microsoft Azure docs)
- Document Generation Best Practices (Windward, Docupilot)
- Hebrew Calendar Libraries (go-hebrewcal)

## Appendix

### Research Notes

**Web Research Conducted:**
1. Multi-tenant SaaS configuration patterns → Shared database with RLS recommended
2. Template builders for non-technical users → Monaco editor + constrained customization
3. Zmanim calculation APIs → KosherJava (157+ calculations), Hebcal REST API

**Key Technical Findings:**
- React-PDF supports Hebrew RTL text rendering
- Monaco editor provides autocomplete and syntax highlighting out-of-box
- Go PEG parsers (participle) suitable for DSL implementation

### Conversation Highlights

**Critical Insights from User:**

1. **"Each synagogue has their own calculations, formulas, logic. If the system isn't capable of dealing with all these complexities, it just won't be used."**
   - Led to hierarchical tree + DSL hybrid approach

2. **"We need primitives like Selichot days, Rosh Chodesh, holidays. Each community would have their own primitives."**
   - Informed primitive system design (global + community-specific)

3. **"Tree structure: Shacharit → First Minyan → Rules for Sunday, Rosh Chodesh, etc. With validation that creates minyan time for whole year."**
   - Defined core architecture: tree + coverage validation

4. **"I don't like Word - requires license, users can break layout. Need something rigid, open source, web/mobile compatible."**
   - Led to React-PDF + fixed templates decision

**Example PDF Provided:**
- Beis Mordechai Manchester Winter 5786 timetable
- Showed Hebrew RTL text, multi-minyan display (8:30* / 8:00 / 7:40), asterisk notation for Ezrat Nashim
- Informed PDF rendering requirements and layout structure

### Open Questions

**Resolved During Research:**
- ✅ Template approach: Fixed templates with data customization
- ✅ Zmanim source: Delegate to KosherJava/Hebcal
- ✅ Multi-tenant pattern: Shared database with RLS
- ✅ Rule priority: Auto-calculated with manual override
- ✅ Optional minyanim: Flag + condition, validated only on matching days

**Remaining for PRD/Architecture:**
- Specific DSL syntax grammar (define formally during implementation)
- Exact PDF layout variants needed (learn from design partners)
- Authentication/authorization model (likely Clerk as per tech preferences)
- Deployment strategy (AWS Serverless architecture details)

---

_This domain brief was created through collaborative research between the user and a multi-agent AI team. It should be referenced during PRD creation, architecture design, and implementation planning._
