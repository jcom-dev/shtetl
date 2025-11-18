# Shtetl UX Design Specification

_Created on 2025-11-18 by BMad_
_Generated using BMad Method - Create UX Design Workflow v1.0_

---

## Executive Summary

**Project Vision:** Shtetl is a free, open-source platform that transforms Jewish community communications by creating a centralized hub where rabbinic authorities publish authoritative calendar calculations, synagogue administrators configure minyan schedules, and congregants access accurate times through mobile apps, SMS, and automated alerts.

**Core Value Proposition:** "Extreme Customization Without Complexity" - Enable each synagogue to configure highly complex, unique scheduling rules through intuitive interfaces while guaranteeing 100% schedule coverage and maintaining halachic accuracy.

**Target Users:**
1. **Rabbinic Authorities** - Technically sophisticated users creating astronomical and calendar calculation algorithms
2. **Synagogue Administrators (Gaboim)** - Non-technical users configuring minyan schedules using visual tree-based interfaces
3. **Congregants (Kehilla Members)** - End users accessing schedules via mobile apps and web with minimal cognitive load

**Platform:** Web application (React + Downshift) for admin interfaces, mobile app (React Native + Expo) for congregants, responsive mobile web fallback.

**Key Innovation:** Dual DSL system with hierarchical primitive cascade and real-time coverage validation preventing incomplete schedules.

### User Empathy Analysis

#### Synagogue Administrator (Gabbai)
- **Thinks:** "I can't make mistakes - the community depends on accurate times"
- **Feels:** Anxiety about halachic responsibility, overwhelmed by complexity, pride in service
- **Pains:** Hours on manual notifications, fear of errors, no coverage validation until too late
- **Gains:** Confidence from 100% coverage guarantee, time saved, easy handoff to successor

#### Rabbinic Authority (Zmanim Publisher)
- **Thinks:** "My calculations must be astronomically and halachically precise"
- **Feels:** Responsibility for accuracy affecting thousands, intellectual satisfaction
- **Pains:** Manual calculations prone to error, difficulty documenting methodology
- **Gains:** Trusted regional source, clear audit trail, technical tools that match sophistication

#### Congregant (Kehilla Member)
- **Thinks:** "I just need to know what time minyan is - why is this so hard?"
- **Feels:** Frustration with scattered info, anxiety about missing minyan, trust when accurate
- **Pains:** Information scattered across channels, no personalized reminders
- **Gains:** 30-second time-to-answer, wake-up alarms linked to minyan times

### UX Design Principles (From Empathy Analysis)

| Persona | Core Need | UX Principle |
|---------|-----------|--------------|
| **Gabbai** | Confidence without complexity | Visual validation, preview before publish, 100% coverage guarantee always visible |
| **Authority** | Precision with documentation | IDE-like experience, inline citations, version comparison, reference validation |
| **Congregant** | Instant, trustworthy access | Zero cognitive load, <30 seconds to answer, personalized alerts |

**Critical Insight:** The gabbai's anxiety about mistakes drives the entire admin UX. Every interaction builds confidence: green checkmarks, progress indicators, clear error messages with solutions, and "preview exactly what congregants see" functionality.

### Six Perspectives Analysis

#### Facts (White Hat)
- 3 distinct personas with fundamentally different technical abilities
- 100 functional requirements covering MVP
- NFR-USE-1: Gaboim must create first schedule within 30 minutes
- NFR-USE-2: Congregants must subscribe within 2 minutes of launch
- NFR-PERF-4: Autocomplete suggestions within 100ms

#### Emotions (Red Hat)
- Gaboim feel intimidated by anything resembling "coding"
- Tree structure is intuitive (folder/hierarchy mental model)
- Dark theme for authorities signals "technical tool"
- 100% coverage indicator is the anxiety-killer gaboim need
- Hebrew RTL rendered correctly builds trust

#### Risks (Black Hat)
- Complexity creep overwhelming gaboim
- False confidence (coverage ≠ correctness)
- Mobile app feeling like afterthought
- Onboarding cliff with empty tree
- Mental model mismatch (gaboim think "weekly schedules," system thinks "rules")

