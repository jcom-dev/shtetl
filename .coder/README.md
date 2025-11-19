# Shtetl Coder Workspace

This directory contains the Coder workspace template for the Shtetl platform.

## Setup Instructions

**See the main [README.md](../README.md#-complete-developer-onboarding-zero-to-coding-in-5-minutes) for complete setup instructions.**

This document provides additional details, troubleshooting, and advanced configuration.

## What's Included

### Services

| Service    | Port | Type     | Description                        |
| ---------- | ---- | -------- | ---------------------------------- |
| PostgreSQL | 5432 | Database | PostgreSQL 18 (postgres:18-alpine) |
| Redis      | 6379 | Cache    | Redis 8.4 (redis:8.4-alpine)       |
| Zmanim     | 8001 | gRPC     | Zmanim calculation service         |
| Shul       | 8002 | REST     | Shul administration service        |
| Kehilla    | 8003 | REST     | Community API service              |
| Web App    | 3000 | HTTP     | Next.js web application            |

### Development Tools

**Installed in Container:**

- **Go 1.25.4** - Backend services
- **Node.js 24.11.1 LTS** - Frontend applications
- **npm** - Package manager (comes with Node.js)
- **Git** - Version control
- **Docker** - Container runtime

> **Note:** Each Coder workspace runs in an isolated Docker container, so there's no conflict with your host machine's Go or Node.js installations. The container provides complete environment isolation.

### Environment Variables

The workspace automatically configures:

```bash
# Database
DATABASE_URL=postgresql://shtetl:shtetl_dev@postgres:5432/shtetl_dev
POSTGRES_HOST=postgres
POSTGRES_PORT=5432
POSTGRES_USER=shtetl
POSTGRES_PASSWORD=shtetl_dev
POSTGRES_DB=shtetl_dev

# Redis
REDIS_URL=redis://redis:6379
REDIS_HOST=redis
REDIS_PORT=6379

# Service Ports
ZMANIM_PORT=8001
SHUL_PORT=8002
KEHILLA_PORT=8003
WEB_PORT=3000
```

## Starting Services

After workspace creation, start the services:

### Backend Services

```bash
# Terminal 1: Zmanim Service (gRPC)
cd submodules/shtetl-api/zmanim
go run cmd/zmanim/main.go

# Terminal 2: Shul Service (REST)
cd submodules/shtetl-api/shul
go run cmd/shul/main.go

# Terminal 3: Kehilla Service (REST)
cd submodules/shtetl-api/kehilla
go run cmd/kehilla/main.go
```

### Frontend Applications

```bash
# Terminal 4: Web App (Next.js)
cd submodules/shtetl-web
npm run dev

# Terminal 5: Mobile App (Expo)
cd submodules/shtetl-mobile
npm start
```

## Health Checks

Verify all services are running:

```bash
# Zmanim Service
curl http://localhost:8001/health
# Expected: (gRPC health endpoint - will be added in Story 1.11)

# Shul Service
curl http://localhost:8002/health
# Expected: {"status":"ok","service":"shul"}

# Kehilla Service
curl http://localhost:8003/health
# Expected: {"status":"ok","service":"kehilla"}

# Web App
open http://localhost:3000
# Expected: Next.js app loads

# PostgreSQL
psql postgresql://shtetl:shtetl_dev@localhost:5432/shtetl_dev -c "SELECT version();"

# Redis
redis-cli -h localhost -p 6379 ping
# Expected: PONG
```

## Architecture

The Coder workspace uses Docker containers orchestrated by Terraform:

```
┌─────────────────────────────────────────┐
│         Coder Workspace                 │
│  ┌───────────────────────────────────┐  │
│  │   Development Container           │  │
│  │   - Go 1.25.4                     │  │
│  │   - Node.js 24.x                  │  │
│  │   - Git, npm, etc.                │  │
│  └───────────────┬───────────────────┘  │
│                  │                       │
│  ┌───────────────▼───────────────────┐  │
│  │   Docker Network                  │  │
│  │  ┌─────────┐  ┌──────────┐       │  │
│  │  │PostgreSQL│  │  Redis   │       │  │
│  │  │  :5432  │  │  :6379   │       │  │
│  │  └─────────┘  └──────────┘       │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

## Files

- **`shtetl-workspace.tf`** - Terraform template defining the workspace
- **`startup.sh`** - Initialization script run on workspace creation
- **`README.md`** - This file

## Troubleshooting

### "You are not logged in" error

```bash
# 1. Make sure Coder server is running
coder server

# 2. Login to your local instance (in a new terminal)
coder login http://localhost:3000
```

### "Template not found" error

```bash
# The template needs to be registered before creating workspaces
coder templates push shtetl --directory .coder
```

### Git clone fails with "Host key verification failed"

The Coder workspace SSH key needs to be added to your GitHub account:

```bash
# Get the public key from the workspace
coder ssh shtetl-dev -- "cat ~/.ssh/id_ed25519.pub 2>/dev/null || coder publickey"

# Add it to GitHub: https://github.com/settings/ssh/new
# Then rebuild the workspace
coder delete shtetl-dev --yes
coder create shtetl-dev --template shtetl
```

### Docker permission denied error

```bash
# Add your user to the docker group
sudo usermod -aG docker $USER

# Apply the new group (without logging out)
newgrp docker

# Verify Docker access
docker ps

# Retry template push
coder templates push shtetl --directory .coder
```

### Workspace creation fails

```bash
# Check Docker is running
docker ps

# Check Coder is running
coder version

# View Coder logs
coder logs
```

### Services won't start

```bash
# Check if ports are already in use
lsof -i :5432  # PostgreSQL
lsof -i :6379  # Redis
lsof -i :8001  # Zmanim
lsof -i :8002  # Shul
lsof -i :8003  # Kehilla
lsof -i :3000  # Web

# Restart PostgreSQL container
docker restart coder-<workspace-id>-postgres

# Restart Redis container
docker restart coder-<workspace-id>-redis
```

### Go dependencies fail to install

```bash
# Manually download dependencies
cd submodules/shtetl-api/zmanim
go mod download

cd ../shul
go mod download

cd ../kehilla
go mod download
```

### Node dependencies fail to install

```bash
# Clear npm cache and reinstall
cd submodules/shtetl-web
rm -rf node_modules package-lock.json
npm install

cd ../shtetl-mobile
rm -rf node_modules package-lock.json
npm install
```

## Platform-Specific Notes

### macOS

- Docker Desktop must be running
- Ensure sufficient resources allocated (4GB RAM minimum, 8GB recommended)

### Linux (Ubuntu 22.04)

- Install Docker: `sudo apt-get install docker.io`
- Add user to docker group: `sudo usermod -aG docker $USER`
- Log out and back in for group changes to take effect

### Windows WSL2

- Install WSL2 with Ubuntu
- Install Docker Desktop for Windows with WSL2 backend
- Run Coder from within WSL2 terminal

## Development Workflow

1. **Create workspace** - `coder create shtetl-dev --template shtetl`
2. **Wait for initialization** - Startup script runs automatically (3-5 minutes)
3. **Start services** - Run each service in separate terminals
4. **Develop** - Make changes, services auto-reload
5. **Test** - Run tests, check health endpoints
6. **Commit** - Git commit and push changes
7. **Stop workspace** - `coder stop shtetl-dev` (optional, saves resources)
8. **Restart workspace** - `coder start shtetl-dev` (fast, ~30 seconds)

## BMAD Integration

This workspace is designed for AI-first development with BMAD v6:

- **Zero Configuration Drift** - Every workspace is identical
- **Fast Onboarding** - New contributors productive in <5 minutes
- **AI-Optimized** - Consistent environment for Claude Code and other AI agents
- **MCP Support** - Model Context Protocol servers pre-configured

## Next Steps

- **Story 1.3** - API Contract Design (OpenAPI specs, protobuf definitions)
- **Story 1.4** - Domain Sample Data Fixtures
- **Story 1.5** - Mock API Servers
- **Story 1.10** - Full CI/CD Pipeline

## References

- [Coder Documentation](https://coder.com/docs)
- [Architecture](../docs/architecture.md)
- [Epics](../docs/epics.md)
- [Story 1.2](../docs/sprint-artifacts/1-2-coder-workspace-template.md)
