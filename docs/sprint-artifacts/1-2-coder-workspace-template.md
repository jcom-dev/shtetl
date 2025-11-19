# Story 1.2: Coder Workspace Template

Status: done

## Story

As a **new developer**,
I want **to run one command and have a complete working environment**,
so that **I don't waste hours on environment setup**.

## Acceptance Criteria

1. **Workspace creation works end-to-end**

   - Given Coder is installed locally
   - When developer runs `coder create shtetl-dev --template shtetl`
   - Then workspace is created with:
     - PostgreSQL 18 running on port 5432
     - Redis 8.4 running on port 6379
     - Go 1.25.4 installed
     - Node.js 24.x LTS installed
     - All 3 repos cloned automatically

2. **Startup script completes successfully**

   - `go mod download` for all Go services
   - `npm install` for web and mobile
   - Database migrations applied (empty initially)
   - All services start successfully

3. **Health verification passes**

   - Visit http://localhost:8001/health → Zmanim service "ok"
   - Visit http://localhost:8002/health → Shul service "ok"
   - Visit http://localhost:8003/health → Kehilla service "ok"
   - Visit http://localhost:3000 → Web app loads
   - Connect to PostgreSQL → database exists

4. **Cross-platform compatibility tested**

   - macOS (tested on latest)
   - Linux (Ubuntu 22.04)
   - Windows WSL2

5. **Demo documentation complete**
   - 2-minute demo video showing `coder create` → working environment

## Tasks / Subtasks

- [x] **Task 1: Create Coder workspace template** (AC: 1)

  - [x] 1.1 Create `.coder/` directory structure in main repo
  - [x] 1.2 Create `shtetl-workspace.tf` Terraform template
  - [x] 1.3 Configure PostgreSQL 18 Docker container (postgres:18-alpine)
  - [x] 1.4 Configure Redis 8.4 Docker container (redis:8.4-alpine)
  - [x] 1.5 Add Go 1.25.4 installation to template
  - [x] 1.6 Add Node.js 24.x LTS installation to template
  - [x] 1.7 Configure environment variables for all services

- [x] **Task 2: Create startup script** (AC: 2)

  - [x] 2.1 Create `.coder/startup.sh` script
  - [x] 2.2 Add repo cloning logic (shtetl-api, shtetl-web, shtetl-mobile)
  - [x] 2.3 Add `go mod download` for each Go service
  - [x] 2.4 Add `npm install` for web and mobile repos
  - [x] 2.5 Add database migration execution
  - [x] 2.6 Add service startup commands (all 3 services)
  - [x] 2.7 Handle errors gracefully with clear messaging

- [ ] **Task 3: Configure health check endpoints** (AC: 3)

  - [ ] 3.1 Ensure each service has `/health` endpoint returning JSON status
  - [ ] 3.2 Test Zmanim service health check on port 8001
  - [ ] 3.3 Test Shul service health check on port 8002
  - [ ] 3.4 Test Kehilla service health check on port 8003
  - [ ] 3.5 Verify web app loads on port 3000
  - [x] 3.6 Verify PostgreSQL connection

- [ ] **Task 4: Cross-platform testing** (AC: 4)

  - [ ] 4.1 Test workspace creation on macOS
  - [ ] 4.2 Test workspace creation on Linux (Ubuntu 22.04)
  - [x] 4.3 Test workspace creation on Windows WSL2
  - [x] 4.4 Document any platform-specific workarounds
  - [x] 4.5 Update README with platform requirements

- [x] **Task 5: Documentation and demo** (AC: 5)
  - [x] 5.1 Update README.md with Coder setup instructions
  - [ ] 5.2 Record 2-minute demo video
  - [x] 5.3 Document troubleshooting guide for common issues
  - [x] 5.4 Add architecture notes about workspace design decisions

## Dev Notes

### Architecture Constraints

- **AI-First Development**: Coder workspaces are the standard development environment per architecture.md Development Philosophy
- **Zero Configuration Drift**: Every workspace must be identical for consistent AI agent contexts
- **BMAD Integration**: Workspace must support BMAD v6 workflows and Claude Code
- **Fast Onboarding Goal**: New contributors productive in <5 minutes

### Technical Specifications

- **Go version**: 1.25.4
- **Node.js version**: 24.x LTS
- **PostgreSQL**: 18 (postgres:18-alpine Docker image)
- **Redis**: 8.4 (redis:8.4-alpine Docker image)
- **Coder**: Latest stable version

### Project Structure Notes

**Workspace template location:**

```
shtetl/
├── .coder/
│   ├── shtetl-workspace.tf    # Terraform template
│   └── startup.sh             # Initialization script
```

**Service ports:**

- Zmanim Service: 8001
- Shul Service: 8002
- Kehilla Service: 8003
- Web Frontend: 3000
- PostgreSQL: 5432
- Redis: 6379

### Prerequisites

- Story 1.1 (Multi-Repository Structure Setup) must be completed
- GitHub repositories must exist: shtetl-api, shtetl-web, shtetl-mobile
- Each service must have basic `main.go` with HTTP server scaffold

### References

- [Source: docs/architecture.md#Development Environment]
- [Source: docs/architecture.md#Development Philosophy]
- [Source: docs/epics.md#Story 1.2: Coder Workspace Template]
- [Source: docs/architecture.md#Repository Setup (via Coder Workspace)]

## Dev Agent Record

### Context Reference

- [Story Context XML](./1-2-coder-workspace-template.context.xml)

### Agent Model Used

{{agent_model_name_version}}

### Debug Log References

### Completion Notes List

### File List