#### Benefits (Yellow Hat)
- Guided "Add Your First Minyan" eliminates blank-page anxiety
- Real-time coverage % climbing 0→100% is satisfying
- Clone from template saves hours
- One-tap subscription delights congregants
- Find minyanim when traveling = killer feature

#### Creative Ideas (Green Hat)
- **"Schedule Detective" onboarding** - System asks questions, builds rules automatically
- **Visual calendar overlay** - Rules as colored blocks (green=covered, red=missing)
- **"What-if" simulator** - "What happens on Chanukah that falls on Sunday?"
- **Community templates** - Browse and import from similar Shuls
- **QR code on PDF** - Scan printed timetable to subscribe

### UX Strategy Priorities

| Priority | Focus | Key Approach |
|----------|-------|--------------|
| **1** | Confidence-Building (Gabbai) | Every interaction reduces anxiety; visual feedback at every step; preview matches output exactly; guided onboarding |
| **2** | Speed-to-Value (Congregant) | <30 seconds to today's times; <2 minutes to set up alerts; offline-capable; native mobile feel |
| **3** | Professional Tooling (Authority) | IDE patterns; inline documentation; version control; external validation |

### Key UX Terminology Decision
**Use "rules" not "DSL"** in gabbai interface - technical terminology scares non-technical users.

### First Principles: Extreme Customization Without Complexity

#### Fundamental Truths

1. **Complexity lives in the domain, not the interface** - Scheduling rules ARE complex; the interface captures complexity, not hides it
2. **Users express intent, systems express implementation** - "Rosh Chodesh starts at 8:15" → system stores structured rule
3. **Validation is the confidence mechanism** - Real-time validation catches what humans can't mentally simulate
4. **Power and simplicity are orthogonal** - Few concepts to learn (simple) + can express any outcome (powerful)
5. **Progressive disclosure matches progressive expertise** - Same interface, different depths

#### Three Concepts Maximum (Gabbai Interface)

The entire scheduling interface reduces to exactly 3 concepts:
1. **Minyan** (what) - Shacharit First Minyan
2. **Condition** (when) - Rosh Chodesh, Sunday
3. **Time** (result) - 8:15

Everything else is implementation detail hidden from user.

#### Core UX Mechanisms

| Mechanism | Purpose | Implementation |
|-----------|---------|----------------|
| **Natural Language Input** | Bridge user intent → structured data | Autocomplete accepts "Rosh" → shows "RoshChodesh" primitive |
| **Real-time Validation** | Remove cognitive load | System tells user what's missing, not user checking 365 days |
| **Preview** | Bridge mental models | PDF preview shows rules in "weekly schedule" format gabbai expects |
| **Templates** | Encode expert knowledge | "Most Orthodox Shuls use these Rosh Chodesh rules" → one-click import |

#### The Reconstructed Solution

**"Extreme Customization Without Complexity" = CEVP:**
- **Capture** all domain complexity (100% of real scheduling needs)
- **Express** in user's mental model (minyan/condition/time)
- **Validate** exhaustively (100% coverage guarantee)
- **Preview** in familiar format (PDF matching expectations)

*The system is complex. The interface is simple. The user is confident.*

### User Journey Analysis

#### Critical User Journeys

**Journey 1: Gabbai Creates First Schedule**
- 12 stages from discovery → publish → share
- **Make-or-break moment:** First view of empty dashboard (Stage 3)
- **Aha moment:** Watching coverage % climb 0→100% (Stage 9)
- **Trust verification:** PDF preview matches expectations (Stage 10)

**Journey 2: Authority Publishes Calendar Stream**
- 9 stages from access → define → validate → publish
- **Critical:** Validation against reference libraries (Stage 5)
- **Religious requirement:** Methodology documentation with sources (Stage 6)
- **Safety:** Version comparison before publish (Stage 7)

**Journey 3: Congregant Finds Minyan & Subscribes**
- 10 stages from download → find → subscribe → receive alert
- **Speed target:** See times within 3 seconds of launch (Stage 4)
- **Simplicity:** One-tap subscription, not multi-step form (Stage 6)
- **Trust builder:** First accurate notification (Stage 8)

#### Onboarding Success Metrics

