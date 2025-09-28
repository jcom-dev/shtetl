# Project Brief: Shtetl

## Executive Summary
Shtetl is a free, open-source platform that unifies Jewish community communications into a centralized hub. It enables gaboim to manage their Shuls' content, including davening times, shiurim, and other events through a dedicated administrative interface. The platform assists authorized rabbinic authorities in calculating and publishing official zmanim for each locality, which Shuls then use to coordinate their minyan times. Beyond scheduling, Shtetl offers comprehensive Shul management tools for tracking membership, aliyos, simchas, and yahrzeits. By providing each Shul with its own controlled space while enabling unified access through mobile apps, SMS updates, text-to-speech call-in numbers, MCP integrations, and automation hooks, Shtetl eliminates fragmented communication channels. The platform's core promise is timely, actionable visibility into zmanim, davening and shiurim times through a connected ecosystem of alerts that keeps the entire community informed, with or without a smartphone.

## Problem Statement
Local Jewish communities struggle to coordinate through a fragmented network of communication channels—WhatsApp broadcasts, hallway announcements, SMS, and email threads—to track minyan schedules, shiur rotations, and communal services. Each Shul maintains its own version of truth, typically in printed formats posted on walls but inaccessible when needed on-the-go. With schedules changing weekly or even daily, congregants find it challenging to stay current and set reliable reminders. Gabbaim spend countless hours maintaining spreadsheets, calendars, and messaging systems, yet still field panicked calls minutes before Shacharis asking whether minyan starts at 6:20 or 6:30. The lack of standardized APIs limits community developers from building automated solutions, even as demands grow for smart features like automated climate control before minyanim, schedule-based alerts and location-aware notifications.

## Proposed Solution
Shtetl is a modular, open-source platform that preserves Shul autonomy while enabling opt-in participation in a shared community data network. Through independent control panels, each Shul maintains complete authority over its digital presence, including minyan schedules, shiurim, alerts, simchas, and zmanim calculations. The platform distributes this Shul-approved information through congregant-chosen channels: mobile apps, SMS alerts, email notifications, and facility automation systems. Using MCP servers, Zapier integrations, and smart building controls, Shtetl converts schedule data into automated actions and personalized reminders — all under each Shul's oversight. As a community-owned solution, the platform welcomes developer contributions while ensuring updates respect local minhagim and individual Shul preferences. The result is a flexible, sustainable system that keeps schedules synchronized, supports automation, and adapts to each community's unique needs.

## Target Users
### Primary User Segment: Congregation Members
- **Demographic/Firmographic Profile:** Mispallelim in Jewish communities of all sizes, including regular members, guests, travelers, and anyone seeking to daven in any minyan.
- **Current Behaviors & Workflows:** Currently juggle multiple communication channels including WhatsApp groups, printed bulletins, personal inquiries to gabbaim, and informal word-of-mouth networks to stay informed about Shul schedules and events.
- **Needs & Pain Points:** Need consistent access to accurate zmanim, minyan times, and shiur schedules in real-time; experience frequent frustration with fragmented communications, unreliable notifications, and lack of clarity regarding service locations and types; gabbaim spend hours compiling timetables, sending email and SMS, and chasing members for confirmations and changes.
- **Goals:** Easily find and attend suitable minyanim with personalized wake-up alarms linked to actual davening times, efficiently manage learning commitments, coordinate daily schedules around davening times, reduce administrative overhead for gaboim through automation, and stay instantly informed of community events and schedule changes through a centralized system.

## Goals & Success Metrics
### Business Objectives
- Enable 3-5 Shuls to replace manual production of all timetables within 6 months of launch.
- Increase Shul efficiency by centralizing 80% of communal schedule updates into Shtetl within 6 months of launch.
- Reduce administrator workload by cutting time spent on manual notifications by 60% per participating Shuls within the first 6 months.
- Foster open-source adoption with a vibrant community of volunteer developers contributing features and integrations

### User Success Metrics
- At least 70% of active congregants subscribe to personalized minyan or shiur alerts.
- 80% of users can find current minyan times within 30 seconds of opening the app.
- 60% of surveyed users report they no longer need to call gabbaim for schedule information.

