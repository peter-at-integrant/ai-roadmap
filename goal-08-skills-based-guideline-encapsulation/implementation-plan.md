# Implementation Plan — Goal 8: Skills-Based Guideline Encapsulation

**Goal:** Package all Agency coding standards and team guidelines as installable skills — compatible with Claude Code and Cursor, portable across team members.
**Demo Project:** [Agency — Band & Venue Booking Platform](../agency-project-brief.md)
**Owner:** TPL
**Target:** Week 6

---

## Approach

Create an `agency-skills` npm package containing 6 skill directories — one per guideline area. Published to GitHub Packages. Installed via `npx @agency/skills install` which copies skill directories into `.claude/skills/`. Both Claude Code and Cursor auto-load skills from `.claude/skills/` — one install, both tools. Cursor also reads `.cursorrules` (symlinked from `AGENTS.md`, established in Goal 2) for project-level rules. Content is drawn directly from the Agency project's architecture, stack, and conventions.

> **Skill format:** Each skill must be a directory containing a `SKILL.md` file with YAML frontmatter (`name` + `description`). A flat `.md` file will not be registered as a slash command by either tool.

---

## Step-by-Step Implementation

### Step 1 — Set up the skills package

In `agency-workspace`:

```bash
mkdir agency-skills && cd agency-skills
npm init -y
# Set name to @agency/skills in package.json
```

Structure:
```
agency-workspace/
  agency-skills/
    skills/
      dev-coding-standards/
        SKILL.md          ← name + description frontmatter required
      pr-review-process/
        SKILL.md
      architecture-patterns/
        SKILL.md
      work-item-templates/
        SKILL.md
      testing-standards/
        SKILL.md
      documentation-requirements/
        SKILL.md
    bin/
      skills.js           ← CLI entry point
    package.json
```

### Step 2 — Skill: Development Coding Standards

Create `skills/dev-coding-standards/SKILL.md`:

```markdown
---
name: dev-coding-standards
description: This skill should be used when writing or reviewing code for the Agency platform to ensure it follows team coding standards across agency-gig-api, agency-gig-web, and agency-gig-ui.
---

# Skill: Agency Development Coding Standards

## agency-gig-api (.NET 9)
- CQRS via MediatR: commands in `Application/Features/<Feature>/Commands/`, queries in `Queries/`
- `Result<T>` from FluentResults — never throw exceptions for flow control
- FluentValidation: validator co-located with its command/query file
- No AutoMapper — manual mapping in `Application/Features/<Feature>/Mappers/`
- Async throughout: all methods `async Task<>`, no `.Result` or `.Wait()`
- No raw SQL — EF Core only; migrations in `Infrastructure/Persistence/Migrations/`
- No DbContext outside Infrastructure layer
- Naming: `PascalCase` classes, `_camelCase` private fields, `Async` suffix on async methods

## agency-gig-web (Next.js 15 / TypeScript)
- App Router: `app/(band)/` for Band Portal, `app/(venue)/` for Venue Dashboard
- Functional components only — no class components
- Custom hooks: `use<Feature>` in `hooks/` — all server state via TanStack Query
- No `any` type — use `unknown` and narrow
- React Hook Form for all forms — no uncontrolled inputs
- Import UI from `agency-gig-ui` — never duplicate components
- No direct `fetch()` in components — use TanStack Query hooks

## agency-gig-ui (Component Library)
- Every component: `<ComponentName>Props` interface + Storybook story + Vitest test
- No business logic — pure UI only
- Export only from `src/index.ts`
- `data-testid` attribute required on all interactive elements
```

### Step 3 — Skill: PR Review Process

Create `skills/pr-review-process/SKILL.md`:

