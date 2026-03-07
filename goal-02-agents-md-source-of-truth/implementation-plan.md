# Implementation Plan — Goal 2: AGENTS.md as Tool-Agnostic Source of Truth

**Goal:** Every Agency repo has an `AGENTS.md` that AI tools read automatically to produce on-standard output.
**Demo Project:** [Agency — Band & Venue Booking Platform](../agency-project-brief.md)
**Owner:** All Teams
**Target:** Week 4

---

## Approach

Define a **shared `AGENTS.md` template** and create one per Agency repo. Each file is tool-agnostic and consumed by Claude Code (as `CLAUDE.md` symlink), Cursor (as `.cursorrules`), and any other tool. The three Agency repos each have distinct stacks and conventions — `agency-gig-api` (.NET 9), `agency-gig-web` (Next.js), `agency-gig-ui` (component library) — so each AGENTS.md is repo-specific while following the same structure.

---

## Step-by-Step Implementation

### Step 1 — Define the template

Create `tools/templates/AGENTS.md` in `agency-workspace`:

```markdown
# AGENTS.md — <Repo Name>

## Project Purpose
<!-- One paragraph: what this repo does within the Agency platform -->

## Tech Stack
<!-- Runtime, frameworks, key libraries -->

## Architecture Overview
<!-- Key patterns and layers; link to docs/architecture.md -->

## Coding Standards
<!-- Inline the key rules; link to docs/coding-standards.md for full detail -->

## Key Flows
<!-- 3-5 most important flows implemented in this repo -->

## Do Not
<!-- Anti-patterns specific to this repo -->

## Links
- Architecture: [./docs/architecture.md]
- Coding Standards: [./docs/coding-standards.md]
- GitHub Issues: [https://github.com/peter-at-integrant/agency-workspace/issues]
```

### Step 2 — `agency-gig-api` AGENTS.md

```markdown
# AGENTS.md — agency-gig-api

## Project Purpose
The backend REST API for the Agency platform. Handles all business logic for
band/venue registration, gig slot management, booking lifecycle, and reviews.
Exposes a JSON API consumed by agency-gig-web.

## Tech Stack
- .NET 9 Web API
- Entity Framework Core 9 with PostgreSQL 16
- ASP.NET Core Identity (JWT authentication)
- MediatR (CQRS)
- FluentValidation
- xUnit + FluentAssertions + NSubstitute

## Architecture Overview
Clean Architecture: API → Application → Domain → Infrastructure.
See [docs/architecture.md](./docs/architecture.md).
- Commands in `Application/Features/<Feature>/Commands/`
- Queries in `Application/Features/<Feature>/Queries/`
- Entities in `Domain/Entities/`
- EF Core in `Infrastructure/Persistence/`

## Coding Standards
- Use `Result<T>` (FluentResults) — never throw for flow control
- All methods async/await — no `.Result` or `.Wait()`
- FluentValidation validator co-located with its command/query
- No raw SQL — EF Core + migrations only
- No AutoMapper — manual mapping in `Application/Features/<Feature>/Mappers/`
- Test naming: `MethodName_StateUnderTest_ExpectedBehavior`

## Key Flows
1. Band registration → BandProfile created, JWT returned
2. Venue posts GigSlot → availability checked, slot persisted
3. Band sends BookingRequest → conflict detection, Pending status
4. Venue confirms/declines → status transition, domain event raised
5. Post-gig review → only unlocked after gig date passed

## Do Not
- No business logic in controllers — controllers call MediatR only
- No direct DbContext access outside Infrastructure layer
- No synchronous DB calls

## Links
- Architecture: [./docs/architecture.md]
- Coding Standards: [./docs/coding-standards.md]
```

### Step 3 — `agency-gig-web` AGENTS.md