| Persona | First-Time Flow | Success Metric |
|---------|-----------------|----------------|
| Gabbai | Guided "Add First Minyan" wizard | First rule created in <5 minutes |
| Authority | Template selection + example formulas | First calculation validated in <10 minutes |
| Congregant | Location → Shul → Today's times | See times in <30 seconds |

#### Confidence Builders by Persona

| Persona | Confidence Mechanism |
|---------|----------------------|
| Gabbai | Coverage % climbing, PDF preview matching expectations |
| Authority | Validation against KosherJava, version diff highlighting |
| Congregant | "Last updated" timestamp, clear offline indicator |

#### Critical Touchpoints to Nail

1. **Empty state** (Gabbai) - Must feel guided, not overwhelming
2. **Autocomplete** (Gabbai + Authority) - Must feel magical, instant suggestions
3. **First notification** (Congregant) - Must be accurate and timely
4. **PDF preview** (Gabbai) - Must match familiar printed format exactly

#### Friction Points to Eliminate

| Friction | Solution |
|----------|----------|
| Location/timezone setup | Auto-detect from browser/device |
| "Which primitives exist?" | Show all available in sidebar panel |
| "Which days are missing?" | Click coverage % → list specific days |
| Permission requests | Explain value before asking |
| Shul not found | "Request your Shul" option with notification when added |

### Pre-mortem Analysis: UX Failure Prevention

#### Identified Failure Scenarios

**Scenario 1: Gaboim Abandon System**
- "Excel fallback" - Updates slower than familiar workflow → Need quick-edit mode
- "Coverage lie" - 100% coverage but incorrect times → Need what-if date tester
- "Hebrew disaster" - RTL rendering issues → Need Hebrew testing from day 1

**Scenario 2: Congregants Uninstall App**
- "Notification flood" - Too many alerts → Need smart defaults and volume preview
- "Stale data" - Cached times don't match actual → Need prominent freshness indicators
- "Traveler letdown" - No Shuls in area → Need request option and fallback data

**Scenario 3: Authorities Never Publish**
- "Expertise cliff" - Formula editor too complex → Need templates and examples

#### UX Risk Mitigation Matrix

| Risk | Impact | Mitigation |
|------|--------|------------|
| No quick-edit for updates | Gabbai abandonment | Quick-edit dashboard, keyboard shortcuts, "update this week" flow |
| Coverage ≠ correctness | Trust destruction | What-if date tester, diff preview, manual priority override |
| Hebrew RTL rendering | Immediate rejection | Hebrew test fixtures from day 1, visual regression tests, RTL QA checklist |
| Notification overload | App uninstalls | Smart defaults (primary minyan only), volume preview, easy bulk-unsubscribe |
| Stale data display | Trust erosion | "Last updated" on every screen, push on schedule change, offline indicator |
| No Shuls in area | Traveler abandonment | "Request Shul" with notification, fallback to GoDaven/MyZmanim, show nearest |
| Formula editor complexity | Authority abandonment | Starter templates (GRA, MGA), example formulas, import from KosherJava |

#### Critical UX Safeguards

1. **Quick-edit mode** - Make common updates faster than Excel
2. **"What-if" date tester** - Verify correctness, not just coverage
3. **Hebrew RTL testing** - Core requirement from day 1, not afterthought
4. **Notification volume controls** - Quality over quantity, preview before subscribe
5. **Prominent freshness indicators** - Every screen shows "last updated"
6. **Graceful empty states** - Always provide next action when no results
7. **Starter templates** - No blank-canvas syndrome for any persona

### Core Experience Definition

#### Platform Strategy

| Persona | MVP Channels | Rationale |
|---------|--------------|-----------|
| **Congregant** | Mobile app, Phone voice, SMS | Multi-channel access for all user types |
| **Gabbai** | Desktop web only | Complex initial setup, "configure once runs forever" |
| **Authority** | Desktop web only | Technical interface requires full keyboard/screen |

**Post-MVP Roadmap:** Gabbai mobile app

**Design Note:** No emergency mobile fallback needed for gaboim - minyan schedules follow predictable patterns tied to zmanim and calendar, not subject to last-minute changes.

#### Congregant Multi-Channel Strategy

**Three channels provide complete accessibility:**