### Key Performance Indicators (KPIs)
- **Shul Onboarding Rate:** Number of active Shul spaces created vs. outreach targets (goal: 15 by month 6).
- **User Engagement:** Monthly active users across mobile app, SMS, and web channels (goal: 70% of registered members).
- **Contribution Velocity:** Pull requests merged per quarter from community contributors. Number of active contributors.

## MVP Scope
### Core Features (Must Have)
- **Shul Spaces:** Dedicated admin portals for each Shul to manage schedules, events, and announcements with fine-grained permissions.
- **Prayer Time Scheduling:** Daily minyan setup with zmanim-aware logic, recurring templates, and exception handling for holidays.
- **Shiur & Event Listings:** Publish learning sessions and communal events with categories, speakers, and location metadata.
- **User Subscriptions & Alerts:** Allow congregants to subscribe to specific minyanim or shiur categories and receive push/email notifications.
- **Automation Hooks:** Integrate with MCP servers/Zapier to trigger facilities actions (e.g., HVAC on/off) based on schedule data.
- **Mobile App Access:** Provide iOS/Android front ends for browsing schedules, configuring reminders, and receiving alerts.

### Out of Scope for MVP
- Donation management or payment processing.
- Complex CRM features beyond basic member directories.
- AI-driven personalization or recommendation engines.
- Multi-language translations beyond core English interface.

### MVP Success Criteria
Shtetl MVP is successful when at least five Shuls actively maintain their schedules through the platform, congregants rely on the mobile app or alerts for daily minyan attendance, and the automation hooks are powering at least one real-world facility action per participating Shul, with feedback indicating improved schedule confidence.

## Post-MVP Vision
### Phase 2 Features
- **Comprehensive Shul Portal:** Complete membership management with contact information, payment tracking, aliyos scheduling, shaylos submission and tracking, yahrzeit management, and event coordination.
- **Full-Featured Mobile App:** Comprehensive mobile application supporting all portal features including membership management, aliyos scheduling, shaylos submission, yahrzeit tracking, and event coordination with offline capabilities.
- **Public APIs & Event Hooks:** Well-structured, publicly accessible APIs and event hooks making Shtetl data easily available to AI systems, MCP servers, Zapier/Make integrations, and third-party developers.
- **Shared Learning Libraries:** Catalog shiur recordings, source sheets, and educational content linked to schedules and speakers.
- **Multi-Language Support:** Hebrew/Yiddish interface and content localization for diverse communities.


### Long-term Vision
Shtetl transforms into the vibrant digital heartbeat of global Jewish life — a living ecosystem where every Shul, and organization connects seamlessly through intelligent APIs and community-driven innovation. Imagine a world where you can find a minyan based on time, location anywhere in the world within seconds or subscribe to events from any shul, and get automatic reminders for the minyanim or events that are important to you via SMS, app notification, etc. Shtetl becomes the universal platform that preserves local autonomy while enabling global collaboration, turning every Jewish community into a connected node in a worldwide network of shared collaboration, automated efficiency, and meaningful data.

### Expansion Opportunities
- 
## Technical Considerations
### Platform Requirements
- **Target Platforms:** Web admin console; native mobile apps (iOS/Android) for congregants; integration endpoints for MCP/Zapier connectors.
- **Browser/OS Support:** Modern evergreen browsers for web; iOS 15+/Android 11+ for mobile to leverage current push/automation capabilities.
- **Performance Requirements:** Near-real-time propagation of schedule updates (<30 seconds); automation triggers to execute within 2 minutes of scheduled events.

### Technology Preferences
- **Frontend:** React-based web admin
- **Mobile:** React Native for cross-platform mobile apps
- **Backend:** Golang with modular service architecture
- **Database:** MongoDB for document storage, Redis for caching
- **Authentication:** Clerk for user management and authentication
- **Hosting/Infrastructure:** AWS Serverless with Terraform for infrastructure as code
- **DevOps:** GitHub Repo/Projects/Actions for CI/CD and project management
- **Integrations:** Zapier/MCP, Slack, Notion for automation and workflow integration

