# Implementation Plan — Goal 1: Multi-Repository Root Setup

**Goal:** Configure a root repository that lets AI tools span all Agency project repos in a single session.
**Demo Project:** [Agency — Band & Venue Booking Platform](../agency-project-brief.md)
**Owner:** TPL
**Target:** Week 2

---

## Approach

Use a **Git workspace repository** (`agency-workspace`) with submodules as the single entry point. The workspace links all three Agency repos — `agency-gig-api`, `agency-gig-web`, `agency-gig-ui` — so an AI tool opened at the root has full cross-repo context. A `CLAUDE.md` at root and tool-specific configs for Cursor provide immediate context without manual prompting.

---

## Step-by-Step Implementation

### Step 1 — Create the root workspace repo

```bash
mkdir agency-workspace && cd agency-workspace
git init
git remote add origin git@github-integrant:peter-at-integrant/agency-workspace.git
```

### Step 2 — Add all three Agency repos as submodules

```bash
git submodule add git@github-integrant:peter-at-integrant/agency-gig-api.git services/agency-gig-api
git submodule add git@github-integrant:peter-at-integrant/agency-gig-web.git services/agency-gig-web
git submodule add git@github-integrant:peter-at-integrant/agency-gig-ui.git  services/agency-gig-ui
git submodule update --init --recursive
```

### Step 3 — VS Code multi-root workspace file

Create `agency.code-workspace` at root:

```json
{
  "folders": [
    { "name": "Workspace Root",   "path": "." },
    { "name": "agency-gig-api",   "path": "services/agency-gig-api" },
    { "name": "agency-gig-web",   "path": "services/agency-gig-web" },
    { "name": "agency-gig-ui",    "path": "services/agency-gig-ui" }
  ],
  "settings": {
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  }
}
```

### Step 4 — Claude Code root context (`CLAUDE.md`)

```markdown
# Agency Workspace

This is the root workspace for the Agency — Band & Venue Booking Platform.
See [agency-project-brief.md](./ai-roadmap/agency-project-brief.md) for full context.

## Repositories

| Path | Repo | Stack |
|---|---|---|
| `services/agency-gig-api` | Backend REST API | .NET 9, EF Core, PostgreSQL |
| `services/agency-gig-web` | Next.js frontend | Next.js 15, TypeScript, Tailwind |
| `services/agency-gig-ui`  | Shared component library | TypeScript, React, Storybook |

## Cross-repo rules
- `agency-gig-ui` is consumed by `agency-gig-web` — changes to component APIs affect the web app
- `agency-gig-api` owns the database schema — never bypass it from the frontend
- Each repo has its own `AGENTS.md` with repo-specific standards
```

### Step 5 — Cursor configuration

Create `.cursor/settings.json`:

```json
{
  "include": [
    "services/agency-gig-api/**",
    "services/agency-gig-web/**",
    "services/agency-gig-ui/**"
  ],
  "contextFiles": [
    "CLAUDE.md",
    "services/agency-gig-api/AGENTS.md",
    "services/agency-gig-web/AGENTS.md",
    "services/agency-gig-ui/AGENTS.md"
  ]
}
```

### Step 6 — Claude Code MCP configuration

Create `.claude/settings.json` to register the filesystem MCP:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "./services"]
    }
  }
}
```

This gives Claude programmatic cross-repo file access beyond text context.

### Step 7 — Validate cross-repo session

Run a real Agency scenario:
> *"Add a `FeatureFlags__EnableReviews` key to all `appsettings.json` files in agency-gig-api, and add the corresponding env var to `agency-gig-web`'s `.env.example`."*

Expected: Claude reads both repos, finds the files, applies changes in a single session without context switching.

### Step 8 — Developer setup guide

Create `docs/workspace-guide.md`:

```markdown
# Agency Workspace Setup

## Clone
git clone --recurse-submodules git@github-integrant:peter-at-integrant/agency-workspace.git

## Open in VS Code
code agency.code-workspace

## Update submodules (after teammates push)
git submodule update --remote --merge

## Start services
cd services/agency-gig-api && dotnet run
cd services/agency-gig-web && npm run dev
```

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| Git submodules | Link `agency-gig-api/web/ui` into workspace root |
| VS Code multi-root workspace | Single IDE window spanning all 3 repos |
| Claude Code `CLAUDE.md` | Root-level context describing all repos |
| `.claude/settings.json` | MCP filesystem server registration |
| Cursor `.cursor/settings.json` | Cursor-specific context inclusion |

---

## Proof of Knowledge

By completing this goal, the implementor demonstrates:
- Git submodule lifecycle: init, update, recursive clone, remote update
- Multi-root workspace configuration for VS Code-compatible tools
- How Claude Code resolves context from `CLAUDE.md` and MCP configs
- Cross-repo navigation and change propagation in an AI session

---

## Demo Scenario

> Open `agency.code-workspace`. Ask Claude Code:
> *"The BookingRequest entity needs a new `Notes` field. Add it to the EF Core entity in agency-gig-api, create the migration, and add the corresponding form field to the booking request form in agency-gig-web."*
>
> Expected: Claude proposes changes in `agency-gig-api/Domain/Entities/BookingRequest.cs`, creates a migration, and updates the React form in `agency-gig-web` — all in one session.

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Submodule HEAD drift | Weekly CI job: `git submodule update --remote` + commit |
| Clone time for 3 repos | Shallow submodules `--depth 1` for read-only context use |
| AI context window with 3 repos | Tiered CLAUDE.md: root maps repos → each repo's AGENTS.md provides detail |
| `agency-gig-ui` version mismatch | Pin submodule SHA; update deliberately when component library releases |