| Channel | Use Case | Users |
|---------|----------|-------|
| **Mobile App** | Rich experience, subscriptions, alerts, offline cache | Smartphone users |
| **Phone-in Voice** | Natural language queries in English/Hebrew/Yiddish | Non-smartphone users, elderly, hands-free |
| **SMS Queries** | Text-based requests and responses | Feature phone users, quick queries |

**SMS Query Examples:**
- "Today's zmanim" → All zmanim for user's location
- "Next mincha in Prestwich" → Location-aware search
- "All minchas near me" → List upcoming minyanim
- "Shacharit tomorrow Beis Mordechai" → Specific shul query

**SMS Response Considerations:**
- 160 character limit per message
- Prioritize most relevant info first
- Multi-message for longer lists
- Phone number links to user profile for "near me" queries

**Shared NLP Layer:** Phone voice and SMS use same natural language processing, different output formatting.

#### Phone-In System (Voice Interface)

**Purpose:** Natural language voice queries for non-smartphone users

**Target users:**
- Older congregants without smartphones
- Orthodox users who avoid smartphones for religious reasons
- Anyone needing quick hands-free access

**Core capabilities:**
- Natural language queries in multiple languages (English, Hebrew, Yiddish)
- Location-aware searches ("Find next Mincha in Prestwich")
- Time-aware responses ("Next Shacharit at Beis Mordechai is 7:30am tomorrow")
- Simple menu fallback for structured navigation

**Example interactions:**
- "When is the next Mincha near Prestwich?"
- "What time is Maariv at Beis Mordechai tonight?"
- "מתי מנחה הבאה?" (When is the next Mincha?)

**UX considerations:**
- Clear, slow speech output for elderly users
- Repeat option for missed information
- Transfer to human support for complex queries
- SMS follow-up option to send times to any phone

#### Core Experience by Persona

**Congregant Core Experience:** "Find today's minyan time instantly"
- Open app → see times immediately
- One-tap subscribe to alerts
- Trust that times are always accurate

**Gabbai Core Experience:** "Configure once, runs forever"
- Initial setup must be intuitive enough for one-time use
- Once configured correctly, system runs automatically
- No ongoing attention required after proper setup
- **Critical UX focus:** Tree builder + rule editor for first-time configuration

**Authority Core Experience:** "Define precise calculations with validation"
- Write formulas with autocomplete assistance
- Validate against reference libraries
- Document methodology with sources

#### The Hardest UX Challenge

The most critical interface to get right:

**Timetable Templating Engine** - Producing formatted timetables to each synagogue's unique requirements:
- **Time periods:** Weekly, monthly, seasonal, Yom Tov-specific
- **Format variations:** Each Shul has their own layout expectations
- **Configuration complexity:** Multiple output formats from same underlying rules
- **Hebrew RTL rendering:** Critical for trust and usability

Secondary challenges:
- Gabbai DSL interface (non-technical rule definition)
- Authority DSL interface (technical formula definition)

Note: DSL complexity deferred - focus on UX patterns, not language syntax.

#### Template Library Approach

**Core UX Principle:** Gaboim don't want to DESIGN a timetable - they want THEIR timetable to look like the one they're used to seeing on the wall.

**Solution:** "Pick something like this" → Template library with visual selection

**Template Library User Flow:**
1. **Browse gallery** - Visual thumbnails of real timetable styles
2. **Preview with your data** - "See how YOUR minyanim look in this template"
3. **Select and customize** - Adjust colors, fonts, logo, layout tweaks
4. **Generate** - Output in chosen format (PDF, print, digital display)

**Template Categories:**

| Category Type | Options |
|---------------|---------|
| **By format** | Weekly, Monthly, Seasonal, Yom Tov |
| **By style** | Traditional, Modern, Minimal, Information-dense |
| **By community** | Ashkenazi, Sephardi, Chassidic |

**MVP Template Set (5-10 curated templates):**
1. Beis Mordechai classic (design partner reference)
2. Simple weekly - minimal, clean
3. Information-dense monthly - multiple minyanim
4. Yom Tov special - holiday-focused layout
5. Modern digital - optimized for screens not print

**Template Technical Architecture:**