```markdown
---
name: pr-review-process
description: This skill should be used when reviewing a pull request for the Agency platform to ensure it meets team quality, architecture, and documentation standards.
---

# Skill: Agency PR Review Process

## Reviewer Checklist

### Code Quality
- [ ] Follows Agency coding standards (run `@agency/skills read dev-coding-standards`)
- [ ] No magic strings — constants or enums used
- [ ] No commented-out code
- [ ] Files under 300 lines; if over, flag for discussion

### Architecture (agency-gig-api)
- [ ] No business logic in controllers — MediatR only
- [ ] Correct layer (Domain / Application / Infrastructure / API)
- [ ] No new direct DbContext access outside Infrastructure

### Architecture (agency-gig-web)
- [ ] Server Components used where possible
- [ ] Client Components only where interactivity required
- [ ] No fetch() in components — TanStack Query hooks

### Testing
- [ ] Unit tests added for new logic
- [ ] Integration tests pass
- [ ] If UI change: Playwright test added or existing tests still pass

### Security
- [ ] No hardcoded secrets or credentials
- [ ] Auth checks present on protected endpoints
- [ ] User input validated (FluentValidation in API, React Hook Form in web)

### Documentation
- [ ] PR description complete (Summary, Changes, Test Plan, Linked Items)
- [ ] AGENTS.md updated if standards or architecture changed
- [ ] GitHub issue linked (#<id>)

## Approval Rules
- 2 approvals required before merge to main
- At least 1 from Peter (TL) or designated senior developer
- All CI checks must pass
- All review threads resolved
```

### Step 4 — Skill: Architecture Patterns

Create `skills/architecture-patterns/SKILL.md`:

```markdown
---
name: architecture-patterns
description: This skill should be used when designing or reviewing Agency platform architecture to ensure it follows Clean Architecture, CQRS, and App Router patterns.
---

# Skill: Agency Architecture Patterns

## agency-gig-api — Clean Architecture
```
API → Application → Domain → Infrastructure
```
- Domain: entities, value objects, domain events. Zero external dependencies.
- Application: use cases (CQRS), interfaces, DTOs. Depends on Domain only.
- Infrastructure: EF Core, repositories, external clients. Implements Application interfaces.
- API: controllers, middleware, DI, Program.cs.

## CQRS with MediatR
- Commands: `Create<Entity>Command`, `Update<Entity>Command`, `Delete<Entity>Command`
  → return `Result<EntityId>` or `Result`
- Queries: `Get<Entity>ByIdQuery`, `List<Entity>sQuery`
  → return `Result<EntityDto>` or `Result<List<EntityDto>>`
- Handlers: one handler per command/query, co-located in same folder

## Booking Status State Machine
Pending → Confirmed | Declined → Completed (after gig date) | Cancelled
Transitions enforced in Domain entity: `BookingRequest.Confirm()`, `.Decline()`, `.Cancel()`
Never update Status directly — always through domain methods.

## Repository Pattern
- Interface in Application: `IBandRepository`, `IBookingRequestRepository`, etc.
- Implementation in Infrastructure: `BandRepository : IBandRepository`
- Return domain entities from repositories — map to DTOs in Application layer

## Error Handling
- `Result<T>` for all expected failures: not found, validation, conflict
- `Result.Fail("Slot already booked")` → maps to 409 in global result filter
- Unhandled exceptions → 500 (global exception middleware)

## agency-gig-web — Next.js App Router
- Server Components: initial data fetch, no interactivity
- Client Components: forms, modals, real-time updates — mark `"use client"`
- Data: TanStack Query hooks → Next.js route handlers → agency-gig-api
- Routing: `(band)/` group = Band Portal, `(venue)/` group = Venue Dashboard
```

### Step 5 — Skill: Work Item Templates

Create `skills/work-item-templates/SKILL.md`:

```markdown
---
name: work-item-templates
description: This skill should be used when creating user stories, tasks, or bug reports for the Agency platform to ensure they follow the team work item template.
---

# Skill: Agency Work Item Templates

## User Story
**Title:** As a [band / venue manager], I want [capability] so that [benefit]
**Acceptance Criteria:**
- Given [context], When [action], Then [outcome]
**Tags:** [feature-area] [repo: agency-gig-api | agency-gig-web | agency-gig-ui]
**Story Points:** 1 | 2 | 3 | 5 | 8 | 13

## Task
**Title:** [Verb + object] e.g. "Implement BookingRequest.Confirm() domain method"
**Repo:** agency-gig-api | agency-gig-web | agency-gig-ui
**Estimated Hours:** [hours]

## Bug
**Title:** Bug: [short description]
**Repo:** [affected repo]
**Steps to Reproduce:** [numbered]
**Expected / Actual:** [contrast]
**Severity:** Critical | High | Medium | Low

## Feature Area Tags
- `bands` — band registration, profiles, availability
- `venues` — venue registration, profiles, gig slots
- `bookings` — booking requests, status transitions, conflict detection
- `reviews` — post-gig reviews, ratings
- `auth` — registration, login, JWT
- `infra` — CI/CD, tooling, dependencies
```

### Step 6 — Skill: Testing Standards

Create `skills/testing-standards/SKILL.md`:

```markdown
---
name: testing-standards
description: This skill should be used when writing or reviewing tests for the Agency platform to ensure they follow team unit, integration, and E2E testing standards.
---

# Skill: Agency Testing Standards

## Test Naming
`MethodName_StateUnderTest_ExpectedBehavior`
e.g. `Confirm_WhenAlreadyConfirmed_ReturnsFailure`
    `BookingRequestHandler_WhenSlotUnavailable_ReturnsConflictError`

## Unit Tests (agency-gig-api)
- xUnit + FluentAssertions + NSubstitute
- AAA: Arrange / Act / Assert — one logical assertion per test
- Mock external dependencies: `IBookingRequestRepository`, `IGigSlotRepository`
- Test all booking status transitions and conflict detection logic

## Integration Tests (agency-gig-api)
- `WebApplicationFactory<Program>` + Testcontainers (real PostgreSQL)
- Reset DB between test classes using `Respawn`
- Test all API endpoints: happy path + key failure cases

## E2E Tests (agency-gig-web — Playwright)
- Target acceptance criteria, not implementation details
- Use `data-testid` selectors — never CSS classes or visible text
- `waitForSelector` / `expect().toBeVisible()` — no `page.waitForTimeout`
- One describe block per Agency feature; one test per acceptance criterion
- Run against staging: `BASE_URL=${{ secrets.STAGING_URL }} npx playwright test`

## Coverage Targets
| Layer | Target |
|---|---|
| Domain + Application (unit) | 80% line coverage |
| API endpoints (integration) | 100% of public endpoints |
| UI flows (E2E) | 80% of acceptance criteria per sprint |
```

### Step 7 — Skill: Documentation Requirements

Create `skills/documentation-requirements/SKILL.md`:

```markdown
---
name: documentation-requirements
description: This skill should be used when writing PRs, ADRs, or updating AGENTS.md for the Agency platform to ensure documentation requirements are met.
---

# Skill: Agency Documentation Requirements

## Every PR must include
- [ ] PR description: Summary, Changes, Test Plan, Linked Items (#<id>)
- [ ] Release notes artifact (auto-generated by pipeline — Goal 5)

## Code documentation
- Public API controllers and service interfaces: XML doc comments
- Complex domain logic: inline comment explaining *why*, not *what*
- No commented-out code in PRs

## Architecture changes → update docs
- `docs/architecture.md` if layer or component structure changes
- `docs/decisions/adr-NNN-*.md` for significant decisions
- `AGENTS.md` if standards, patterns, or tech stack changes

## API changes
- EF Core migration required for all schema changes
- OpenAPI spec auto-generated — review `swagger.json` diff in PR
- Notify consuming team (agency-gig-web) of any breaking changes

## Component library changes (agency-gig-ui)
- Update Storybook story for modified components
- Bump version (semver) before publishing
- Notify agency-gig-web team of breaking prop changes
```

### Step 8 — CLI entry point

