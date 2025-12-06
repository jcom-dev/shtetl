# 🕍 Shtetl - Many Shuls. One Community.

<div align="center">

![Shtetl Logo](https://img.shields.io/badge/Shtetl-Community_Platform-4A90E2?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMTYiIGhlaWdodD0iMTYiIHZpZXdOb3g9IjAgMCAxNiAxNiIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KPHBhdGggZD0iTTggMkw2IDZINCw4SDEyTDEwIDZIOFoiIHN0cm9rZT0id2hpdGUiIHN0cm9rZS13aWR0aD0iMSIgZmlsbD0id2hpdGUiLz4KPC9zdmc+)
![License](https://img.shields.io/badge/License-Open_Source-green?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-In_Development-orange?style=for-the-badge)
![Community](https://img.shields.io/badge/Community-Driven-purple?style=for-the-badge)

**🌟 The Digital Heartbeat of Jewish Life 🌟**

_Unifying Jewish community communications into a centralized, intelligent hub_

[🚀 Get Started](#-quick-start) • [📖 Documentation](#-documentation) • [🤝 Contributing](#-contributing) • [💬 Community](#-community)

</div>

---

## 🎯 What is Shtetl?

**Shtetl** is a free, open-source multi-tenant SaaS platform that transforms fragmented Jewish community communications into a unified, intelligent ecosystem. The platform creates a centralized hub where **rabbinic authorities** publish authoritative zmanim calculations, **synagogue administrators (gaboim)** configure complex minyan schedules, and **congregants (kehilla members)** access accurate times through mobile apps, SMS, and automated alerts.

### 💎 Core Innovation: "Extreme Customization Without Complexity"

Shtetl enables each synagogue to configure highly complex, unique scheduling rules through intuitive interfaces while guaranteeing 100% schedule coverage and maintaining halachic accuracy.

### 🌈 Key Innovations

<table>
<tr>
<td width="50%">

#### 🎯 **Dual DSL System**

- 📐 **Zmanim Provider Builder** - Technical interface for rabbinic authorities to create astronomical calculation algorithms
- 🌳 **Minyan Scheduling DSL** - Non-technical tree-based interface for gaboim with visual rule builder + Downshift autocomplete
- ✅ Each optimized for its audience's expertise level

</td>
<td width="50%">

#### 🏗️ **Hierarchical Primitive Cascade**

- 🌍 Global primitives (Yom Tov, Fast Days, Rosh Chodesh)
- 🗺️ Auto-loaded regional primitives (UK Bank Holidays, US Federal Holidays)
- 🏛️ Shul-specific custom primitives (anniversaries, local events)
- 🔒 **Enforcement:** Lower levels can add but NEVER remove inherited primitives

</td>
</tr>
<tr>
<td width="50%">

#### ✅ **Real-Time Coverage Validation**

- 🔍 Validates every non-optional minyan has time for every day
- ⚠️ Reports missing coverage: "15 days missing times for Shacharit"
- 🚫 **Prevents publishing** until 100% coverage achieved
- 💚 Guarantees complete schedules, prevents errors

</td>
<td width="50%">

#### 📱 **Multi-Channel Access**

- 📱 Native mobile apps (iOS 14+, Android 10+)
- 💬 SMS alerts & push notifications
- 🌐 Web admin consoles (technical + non-technical)
- 🔌 API integrations (MCP/Zapier automation hooks)

</td>
</tr>
</table>

### 🎯 Three Distinct User Personas

<table>
<tr>
<td width="33%">

**👨‍🏫 Rabbinic Authorities**

Create and publish authoritative Jewish calendar calculation streams with multiple halachic opinions

</td>
<td width="33%">

**👔 Shul Administrators (Gaboim)**

Configure complex minyan schedules using tree-based rules with real-time validation

</td>
<td width="33%">

**👥 Congregants (Kehilla)**

Find minyan times, subscribe to personalized alerts, access schedules offline

</td>
</tr>
</table>

---

## 🎨 Core Value Proposition

<div align="center">

### **"Find Your Minyan. Never Miss A Shiur. Stay Connected."**

</div>

```mermaid
graph LR
    A[👥 Shul Admins] -->|Manage| B[📊 Shtetl Platform]
    B --> C[📱 Mobile Apps]
    B --> D[💬 SMS/Email]
    B --> E[🏠 Smart Building]
    B --> F[🔌 API/Webhooks]

    C --> G[👨‍👩‍👧‍👦 Community Members]
    D --> G
    E --> G
    F --> G

    style B fill:#4A90E2,stroke:#333,stroke-width:2px,color:#fff
    style G fill:#90EE90,stroke:#333,stroke-width:2px
```

---

## 🚀 Complete Developer Onboarding (Zero to Coding in 5 Minutes)

#### Step 1: Prerequisites (One-Time Setup)

Install these tools once on your machine:

**Docker Desktop:**

- macOS: [Download Docker Desktop](https://www.docker.com/products/docker-desktop)
- Linux: `sudo apt-get install docker.io && sudo usermod -aG docker $USER` (log out/in after)
- Windows: Docker Desktop with WSL2 backend

**Coder CLI:**

```bash
# macOS
brew install coder/coder/coder

# Linux / Windows WSL2
curl -fsSL https://coder.com/install.sh | sh
```

**Verify installations:**

```bash
docker --version
coder version
```

#### Step 2: Clone Repository

```bash
git clone https://github.com/jcom-dev/shtetl.git
cd shtetl
```

#### Step 3: First-Time Coder Setup

Start the Coder server and register the workspace template:

```bash
# Terminal 1: Start Coder server (keep running)
coder server

# Terminal 2: Login and register template
coder login http://localhost:3000    # Creates admin account on first run
coder templates push shtetl --directory .coder
```

> **Note:** The Coder server must be running whenever you use Coder commands.

#### Step 4: Add SSH Key to GitHub

The workspace needs SSH access to clone repositories:

```bash
# Create workspace (will generate SSH key)
coder create shtetl-dev --template shtetl

# Get the public key
coder ssh shtetl-dev -- "cat ~/.ssh/id_ed25519.pub 2>/dev/null || coder publickey"
```

1. Copy the key (starts with `ssh-ed25519`)
2. Add to GitHub: https://github.com/settings/ssh/new
3. Rebuild workspace to clone repos:

```bash
coder delete shtetl-dev --yes
coder create shtetl-dev --template shtetl
```

#### Step 5: What Happens Automatically

When the workspace is created:

- ✅ Creates PostgreSQL 18 and Redis 8.4 containers
- ✅ Installs Go 1.25.4 and Node.js 24.11.1
- ✅ Clones all three service repositories
- ✅ Installs all dependencies (`go mod download`, `npm install`)
- ✅ Configures environment variables

**Time:** 3-5 minutes

#### Step 6: Access Workspace

```bash
# Open in VS Code (recommended)
coder code shtetl-dev

# Or SSH into workspace
coder ssh shtetl-dev
```

#### Step 7: Services Auto-Start

Services start automatically in a tmux session. To view them:

```bash
# Attach to tmux session to see service logs
tmux attach -t shtetl

# Switch between windows: Ctrl+B then window number (0-3)
# Detach: Ctrl+B then D
```

**To start services manually** (if needed):

```bash
# From workspace root: /home/coder/workspace/shtetl

# Terminal 1: Zmanim Service (REST 8101)
cd submodules/shtetl-api/zmanim
go run cmd/zmanim/main.go

# Terminal 2: Shul Service (REST 8103)
cd submodules/shtetl-api/shul
go run cmd/shul/main.go

# Terminal 3: Kehilla Service (REST 8105)
cd submodules/shtetl-api/kehilla
go run cmd/kehilla/main.go

# Terminal 4: Web App (Next.js - port 8100)
cd submodules/shtetl-web
npm run dev
```

#### Step 8: Verify Everything Works

```bash
# Check service health
curl http://localhost:8103/health  # Shul service
curl http://localhost:8105/health  # Kehilla service

# Open web app
open http://localhost:8100

# Test database
psql postgresql://shtetl:shtetl_dev@localhost:5432/shtetl_dev -c "SELECT version();"

# Test Redis
redis-cli -h localhost -p 6379 ping  # Should return PONG
```

### Daily Workflow

```bash
# Start workspace (if stopped)
coder start shtetl-dev

# Open in VS Code
coder code shtetl-dev

# Stop workspace when done (saves resources)
coder stop shtetl-dev
```

### Customizing Workspace (Advanced)

Override repository URLs or branch when creating a workspace:

```bash
# Use a different branch for all repos
coder create shtetl-dev --template shtetl \
  --parameter shtetl_branch=feature/my-branch

# Override specific repository URLs
coder create shtetl-dev --template shtetl \
  --parameter shtetl_branch=develop \
  --parameter shtetl_api_repo=git@github.com:my-fork/shtetl-api.git
```

**Available parameters:**

| Parameter           | Default                                       | Description                    |
| ------------------- | --------------------------------------------- | ------------------------------ |
| `shtetl_branch`     | `main`                                        | Branch to checkout for all repos |
| `shtetl_repo`       | `git@github.com:jcom-dev/shtetl.git`          | Main repository URL            |
| `shtetl_infra_repo` | `git@github.com:jcom-dev/shtetl-infra.git`    | Infrastructure repository URL  |
| `shtetl_api_repo`   | `git@github.com:jcom-dev/shtetl-api.git`      | API services repository URL    |
| `shtetl_web_repo`   | `git@github.com:jcom-dev/shtetl-web.git`      | Web application repository URL |
| `shtetl_mobile_repo`| `git@github.com:jcom-dev/shtetl-mobile.git`   | Mobile app repository URL      |

### 🎯 Key Benefits

- **Container Isolation:** No conflicts with your host machine's Go/Node.js
- **Reproducible:** Every developer gets identical environment
- **Fast:** One command creates complete dev environment
- **Zero Config:** No manual setup required

### 📚 Detailed Documentation

For troubleshooting, platform-specific notes, and advanced configuration:

- [Coder Workspace Guide](.coder/README.md) - Comprehensive setup guide
- [Architecture Documentation](docs/architecture.md) - System design and decisions

---

## 🏗️ Architecture

Shtetl uses a **three-service microservices architecture** designed for multi-tenancy, scalability, and clear separation of concerns:

<div align="center">

```
┌──────────────────────────────────────────────────────────────────┐
│                        Frontend Clients                          │
├──────────────────┬───────────────────┬───────────────────────────┤
│  Zmanim Provider │   Shul Admin UI   │    Kehilla Mobile App    │
│  Builder (Next.js)│   (Next.js Web)   │   (React Native + Web)   │
│  [Authorities]   │    [Gaboim]       │     [Congregants]        │
└─────────┬────────┴─────────┬─────────┴────────────┬─────────────┘
          │                  │                      │
          │                  │                      │
┌─────────▼──────────────────▼──────────────────────▼─────────────┐
│                     REST API Layer (Go)                          │
│              Authentication: Clerk JWT Validation                │
└─────────┬──────────────────┬──────────────────────┬─────────────┘
          │                  │                      │
┌─────────▼─────────┐ ┌──────▼──────────┐ ┌────────▼──────────────┐
│  Zmanim Service   │ │  Shul Service   │ │   Kehilla Service     │
│  (Go + REST)      │ │  (Go + REST)    │ │   (Go + REST)         │
├───────────────────┤ ├─────────────────┤ ├───────────────────────┤
│ • Calendar calcs  │ │ • Minyan config │ │ • Schedule queries    │
│ • Stream publish  │ │ • Rule DSL      │ │ • Subscriptions       │
│ • Formula storage │ │ • Validation    │ │ • Notifications       │
│ • Version control │ │ • PDF generation│ │ • Multi-Shul follow   │
└─────────┬─────────┘ └──────┬──────────┘ └────────┬──────────────┘
          │                  │                      │
          └──────────────────┼──────────────────────┘
                             │
        ┌────────────────────▼─────────────────────────────────────┐
        │              Data & Infrastructure Layer                  │
        ├───────────────────────────────────────────────────────────┤
        │  PostgreSQL 18 (Multi-tenant with Row-Level Security)    │
        │  Redis 8.4 (Caching: schedules, streams, primitives)     │
        │  Clerk (Authentication & user management)                 │
        │  SMS/Email/Push providers (Notifications)                 │
        └───────────────────────────────────────────────────────────┘
```

</div>

### 🔑 Key Architectural Decisions

- **Multi-Tenancy:** Shared PostgreSQL database with application-level Row-Level Security (RLS) via `shul_id`
- **Tree Storage:** Minyan hierarchies stored in PostgreSQL JSONB columns (flexibility + ACID guarantees)
- **Service Communication:** REST API for all communication (Lambda-compatible architecture)
- **Caching Strategy:** Redis for schedules (24h TTL), calendar streams (7d TTL), primitives (1h TTL)
- **Authentication:** Clerk with JWT claims including `shul_id` for tenant isolation
- **Scalability:** Stateless services, horizontal scaling via load balancer, database read replicas

---

## 📊 Project Status & Roadmap

### 🎯 Current Phase: **Phase 1 - Planning Complete**

<div align="center">

| Phase                          | Status      | Description                                         |
| ------------------------------ | ----------- | --------------------------------------------------- |
| 🔍 **Phase 0: Discovery**      | ✅ Complete | Product Brief, Domain Research, Technical Research  |
| 📋 **Phase 1: Planning**       | ✅ Complete | PRD (100 FRs + 90 NFRs), Architecture, Domain Brief |
| 🎨 **Phase 2: UX Design**      | ⏳ Next     | User flows, wireframes, high-fidelity mockups       |
| 🏗️ **Phase 3: Epic Breakdown** | ⏳ Planned  | Epics and stories from PRD requirements             |
| 💻 **Phase 4: Implementation** | ⏳ Planned  | Sprint-based development with AI assistance         |
| 🧪 **Phase 5: Beta Testing**   | ⏳ Planned  | Design partner pilot (5+ Shuls)                     |

</div>

### 🎁 MVP Scope (Phase 4)

**Two Distinct Administrative UIs:**

#### 1️⃣ Zmanim Provider Builder (Rabbinic Authority Interface)

- [ ] Zmanim & Calendar Calculation DSL
- [ ] Daily time calculations (Alot, Netz, Shkiah, Tzait, all halachic times)
- [ ] Hebrew calendar & date calculations (holidays, fast days, special occasions)
- [ ] Multiple calculation methodologies (GRA, MGA, Baal Hatanya)
- [ ] Formula documentation with halachic sources
- [ ] Calendar stream publishing with version control
- [ ] Validation against reference libraries (KosherJava, Hebcal)

#### 2️⃣ Shul Admin UI (Synagogue Schedule Management)

- [ ] Tree-based minyan configuration (Type → Instance → Rules)
- [ ] Minyan Scheduling DSL with Downshift formula input + autocomplete
- [ ] Hierarchical primitive system (Global → Regional → Local)
- [ ] Real-time validation with 100% coverage guarantee
- [ ] Hebrew RTL PDF generation (formatted timetables)
- [ ] Base calendar selection + local customizations
- [ ] Audit trail for all changes

#### 3️⃣ Core Features

- [ ] Shiur & event listings
- [ ] User subscriptions & alerts (push/SMS/email)
- [ ] Automation hooks (MCP/Zapier integrations)
- [ ] Mobile app access (iOS 14+, Android 10+)
- [ ] Multi-Shul following for congregants

### 🚀 Post-MVP Vision (Phase 5+)

- [ ] Comprehensive Shul portal (membership, aliyos, yahrzeits)
- [ ] Full-featured mobile app with offline capabilities
- [ ] Public APIs & event hooks (webhook system)
- [ ] Shared learning libraries
- [ ] Multi-language support (Hebrew/Yiddish)
- [ ] Template marketplace (share configurations)
- [ ] Advanced DSL features (calculated times, constraints)
- [ ] Analytics dashboard (attendance patterns)

---

## 🤝 Contributing

We welcome contributions from developers, designers, and community members!

Shtetl uses a **poly-repo architecture** with git submodules. Understanding this structure is key to contributing effectively.

### 📦 Repository Structure

Shtetl consists of 5 repositories:

| Repository | Purpose | Tech Stack |
|------------|---------|------------|
| **shtetl** (main) | Orchestrator, documentation, cross-repo integration tests | Markdown, GitHub Actions |
| **shtetl-api** | Backend services (Zmanim, Shul, Kehilla) | Go 1.25.4, PostgreSQL, Redis |
| **shtetl-web** | Admin interfaces (Zmanim Builder, Shul Admin) | Next.js 16, TypeScript, Tailwind |
| **shtetl-mobile** | Mobile app for congregants | React Native, Expo |
| **shtetl-infra** | Infrastructure as code | CDKTF (TypeScript), AWS |

The main **shtetl** repo contains all others as git submodules under `submodules/`.

### 🚀 Quick Start for Contributors

#### 1. Fork the Repository You'll Be Working On

**Single-repo contribution** (70% of cases):
```bash
# Fork the specific submodule repo on GitHub
# Example: Fork jcom-dev/shtetl-api to your-username/shtetl-api
```

**Multi-repo contribution** (30% of cases):
```bash
# Fork multiple repos if your feature spans services
# Example: Fork both shtetl-api and shtetl-web
```

#### 2. Clone Main Repo with Submodules

```bash
git clone --recurse-submodules git@github.com:jcom-dev/shtetl.git
cd shtetl
```

#### 3. Point Submodule to Your Fork

```bash
# Navigate to the submodule you're working on
cd submodules/shtetl-api

# Add your fork as a remote
git remote add myfork git@github.com:your-username/shtetl-api.git

# Verify remotes
git remote -v
# origin    git@github.com:jcom-dev/shtetl-api.git (upstream)
# myfork    git@github.com:your-username/shtetl-api.git (your fork)
```

#### 4. Set Up Coder Workspace

**Option A: Use Default Workspace** (uses upstream repos)
```bash
coder create shtetl-dev --template shtetl
```

**Option B: Use Your Fork in Workspace** (recommended for contributors)
```bash
coder create shtetl-dev --template shtetl \
  --parameter shtetl_api_repo=git@github.com:your-username/shtetl-api.git \
  --parameter shtetl_branch=dev
```

This clones YOUR fork inside the Coder workspace, so changes you make are immediately testable.

#### 5. Open Workspace

```bash
# Open in VS Code
coder code shtetl-dev

# Or SSH in
coder ssh shtetl-dev
```

Services will auto-start in a tmux session. To view logs:
```bash
tmux attach -t shtetl
# Ctrl+B then window number to switch
# Ctrl+B then D to detach
```

### 🔨 Development Workflow

#### Single-Repository Contribution (70% of Features)

Most contributions only touch one repository (e.g., adding a new API endpoint, fixing a UI bug).

**Example: Adding a new Zmanim calculation endpoint**

```bash
# 1. Inside Coder workspace (or locally if you have dependencies installed)
cd /home/coder/workspace/shtetl/submodules/shtetl-api

# 2. Create feature branch from dev
git checkout dev
git pull origin dev
git checkout -b feature/add-chatzot-calculation

# 3. Make your changes
# Edit files, add tests, etc.

# 4. Run tests locally
go test ./...
golangci-lint run

# 5. Commit to your fork
git add .
git commit -m "feat(zmanim): Add Chatzot calculation endpoint"
git push myfork feature/add-chatzot-calculation

# 6. Open PR on GitHub
gh pr create --base dev --head your-username:feature/add-chatzot-calculation \
  --title "Add Chatzot calculation endpoint" \
  --body "Adds new endpoint for Chatzot (midday) calculations per GRA opinion"
```

**What happens next:**
1. CI runs tests on your PR (in shtetl-api repo)
2. Maintainer reviews and approves
3. PR merged to `shtetl-api/dev`
4. **Automatic deployment to dev-candidate environment** (GitHub Actions)
5. **Automatic cross-repo integration tests** (validates API + Web + Mobile still work together)
6. If integration tests pass → **Lambda promoted to dev** + **main repo updates submodule reference**
7. Your change is now in the integrated dev environment!

#### Cross-Repository Contribution (30% of Features)

Some features require changes to multiple repos (e.g., new API endpoint + frontend UI to consume it).

**Example: Adding minyan scheduling UI + API**

```bash
# 1. Create feature branches in BOTH repos (inside Coder workspace)

# API changes
cd /home/coder/workspace/shtetl/submodules/shtetl-api
git checkout dev && git pull origin dev
git checkout -b feature/minyan-scheduling-api

# Web changes
cd ../shtetl-web
git checkout dev && git pull origin dev
git checkout -b feature/minyan-scheduling-ui

# 2. Develop in parallel
# Switch between directories as needed
# Test integration locally (both services running in Coder)

# 3. Commit and push to your forks
cd ../shtetl-api
git add . && git commit -m "feat(shul): Add minyan scheduling API"
git push myfork feature/minyan-scheduling-api

cd ../shtetl-web
git add . && git commit -m "feat(admin): Add minyan scheduling UI"
git push myfork feature/minyan-scheduling-ui

# 4. Open PRs in BOTH repos
# API PR
cd ../shtetl-api
gh pr create --base dev --head your-username:feature/minyan-scheduling-api \
  --title "Add minyan scheduling API" \
  --body "Backend API for minyan schedule CRUD operations.

**Related PR:** jcom-dev/shtetl-web#42"

# Web PR
cd ../shtetl-web
gh pr create --base dev --head your-username:feature/minyan-scheduling-ui \
  --title "Add minyan scheduling UI" \
  --body "Admin interface for managing minyan schedules.

**Depends on:** jcom-dev/shtetl-api#37
This PR should be merged AFTER the API PR."
```

**Merge order for cross-repo features:**
1. **Backend first** (shtetl-api) - prevents broken deployments
2. **Frontend second** (shtetl-web) - can safely consume deployed API
3. Main repo integration tests validate the full flow

### 🧪 Testing Your Changes

#### Local Testing (Coder Workspace)

All services run automatically in Coder:

```bash
# Check service health
curl http://localhost:8101/health  # Zmanim service
curl http://localhost:8103/health  # Shul service
curl http://localhost:8105/health  # Kehilla service
curl http://localhost:8100          # Web app

# Run unit tests
cd submodules/shtetl-api
go test ./...

cd ../shtetl-web
npm test
```

#### Integration Testing

After your PR is merged and deployed, automatic integration tests run in the main repo. These tests verify:
- API → Web integration
- API → Mobile integration
- End-to-end user flows

**If integration tests fail:**
- Lambda promotion to dev is BLOCKED
- A GitHub issue is created automatically
- You'll be notified (tagged in the issue)
- Your deployment stays at dev-candidate for debugging
- Investigate and fix, then push another PR

### 🌿 Branch Strategy

| Branch | Purpose | Protection |
|--------|---------|-----------|
| **main** | Production releases | Protected: Requires 1 review, status checks must pass |
| **dev** | Development integration | Protected: Requires 1 review, status checks must pass |
| **feature/*** | Your feature branches | Create from `dev`, merge back to `dev` |
| **fix/*** | Bug fixes | Create from `dev`, merge back to `dev` |

### 📋 PR Requirements

Before your PR can be merged:

✅ All tests must pass (unit + integration)
✅ Code review approved by maintainer
✅ Code style checks pass (linters)
✅ New features include tests
✅ Breaking changes include migration guide
✅ Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/)

**Commit message format:**
```
feat(scope): Add new feature
fix(scope): Fix bug
docs: Update documentation
refactor: Refactor code
test: Add tests
chore: Update dependencies
```

### 🗄️ Database Migrations

When your change requires database schema changes:

#### 1. Create Migration Files

```bash
cd submodules/shtetl-api

# Create migration (use descriptive name)
migrate create -ext sql -dir db/migrations -seq add_minyan_scheduling

# This creates:
# db/migrations/000XXX_add_minyan_scheduling.up.sql
# db/migrations/000XXX_add_minyan_scheduling.down.sql
```

#### 2. Write Backwards-Compatible Migrations (EXPAND-only)

✅ **DO: Expand (Additive Changes)**
- Add new columns (with DEFAULT values or NULL)
- Add new tables
- Add new indexes

```sql
-- ✅ Good: Backwards-compatible
ALTER TABLE shuls ADD COLUMN timezone VARCHAR(50) DEFAULT 'America/New_York';
```

❌ **DON'T: Contract (Breaking Changes)**
- Drop columns (do this in a future deployment after new code is deployed)
- Rename columns (use expand-contract: add new, copy data, drop old later)

```sql
-- ❌ Bad: Breaks old Lambda code still running
ALTER TABLE shuls DROP COLUMN old_field;
```

#### 3. Migration Checklist

Before submitting PR with migrations:

- [ ] Migration is EXPAND-only (backwards-compatible)
- [ ] New columns have DEFAULT values or allow NULL
- [ ] No DROP, RENAME, or ALTER TYPE operations
- [ ] Tested locally with services running
- [ ] Down migration created (for rollback)

**For breaking changes,** split into 2 PRs:
1. PR 1: EXPAND (add new field, dual-write)
2. PR 2: CONTRACT (remove old field, 24-48h later)

### 🔄 Keeping Your Fork Updated

```bash
# Inside the submodule
cd submodules/shtetl-api

# Fetch latest from upstream
git fetch origin

# Update your local dev branch
git checkout dev
git merge origin/dev

# Push to your fork
git push myfork dev

# Rebase your feature branch (if needed)
git checkout feature/your-feature
git rebase dev
```

### 🐛 Troubleshooting

**"Submodule is detached HEAD"**
```bash
cd submodules/shtetl-api
git checkout dev
```

**"Can't push to origin"**
```bash
# You need to push to your fork, not upstream
git push myfork feature/your-branch
```

**"Services not starting in Coder"**
```bash
# Attach to tmux to see logs
tmux attach -t shtetl

# Manually restart a service
cd /home/coder/workspace/shtetl/submodules/shtetl-api/zmanim
go run cmd/server/main.go
```

**"Integration tests failed after my merge"**
1. Check the GitHub issue created by the integration workflow
2. Review the test output logs
3. Debug at dev-candidate endpoint: https://api-dev-candidate.shtetl.com
4. Fix the issue in a new PR
5. Comment on the issue when fixed

### 🎨 Areas We Need Help

<table>
<tr>
<td>

**💻 Leadership**

- Technical Leadership
- Project Management
- Architecture & Design
- PR Reviews

</td>
<td>
&nbsp;

**💻 Development**

- Frontend (Next.js/React Native)
- Backend (Golang)
- Mobile development
- Business Analysis
- QA

&nbsp;

</td>
<td>

**🎨 Design**

- UI/UX design
- Mobile app design
- Logo & branding
- Documentation

</td>
<td>

**📝 Community**

- Testing & feedback
- Translation
- Community outreach
- Design Partners

</td>
</tr>
</table>

---

## 💬 Community

Join our growing community of contributors and users!

<div align="center">

[![Slack](https://img.shields.io/badge/Slack-Join_Workspace-4A154B?style=for-the-badge&logo=slack&logoColor=white)](#)
[![GitHub Discussions](https://img.shields.io/badge/GitHub-Discussions-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/jcom-dev/shtetl/discussions)

</div>

### 🌟 Success Metrics

<div align="center">

| Metric                        | MVP Target (6 months)          | Current |
| ----------------------------- | ------------------------------ | ------- |
| 🏛️ **Active Shuls**           | 5+ maintaining schedules       | 0       |
| 👥 **Congregant Subscribers** | 70%+ of active kehilla members | 0       |
| 📊 **Rabbinic Authorities**   | 1+ publishing calendar streams | 0       |
| 🤖 **Automation Hooks**       | 1+ per participating Shul      | 0       |
| ⚡ **Schedule Lookup Time**   | 80%+ under 30 seconds          | -       |
| 🎯 **Halachic Accuracy**      | Zero breaches                  | -       |

</div>

**Quality Goals:**

- 60%+ of users no longer need to call gabbaim for schedule information
- 60%+ reduction in time gaboim spend on manual notifications
- 99.5% API uptime (excluding planned maintenance)

---

## 📖 Documentation

### Planning Documents (Phase 0-1 Complete)

- 📋 **[Product Brief](docs/brief.md)** - Comprehensive product vision, personas, and domain context
- 📐 **[Architecture Document](docs/architecture.md)** - Three-service microservices architecture with technology stack
- 📑 **[Product Requirements Document (PRD)](docs/prd.md)** - 100 Functional Requirements + 90 Non-Functional Requirements
- 🏛️ **[Domain Brief](docs/domain-brief.md)** - Orthodox community management domain analysis and design patterns
- 🔬 **[Technical Research](docs/research-technical-2025-11-17.md)** - Zmanim libraries, Hebrew calendar, PDF generation evaluation

### Workflow Tracking

- 📊 **[BMM Workflow Status](docs/bmm-workflow-status.yaml)** - Current progress through BMad Method phases

---

## 🛡️ Technical Stack

<div align="center">

**Frontend**

![Next.js](https://img.shields.io/badge/Next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)
![React Native](https://img.shields.io/badge/React_Native-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![Expo](https://img.shields.io/badge/Expo-000020?style=for-the-badge&logo=expo&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwindcss&logoColor=white)

**Backend**

![Go](https://img.shields.io/badge/Go-00ADD8?style=for-the-badge&logo=go&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![AWS Lambda](https://img.shields.io/badge/AWS_Lambda-FF9900?style=for-the-badge&logo=awslambda&logoColor=white)

**Infrastructure & Tools**

![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)
![AWS CDK](https://img.shields.io/badge/AWS_CDK-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Coder](https://img.shields.io/badge/Coder-000000?style=for-the-badge&logo=coder&logoColor=white)

**Auth & Libraries**

![Clerk](https://img.shields.io/badge/Clerk-6C47FF?style=for-the-badge&logo=clerk&logoColor=white)
![Downshift](https://img.shields.io/badge/Downshift-FF6B6B?style=for-the-badge&logo=npm&logoColor=white)

**DevOps & Methodology**

![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)
![GitHub Projects](https://img.shields.io/badge/GitHub_Projects-181717?style=for-the-badge&logo=github&logoColor=white)
![BMAD](https://img.shields.io/badge/BMAD_Method-4A90E2?style=for-the-badge&logo=markdown&logoColor=white)

</div>

### Stack Rationale

- **Go Backend:** High performance, excellent concurrency, strong typing, mature ecosystem
- **PostgreSQL 18:** JSONB for tree structures, ACID guarantees, Row-Level Security for multi-tenancy
- **Redis 8.4:** Caching layer for schedule queries, calendar streams, primitives
- **Next.js + TypeScript:** Type-safe frontend development with server-side rendering, file-based routing, and modern tooling
- **React Native + Expo:** Cross-platform mobile development with Expo Router for file-based navigation, EAS for streamlined app store deployment
- **Downshift:** Lightweight headless autocomplete library (~14KB) for formula input with context-aware suggestions
- **Clerk:** Modern auth provider with JWT, organizations, user metadata
- **AWS Lambda:** Serverless compute with pay-per-use pricing, auto-scaling, and Lambda-compatible service architecture

---

## 📜 License

This project is open source and available under the [GPL License](LICENSE).

---

## 🙏 Acknowledgments

- All our amazing contributors and community members
- The Shuls partnering with us as design partners
- Open source projects that make this possible

---

<div align="center">

### **🌟 Together, we're building the future of Jewish community connectivity 🌟**

**Made with ❤️ by the Jewish tech community**

[⬆ Back to top](#-shtetl---unite-your-jewish-community)

</div>
