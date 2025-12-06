# Shtetl Coder Workspace

This directory contains the Coder workspace template for the Shtetl platform.

## Setup Instructions

**See the main [README.md](../README.md#-complete-developer-onboarding-zero-to-coding-in-5-minutes) for complete setup instructions.**

This document provides additional details, troubleshooting, and advanced configuration.

### Prerequisites: SSH Key Setup (Do This First!)

Coder uses a single SSH key for all Git operations across all workspaces. This key is tied to the Coder server, not individual workspaces. **You must add this key to GitHub before creating workspaces**, otherwise repository cloning will fail.

```bash
# 1. Get the Coder SSH public key
coder publickey

# 2. Copy the output (starts with ssh-ed25519...)

# 3. Add it to GitHub: https://github.com/settings/ssh/new
#    - Title: "Coder Server"
#    - Key: paste the public key
```

Only after the SSH key is added to GitHub should you proceed with template deployment and workspace creation.

## What's Included

### Services

| Service    | Port      | Type     | Description                        |
| ---------- | --------- | -------- | ---------------------------------- |
| PostgreSQL | 5432      | Database | PostgreSQL 18 (postgres:18-alpine) |
| Redis      | 6379      | Cache    | Redis 8.4 (redis:8.4-alpine)       |
| Web App    | 8100      | HTTP     | Next.js web application            |
| Zmanim     | 8101      | REST     | Zmanim calculation service         |
| Shul       | 8103      | REST     | Shul administration service        |
| Kehilla    | 8105      | REST     | Community API service              |

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
WEB_PORT=8100
ZMANIM_REST_PORT=8101
SHUL_REST_PORT=8103
KEHILLA_PORT=8105
```

## Starting Services

After workspace creation, start the services:

### Backend Services

```bash
# Terminal 1: Zmanim Service (REST)
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
# Zmanim Service (REST)
curl http://localhost:8101/health
# Expected: {"status":"ok","service":"zmanim"}

# Shul Service (REST)
curl http://localhost:8103/health
# Expected: {"status":"ok","service":"shul"}

# Kehilla Service
curl http://localhost:8105/health
# Expected: {"status":"ok","service":"kehilla"}

# Web App
open http://localhost:8100
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
Coder Workspace
├── Development Container
│   ├── Go 1.25.4
│   ├── Node.js 24.x LTS
│   └── Git, npm, Docker CLI
│
└── Docker Network
    ├── PostgreSQL 18 (port 5432)
    └── Redis 8.4 (port 6379)
```

**Services:**
- Zmanim Service: REST API on port 8101
- Shul Service: REST API on port 8103
- Kehilla Service: REST API on port 8105
- Web App: Next.js on port 8100

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
lsof -i :8100  # Web
lsof -i :8101  # Zmanim REST
lsof -i :8103  # Shul REST
lsof -i :8105  # Kehilla

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

#### Running Coder as a Systemd Service (Linux/WSL2 Only)

Coder can run as a systemd service, which provides automatic startup on boot, proper process management, and runs under a dedicated `coder` user for improved security isolation.

> **Note:** Systemd is only available on Linux and WSL2. macOS users should run Coder manually or use launchd.

**Why use systemd?**
- **Automatic startup** - Coder starts automatically when the system boots
- **Process supervision** - Systemd restarts Coder if it crashes
- **Security isolation** - Runs under a dedicated `coder` user instead of your personal account
- **Logging** - Integrated with journald for centralized log management

**Setup Steps:**

```bash
# 1. Enable and start the Coder systemd service
sudo systemctl enable --now coder

# 2. Grant the coder user access to Docker
# The coder service runs as a dedicated 'coder' user, which needs
# Docker access to create and manage workspace containers
sudo usermod -aG docker coder

# 3. Restart the Coder service to apply group changes
sudo systemctl restart coder

# 4. Verify the service is running
sudo systemctl status coder

# 5. Get the SSH public key and add it to GitHub BEFORE creating workspaces
coder publickey
# Copy the output and add to: https://github.com/settings/ssh/new
```

**Important:** Complete step 5 before pushing templates or creating workspaces. The SSH key is generated once for the Coder server and shared across all workspaces. Without it, Git clone operations will fail during workspace startup.

**Why `usermod -aG docker coder`?**

When Coder runs as a systemd service, it operates under a dedicated system user called `coder` (not your personal user account). This user needs permission to interact with the Docker daemon to:
- Create workspace containers
- Manage container networks
- Pull images and manage volumes

Without this permission, workspace creation will fail with "permission denied" errors when trying to access the Docker socket.

**Checking Coder Service Logs:**

```bash
# View recent logs
journalctl -u coder -n 50

# Follow logs in real-time
journalctl -u coder -f

# View logs since last boot
journalctl -u coder -b
```

## Development Workflow

1. **Create workspace** - `coder create shtetl-dev --template shtetl`
2. **Wait for initialization** - Startup script runs automatically (3-5 minutes)
3. **Start services** - Run each service in separate terminals
4. **Develop** - Make changes, services auto-reload
5. **Test** - Run tests, check health endpoints
6. **Commit** - Git commit and push changes
7. **Stop workspace** - `coder stop shtetl-dev` (optional, saves resources)
8. **Restart workspace** - `coder start shtetl-dev` (fast, ~30 seconds)

## Using Your Own Fork or Branch

### For Contributors

If you're contributing to Shtetl, you'll want the workspace to use your fork instead of the upstream repos.

#### Option 1: Specify Fork During Workspace Creation (Recommended)

```bash
# Create workspace with your fork
coder create shtetl-dev --template shtetl \
  --parameter shtetl_api_repo=git@github.com:your-username/shtetl-api.git \
  --parameter shtetl_branch=dev
```

**All Available Parameters:**

| Parameter           | Default                                       | Description                    |
| ------------------- | --------------------------------------------- | ------------------------------ |
| `shtetl_branch`     | `main`                                        | Branch to checkout for all repos |
| `shtetl_repo`       | `git@github.com:jcom-dev/shtetl.git`          | Main repository URL            |
| `shtetl_infra_repo` | `git@github.com:jcom-dev/shtetl-infra.git`    | Infrastructure repository URL  |
| `shtetl_api_repo`   | `git@github.com:jcom-dev/shtetl-api.git`      | API services repository URL    |
| `shtetl_web_repo`   | `git@github.com:jcom-dev/shtetl-web.git`      | Web application repository URL |
| `shtetl_mobile_repo`| `git@github.com:jcom-dev/shtetl-mobile.git`   | Mobile app repository URL      |

**Examples:**

```bash
# Use your API fork on a feature branch
coder create shtetl-dev --template shtetl \
  --parameter shtetl_api_repo=git@github.com:myusername/shtetl-api.git \
  --parameter shtetl_branch=feature/minyan-scheduling

# Use multiple forks for cross-repo development
coder create shtetl-dev --template shtetl \
  --parameter shtetl_api_repo=git@github.com:myusername/shtetl-api.git \
  --parameter shtetl_web_repo=git@github.com:myusername/shtetl-web.git \
  --parameter shtetl_branch=feature/minyan-scheduling

# Work on a specific feature branch across all repos
coder create shtetl-dev --template shtetl \
  --parameter shtetl_branch=feature/my-feature
```

#### Option 2: Manual Fork Setup After Creation

If you already created the workspace with upstream repos:

```bash
# SSH into workspace
coder ssh shtetl-dev

# Navigate to submodule
cd /home/coder/workspace/shtetl/submodules/shtetl-api

# Add your fork as remote
git remote add myfork git@github.com:your-username/shtetl-api.git

# Checkout your feature branch
git fetch myfork
git checkout -b feature/my-feature myfork/feature/my-feature

# Restart the service to use new code
# (Find the tmux window or restart manually)
tmux attach -t shtetl
# Navigate to service window, Ctrl+C to stop, then restart
```

#### Option 3: Switch Branches After Creation

```bash
# SSH into workspace
coder ssh shtetl-dev

# Switch to a different branch
cd /home/coder/workspace/shtetl/submodules/shtetl-api
git fetch origin
git checkout feature/different-branch

# Restart services for changes to take effect
cd zmanim
go run cmd/server/main.go
```

### Testing Your Changes

Once you've set up your fork:

1. **Make code changes** in VS Code or your editor
2. **Services auto-reload** (or restart manually)
3. **Run tests:**
   ```bash
   cd /home/coder/workspace/shtetl/submodules/shtetl-api
   go test ./...
   ```
4. **Commit and push to your fork:**
   ```bash
   git add .
   git commit -m "feat: Add new feature"
   git push myfork feature/my-branch
   ```
5. **Open PR** on GitHub from your fork to upstream

### SSH Key Setup

For private repos or forks, ensure your SSH key is configured:

```bash
# Generate SSH key in workspace (if not exists)
coder ssh shtetl-dev -- "test -f ~/.ssh/id_ed25519 || ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -N ''"

# Get public key
coder ssh shtetl-dev -- "cat ~/.ssh/id_ed25519.pub"

# Add to GitHub: https://github.com/settings/ssh/new
```

After adding the key to GitHub, rebuild your workspace or manually clone your fork.

## BMAD Integration

This workspace is designed for AI-first development with BMAD v6:

- **Zero Configuration Drift** - Every workspace is identical
- **Fast Onboarding** - New contributors productive in <5 minutes
- **AI-Optimized** - Consistent environment for Claude Code and other AI agents
- **MCP Support** - Model Context Protocol servers pre-configured

## Next Steps

- **Story 1.3** - Full CI/CD Pipeline (CDKTF + AWS Lambda + REST APIs)
- **Story 1.4** - Integration Testing Framework
- **Story 1.5** - Monitoring & Observability
- **Story 1.6** - API Contract Design (OpenAPI specs)

## References

- [Coder Documentation](https://coder.com/docs)
- [Architecture](../docs/architecture.md)
- [Epics](../docs/epics.md)
- [Story 1.2](../docs/sprint-artifacts/1-2-coder-workspace-template.md)