Create `agency-skills/bin/skills.js`:

```javascript
#!/usr/bin/env node
const fs   = require("fs");
const path = require("path");

const [,, command, skillName] = process.argv;
const skillsDir = path.join(__dirname, "../skills");

if (command === "install") {
  // Copy all skill directories into .claude/skills/
  const dest = path.join(process.cwd(), ".claude/skills");
  fs.mkdirSync(dest, { recursive: true });
  for (const name of fs.readdirSync(skillsDir)) {
    const src = path.join(skillsDir, name, "SKILL.md");
    if (!fs.existsSync(src)) continue;
    fs.mkdirSync(path.join(dest, name), { recursive: true });
    fs.copyFileSync(src, path.join(dest, name, "SKILL.md"));
    console.log(`✓ Installed skill: ${name}`);
  }
  console.log("\nSkills installed to .claude/skills/ — recognized by Claude Code and Cursor.");
  process.exit(0);
}

if (command !== "read" || !skillName) {
  console.log("Usage:");
  console.log("  npx @agency/skills install          — install all skills to .claude/skills/");
  console.log("  npx @agency/skills read <skill-name> — print skill content");
  console.log("Skills:", fs.readdirSync(skillsDir).join(", "));
  process.exit(1);
}

const filePath = path.join(skillsDir, skillName, "SKILL.md");
if (!fs.existsSync(filePath)) {
  console.error(`Skill not found: ${skillName}`);
  process.exit(1);
}

console.log(fs.readFileSync(filePath, "utf-8"));
```

Add to `package.json`:
```json
{
  "name": "@agency/skills",
  "version": "1.0.0",
  "bin": { "skills": "./bin/skills.js" }
}
```

### Step 9 — Link from each repo's `AGENTS.md`

Add to `agency-gig-api`, `agency-gig-web`, `agency-gig-ui` AGENTS.md:

```markdown
## Skills
Agency skills are installed in `.claude/skills/` and auto-loaded by Claude Code and Cursor.
To reinstall or update: `npx @agency/skills install`

Available skills: `/dev-coding-standards`, `/pr-review-process`, `/architecture-patterns`,
`/work-item-templates`, `/testing-standards`, `/documentation-requirements`
```

### Step 10 — Team walkthrough

30-minute session:
1. Install: `npm install -g @agency/skills`
2. Run: `npx @agency/skills read dev-coding-standards` — show output in terminal
3. Paste into Claude Code session — show AI applying the standard
4. Show how `.claude/skills/` folder auto-loads into every session

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| Skill directories (`name/SKILL.md`) | Required format — directory + YAML frontmatter |
| npm (`@agency/skills`) | Versioned, installable skill registry |
| `.claude/skills/` | Auto-loaded by both Claude Code and Cursor each session |
| `.cursorrules` | Cursor project rules — symlinked from `AGENTS.md` (Goal 2) |
| GitHub Packages | Private registry hosting |

---

## Proof of Knowledge

By completing this goal, the implementor demonstrates:
- Ability to distil Agency coding standards into concise, machine-consumable prompts
- npm package authoring and CLI tooling (`bin` entry point)
- Understanding of how AI tools auto-load skills at session start
- Cross-tool compatibility: Claude Code + Cursor + any LLM

---

## Demo Scenario

> New developer joins the team. First AI session:
> `npx @agency/skills read dev-coding-standards`
> Pastes into Claude Code.
> Asks: *"Add a PATCH endpoint to update a band's bio."*
>
> Expected: Claude proposes an `UpdateBandBioCommand` with MediatR handler, FluentValidation, `Result<T>`, and an xUnit test — no review feedback on standards violations.

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Skills go stale | DoD checklist: "Did this change require a skills update?" |
| Private registry access issues | Document `.npmrc` setup in onboarding guide |
| AI ignores long skill files | Keep each skill under 150 lines — concise beats comprehensive |
| Conflicting standards | Skills package is the single source of truth; changes via PR to `agency-workspace` |