### Architecture Considerations
- **Repository Structure:** Multi-repo architecture with separate repositories for core services, admin frontend, mobile clients, and integration adapters to support community contributions and independent development.
- **Service Architecture:** API gateway exposing schedule, alert, and automation services; event-driven messaging for automation triggers; plugin framework for community-built modules.
- **Integration Requirements:** REST/GraphQL APIs with webhooks; connectors to MCP servers, Zapier, and facility control systems (HVAC, lighting).
- **Security/Compliance:** Role-based access for Shul admins; audit trails for schedule edits; secure handling of personal contact data; compliance with regional privacy laws (e.g., GDPR where applicable).

## Constraints & Assumptions
### Constraints
- **Budget:** Bootstrap/open-source funding; rely on volunteer contributions and modest operational budget for hosting and core maintenance.
- **Timeline:** Aim to release MVP within 6-12 months, aligned with upcoming High Holiday season when scheduling pressures peak.
- **Resources:** Core team of a few developers plus volunteer contributors for integrations; Shul admins providing domain input.
- **Technical:** Must accommodate diverse Shul data formats and handle zmanim calculations with halachic accuracy across time zones.

### Key Assumptions
- Shuls are willing to centralize schedules if they retain autonomy over their space and branding.
- Community members will adopt mobile/apps and alert subscriptions if information remains accurate and timely.
- Volunteer developers will contribute integrations if the project offers clear APIs and governance.


## Risks & Open Questions
### Key Risks
- **Data Structure Variations:** Each Shul will have specific data structures and requirements, making it difficult to create a centrally accepted system that works for all communities.
- **Data Accuracy Drift:** Without disciplined admin workflows, stale or conflicting updates could erode trust and cause users to revert to old channels.
- **Inaccurate Data:** . Incorrect times could lead to distrust of the entire system.
- **Adoption Resistance:** Some Shuls may resist centralization due to fear of losing control, different digital policies, or misaligned digital readiness. Internet dependence may also be a concern for some Shuls. An optional SMS or telephone interface can be built to mitigate this.
- **Volunteer Burnout:** Reliance on community contributors could stall integrations or maintenance if onboarding/support isn’t strong.

### Open Questions
- How will we onboard Shuls efficiently, including training and data migration from spreadsheets/WhatsApp?
- What governance model ensures quality control over community contributions and integrations?
- Which communication channels (push, SMS, email) are mandatory at launch vs. optional?
- How do we handle varied halachic interpretations (e.g., zmanim calculations) across communities?

### Areas Needing Further Research
- Benchmarking successful communal scheduling systems to identify best practices.
- Understanding legal/privacy requirements for storing congregant contact data in different regions.
- Researching the individual requirments of different shuls, locations etc to ensure that we can create a common data structure

## Appendices
- **A. Research Summary:** _Not yet provided._
- **B. Stakeholder Input:** _Not yet provided._
- **C. References:** `docs/prd.md`; `docs/architecture/*`.

## Next Steps
1. **Build Initial Team:** Assemble initial leadership team including developers, business analysts, architects, DevOps, and QA specialists to establish project foundation.
2. **Establish Project Structure:** Create weekly meeting cadence and organizational structure that allows developers to balance work and other responsibilities while contributing effectively.
3. **Prepare Specifications:** Use BMad methodology with human oversight to create comprehensive project specifications and design initial architecture.
4. **Setup Contribution Infrastructure:** Prepare Git repositories and development tooling to be contribution-ready for community developers.
5. **Onboard Design Partners:** Recruit minimum 2-3 Shuls as design partners to validate requirements and provide real-world feedback.
6. **Establish Collaboration Framework:** Set up tooling and processes for effective collaboration between core team, community contributors, and Shul partners.

### PM Handoff
This Project Brief provides the full context for Shtetl. Please start in PRD Generation Mode, review the brief thoroughly to work with the user to create the PRD section by section as the template indicates, asking for any necessary clarification or suggesting improvements.

