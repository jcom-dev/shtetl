# Story 1.1: Multi-Repository Structure Setup

Status: ready-for-dev

## Story

As a **developer**,
I want **a clear multi-repo structure with initialized codebases**,
so that **I can start contributing to the appropriate service immediately**.

## Acceptance Criteria

1. **Backend Repository (`shtetl-api/`) initialized**
   - Go module initialized with `go.mod`
   - Three service directories created: `services/zmanim/`, `services/shul/`, `services/kehilla/`
   - Each service has standard Go structure: `cmd/main.go`, `internal/`, `api/`, `pkg/`
   - Each service has basic `main.go` entry point (hello world or minimal server)
   - README.md with setup instructions and architecture overview
   - `.gitignore` for Go projects

2. **Web Frontend Repository (`shtetl-web/`) initialized**
   - Vite + React 19 + TypeScript initialized via `npm create vite@latest`
   - `package.json` with correct dependencies
   - `tsconfig.json` with strict TypeScript configuration
   - Basic folder structure: `src/features/`, `src/shared/`, `src/App.tsx`, `src/main.tsx`
   - README.md with setup instructions
   - `.gitignore` for Node.js projects

3. **Mobile Repository (`shtetl-mobile/`) initialized**
   - Expo + React Native + TypeScript initialized via `npx create-expo-app`
   - `package.json` with Expo SDK 54
   - `tsconfig.json` with TypeScript configuration
   - Basic folder structure: `src/screens/`, `src/components/`, `src/services/`
   - `app.json` with proper Expo configuration
   - README.md with setup instructions
   - `.gitignore` for React Native/Expo projects

4. **All repositories linked as submodules**
   - Main `shtetl/` repo has submodules configured in `submodules/` directory
   - `.gitmodules` properly configured with all three repos
   - Submodules can be cloned with `git clone --recursive`

5. **Documentation complete**
   - Each repo has README.md explaining purpose, setup, and contribution guidelines
   - Architecture overview references correct repo for each component

## Tasks / Subtasks

- [ ] **Task 1: Initialize shtetl-api backend repository** (AC: 1)
  - [ ] 1.1 Create GitHub repository `shtetl-api`
  - [ ] 1.2 Initialize Go module: `go mod init github.com/jcom-dev/shtetl-api`
  - [ ] 1.3 Create service directory structure for zmanim, shul, kehilla
  - [ ] 1.4 Create minimal `cmd/main.go` for each service
  - [ ] 1.5 Create `internal/`, `api/`, `pkg/` directories
  - [ ] 1.6 Add `.gitignore` for Go
  - [ ] 1.7 Add README.md with setup instructions

- [ ] **Task 2: Initialize shtetl-web frontend repository** (AC: 2)
  - [ ] 2.1 Create GitHub repository `shtetl-web`
  - [ ] 2.2 Initialize Vite + React 19 + TypeScript project
  - [ ] 2.3 Configure strict TypeScript in `tsconfig.json`
  - [ ] 2.4 Create folder structure: `src/features/`, `src/shared/`
  - [ ] 2.5 Verify initial build succeeds: `npm run build`
  - [ ] 2.6 Add README.md with setup instructions

- [ ] **Task 3: Initialize shtetl-mobile repository** (AC: 3)
  - [ ] 3.1 Create GitHub repository `shtetl-mobile`
  - [ ] 3.2 Initialize Expo + React Native + TypeScript project
  - [ ] 3.3 Create folder structure: `src/screens/`, `src/components/`, `src/services/`
  - [ ] 3.4 Configure `app.json` with proper Expo settings
  - [ ] 3.5 Verify initial build succeeds: `npx expo start`
  - [ ] 3.6 Add README.md with setup instructions

- [ ] **Task 4: Configure submodules in main repository** (AC: 4)
  - [ ] 4.1 Add shtetl-api as submodule: `git submodule add`
  - [ ] 4.2 Add shtetl-web as submodule
  - [ ] 4.3 Add shtetl-mobile as submodule
  - [ ] 4.4 Verify `.gitmodules` is correct
  - [ ] 4.5 Test cloning with `--recursive` flag

- [ ] **Task 5: Documentation and verification** (AC: 5)
  - [ ] 5.1 Review all README.md files for completeness
  - [ ] 5.2 Verify all repos can be cloned and initialized independently
  - [ ] 5.3 Update architecture.md if any structure changes needed

## Dev Notes

### Architecture Constraints

- **Poly-repo rationale**: Keep each repo's context small for effective AI-assisted development with BMAD methodology
- **BMAD lives only in master repo** - submodule repos contain code only, no BMAD config
- Go backend uses monorepo pattern within `shtetl-api/` (multiple services, shared `pkg/`)
- Frontend repos are standalone with no shared code libraries

### Technical Specifications

- **Go version**: 1.25.4
- **React version**: 19.2
- **Vite version**: 7.2
- **Expo SDK**: 54
- **React Native**: 0.81

### Project Structure Notes

Per architecture.md Section "Poly-Repo Architecture Specification":

- Primary rationale is AI context management for BMAD workflows
- Each repo fits within AI assistant context windows
- Contract-based integration (OpenAPI specs) instead of shared libraries
- Type generation: `npm run generate-types` in frontend repos

### Directory Structures

**shtetl-api expected structure:**
```
shtetl-api/
├── services/
│   ├── zmanim/cmd/main.go
│   ├── shul/cmd/main.go
│   └── kehilla/cmd/main.go
├── pkg/
├── migrations/
├── go.mod
├── go.sum
└── README.md
```

**shtetl-web expected structure:**
```
shtetl-web/
├── src/
│   ├── features/
│   ├── shared/
│   ├── App.tsx
│   └── main.tsx
├── package.json
├── tsconfig.json
├── vite.config.ts
└── README.md
```

**shtetl-mobile expected structure:**
```
shtetl-mobile/
├── src/
│   ├── screens/
│   ├── components/
│   └── services/
├── app.json
├── package.json
├── tsconfig.json
└── README.md
```

### References

- [Source: docs/architecture.md#Poly-Repo Architecture Specification]
- [Source: docs/architecture.md#Project Structure]
- [Source: docs/epics.md#Story 1.1: Multi-Repository Structure Setup]

## Dev Agent Record

### Context Reference

- `docs/sprint-artifacts/1-1-multi-repository-structure-setup.context.xml`

### Agent Model Used

Claude Sonnet 4.5 (claude-sonnet-4-5-20250929)

### Debug Log References

### Completion Notes List

### File List