```yaml
Template:
  metadata: name, author, category, preview image
  layout: grid structure, sections, spacing
  typography: fonts, sizes, Hebrew RTL settings
  colors: primary, secondary, accent, background
  data_mappings: where each minyan/zman appears
  conditional_sections: Yom Tov notes, special announcements
```

**Template Customization Options:**
- Colors (primary, secondary, accent)
- Fonts (with Hebrew RTL support)
- Logo placement
- Section ordering
- Conditional content visibility

**Output Formats:**
- PDF (print-optimized)
- HTML (web embed)
- Image (social media sharing)
- Digital display (landscape mode for screens)

**Growth Strategy (Post-MVP):**
- Community-contributed templates (Shuls share designs)
- "Templates used by Shuls like yours" - social proof
- Popular templates surface to top
- Premium templates from professional designers
- Template marketplace with revenue share

**Network Effect:** More templates attract more Shuls → More Shuls create more templates

#### Design Implication: "Set and Forget"

Since gaboim configure once and system runs automatically:
- ✅ Optimize for first-time setup experience (not ongoing edits)
- ✅ Invest heavily in onboarding and guided flows
- ✅ Smart defaults and templates reduce configuration burden
- ✅ Validation prevents errors before they're published
- ⚠️ Quick-edit mode is lower priority than initially assumed

---

## 1. Design System Foundation

### 1.1 Design System Choice

{{design_system_decision}}

---

## 2. Core User Experience

### 2.1 Defining Experience

{{core_experience}}

### 2.2 Novel UX Patterns

{{novel_ux_patterns}}

---

## 3. Visual Foundation

### 3.1 Color System

{{visual_foundation}}

**Interactive Visualizations:**

- Color Theme Explorer: [ux-color-themes.html](./ux-color-themes.html)

---

## 4. Design Direction

### 4.1 Chosen Design Approach

{{design_direction_decision}}

**Interactive Mockups:**

- Design Direction Showcase: [ux-design-directions.html](./ux-design-directions.html)

---

## 5. User Journey Flows

### 5.1 Critical User Paths

{{user_journey_flows}}

---

## 6. Component Library

### 6.1 Component Strategy

{{component_library_strategy}}

---

## 7. UX Pattern Decisions

### 7.1 Consistency Rules

{{ux_pattern_decisions}}

---

## 8. Responsive Design & Accessibility

### 8.1 Responsive Strategy

{{responsive_accessibility_strategy}}

---

## 9. Implementation Guidance

### 9.1 Completion Summary

{{completion_summary}}

---

## Appendix

### Related Documents

- Product Requirements: `docs/prd.md`
- Product Brief: `docs/brief.md`
- Domain Brief: `docs/domain-brief.md`
- Epics: `docs/epics.md`

### Core Interactive Deliverables

This UX Design Specification was created through visual collaboration:

- **Color Theme Visualizer**: docs/ux-color-themes.html
  - Interactive HTML showing all color theme options explored
  - Live UI component examples in each theme
  - Side-by-side comparison and semantic color usage

- **Design Direction Mockups**: docs/ux-design-directions.html
  - Interactive HTML with 6-8 complete design approaches
  - Full-screen mockups of key screens
  - Design philosophy and rationale for each direction

### Optional Enhancement Deliverables

_This section will be populated if additional UX artifacts are generated through follow-up workflows._

<!-- Additional deliverables added here by other workflows -->

### Next Steps & Follow-Up Workflows

This UX Design Specification can serve as input to:

- **Wireframe Generation Workflow** - Create detailed wireframes from user flows
- **Figma Design Workflow** - Generate Figma files via MCP integration
- **Interactive Prototype Workflow** - Build clickable HTML prototypes
- **Component Showcase Workflow** - Create interactive component library
- **AI Frontend Prompt Workflow** - Generate prompts for v0, Lovable, Bolt, etc.
- **Solution Architecture Workflow** - Define technical architecture with UX context

### Version History

| Date       | Version | Changes                         | Author |
| ---------- | ------- | ------------------------------- | ------ |
| 2025-11-18 | 1.0     | Initial UX Design Specification | BMad   |

---

_This UX Design Specification was created through collaborative design facilitation, not template generation. All decisions were made with user input and are documented with rationale._