```markdown
# AGENTS.md — agency-gig-web

## Project Purpose
The Next.js 15 frontend for the Agency platform. Two main user interfaces:
the Band Portal (registration, browsing, booking requests) and the Venue
Dashboard (slot management, booking confirmations, reviews).

## Tech Stack
- Next.js 15 (App Router)
- TypeScript 5
- Tailwind CSS 4
- TanStack Query (server state)
- React Hook Form
- agency-gig-ui (shared component library)
- Playwright (E2E tests)

## Architecture Overview
App Router structure: `app/(band)/`, `app/(venue)/`, `app/api/` (proxies only).
API calls via TanStack Query hooks in `hooks/`. Forms via React Hook Form.
UI components from `agency-gig-ui` — do not create one-off styled components.

## Coding Standards
- Functional components only — no class components
- Custom hooks: `use<Feature>` in `hooks/`
- Props interfaces: `<ComponentName>Props`
- No `any` type — use `unknown` and narrow
- TanStack Query for all server state — no `useEffect` for data fetching
- Import from `agency-gig-ui` for all shared UI — never duplicate components

## Key Flows
1. Band registration form → POST /api/bands → redirect to dashboard
2. Browse and filter gig slots → GET /api/gig-slots?filters
3. Send booking request → POST /api/booking-requests
4. Venue reviews incoming requests → PATCH /api/booking-requests/:id/status
5. Submit post-gig review → POST /api/reviews

## Do Not
- No business logic in components — all logic in hooks or server actions
- No inline styles — Tailwind only
- No direct fetch() in components — use TanStack Query

## Links
- Architecture: [./docs/architecture.md]
- agency-gig-ui Storybook: [http://localhost:6006]
```

### Step 4 — `agency-gig-ui` AGENTS.md

```markdown
# AGENTS.md — agency-gig-ui

## Project Purpose
The shared React component library for the Agency platform. Consumed by
agency-gig-web. Contains all reusable UI components, design tokens, and
Tailwind configuration.

## Tech Stack
- TypeScript 5 + React 19
- Tailwind CSS 4 (design tokens defined here)
- Storybook 8 (component catalogue)
- Vitest + Testing Library
- tsup (library build)

## Architecture Overview
Flat component structure: `src/components/<ComponentName>/`.
Each component exports: the component, its props interface, and any types.
Entry point: `src/index.ts` — only export from here.

## Coding Standards
- Every component has a TypeScript props interface: `<ComponentName>Props`
- Every component has a Storybook story: `<ComponentName>.stories.tsx`
- Every component has a Vitest test: `<ComponentName>.test.tsx`
- No business logic — pure UI only
- No API calls — components receive data via props
- Props should use primitive types or simple interfaces — avoid coupling to API types

## Do Not
- No API calls or data fetching in this repo
- No routing or navigation logic
- No direct imports from agency-gig-web or agency-gig-api types

## Links
- Storybook: run `npm run storybook` to view all components
- Build: `npm run build` produces `dist/` for consumption
```

### Step 5 — Create tool-compatibility symlinks

In each repo:

```bash
# Claude Code reads AGENTS.md natively; also create CLAUDE.md symlink for older versions
ln -s AGENTS.md CLAUDE.md

# Cursor reads .cursorrules
ln -s AGENTS.md .cursorrules
```

### Step 6 — Validate with AI tool

In a fresh Claude Code session with no manual context, run:
> *"I need to add a `socialLinks` field to the Band profile. Where does it go, what pattern should I follow, and what tests are needed?"*

Confirm the AI cites the correct layers (Domain entity, EF migration, Application command, API endpoint) from the `agency-gig-api` AGENTS.md without additional prompting.

### Step 7 — Define maintenance process

Add to each AGENTS.md footer:
```markdown
## Maintenance
- **Updated by:** [repo tech lead]
- **Update trigger:** Any PR that changes architecture, standards, or tech stack
- **Review cadence:** End of each sprint retrospective
```

---

## Tools & Technologies

| Tool | How it reads AGENTS.md |
|---|---|
| Claude Code | Reads `CLAUDE.md` (symlinked from AGENTS.md) |
| Cursor | Reads `.cursorrules` (symlinked from AGENTS.md) |
| Any LLM | `@AGENTS.md` in prompt, or auto-read from workspace root |

---

## Proof of Knowledge

By completing this goal, the implementor demonstrates:
- Ability to distil real project knowledge (architecture, standards, flows) into concise AI-consumable docs
- Understanding of how AI tools discover and load context files
- Knowledge of each Agency repo's tech stack and architecture patterns
- Tool-agnostic documentation design

---

## Demo Scenario

> Open `agency-gig-api` in a fresh Claude Code session. No manual context.
> Ask: *"Add a PUT endpoint to update a Band's profile. Follow existing patterns."*
>
> Expected: Claude proposes a MediatR command in `Application/Features/Bands/Commands/`, a FluentValidation validator, a controller action that only calls MediatR, and an xUnit test — all without being told to.

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| AGENTS.md goes stale | PR template: "Did this change require an AGENTS.md update?" |
| Teams write vague content | Template in Step 1 with specific section headings enforces structure |
| Symlink breaks on Windows | Document Windows alternative: copy file; add CI check to diff them |
