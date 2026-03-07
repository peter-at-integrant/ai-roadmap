# Implementation Plan — Goal 4: Project Architecture and Standards Context

**Goal:** Every Agency repo has AI-accessible architecture docs and coding standards so AI tools produce first-pass-approvable code.
**Demo Project:** [Agency — Band & Venue Booking Platform](../agency-project-brief.md)
**Owner:** TPL / Dev
**Target:** Week 7

---

## Approach

Build a `/docs` folder in each Agency repo with a defined structure: architecture overview, coding standards, dependency map, and business context. Link everything from each repo's `AGENTS.md`. Measure first-pass PR approval rate from Week 4.

---

## Step-by-Step Implementation

### Step 1 — Standardise the `/docs` structure across all 3 repos

```
<repo>/
  docs/
    architecture.md       # C4 context + component diagram, patterns narrative
    coding-standards.md   # Language-specific rules and examples
    dependencies.md       # What this repo calls, what calls it
    business-context.md   # Purpose, users, critical flows
    decisions/
      adr-001-*.md        # Architecture Decision Records
```

### Step 2 — `agency-gig-api` architecture doc

`docs/architecture.md`:
```markdown
# Architecture — agency-gig-api

## Context
Backend REST API for the Agency platform. Receives HTTP requests from
agency-gig-web and returns JSON. Owns the PostgreSQL database.

## Layer Structure (Clean Architecture)
```
src/
  API/            → Controllers, middleware, DI, Program.cs
  Application/    → Use cases (CQRS via MediatR), interfaces, DTOs
  Domain/         → Entities, value objects, domain events, business rules
  Infrastructure/ → EF Core, repositories, external service clients
```

## Key Patterns
- **CQRS via MediatR:** Commands mutate state; Queries are read-only
- **Result<T>:** All application methods return Result<T> — no flow-control exceptions
- **Repository pattern:** IRepository<T> in Application; EF Core impl in Infrastructure
- **Domain Events:** Raised by entities, dispatched post-commit via IDomainEventDispatcher

## Database (PostgreSQL via EF Core)
Key tables: Bands, Venues, GigSlots, BookingRequests, Reviews
Migrations in: Infrastructure/Persistence/Migrations/
Seed data in: Infrastructure/Persistence/Seed/

## Booking Status Machine
Pending → Confirmed | Declined → Completed (after gig date) | Cancelled
```

### Step 3 — `agency-gig-api` coding standards

`docs/coding-standards.md`:
```markdown
# Coding Standards — agency-gig-api (.NET 9)

## Naming
- Interfaces: prefix `I` (e.g. `IBandRepository`)
- Async methods: suffix `Async`
- Private fields: `_camelCase`
- Commands: `<Verb><Entity>Command` (e.g. `CreateBandCommand`)
- Queries: `Get<Entity>Query` (e.g. `GetBandByIdQuery`)

## Patterns
- Controllers call MediatR only — no business logic
- FluentValidation validator in same folder as its command/query
- Manual mapping in `Application/Features/<Feature>/Mappers/` — no AutoMapper
- Use `Result<T>` from FluentResults — never throw exceptions for flow control
- Repositories live in Infrastructure; interfaces in Application

## Testing
- xUnit + FluentAssertions + NSubstitute
- Integration tests use Testcontainers (real PostgreSQL)
- Test class mirrors source: BandServiceTests → BandService
- Naming: `MethodName_StateUnderTest_ExpectedBehavior`

## Do Not
- No `.Result` or `.Wait()` on async calls
- No `[FromBody]` on GET endpoints
- No raw SQL — EF Core only
- No DbContext access outside Infrastructure layer
```

### Step 4 — `agency-gig-web` architecture doc

`docs/architecture.md`:
```markdown
# Architecture — agency-gig-web

## Context
Next.js 15 (App Router) frontend. Serves the Band Portal and Venue Dashboard.
Calls agency-gig-api for all data. Imports UI components from agency-gig-ui.

## Route Structure
app/
  (band)/           → Band Portal (register, browse, book)
  (venue)/          → Venue Dashboard (slots, requests, reviews)
  api/              → Route handlers (thin proxies to agency-gig-api)

## Data Flow
Component → TanStack Query hook → Next.js route handler → agency-gig-api

## Key Patterns
- Server Components for initial data fetch (SSR/SSG)
- Client Components only where interactivity required (forms, modals)
- TanStack Query for client-side server state
- React Hook Form for all forms
- All shared UI from agency-gig-ui — no one-off styled components
```

### Step 5 — `agency-gig-ui` architecture doc

`docs/architecture.md`:
```markdown
# Architecture — agency-gig-ui

## Context
Shared React component library. Published as an npm package consumed by
agency-gig-web. Contains all reusable UI: buttons, inputs, cards, modals,
the booking status badge, etc.

## Structure
src/
  components/
    Button/
      Button.tsx
      Button.stories.tsx
      Button.test.tsx
      index.ts
  index.ts          ← only export from here

## Release Pattern
Semantic versioning. agency-gig-web pins to a specific version.
Breaking changes (new required props, removed exports) = major version bump.
```

### Step 6 — Dependency maps

`agency-gig-api/docs/dependencies.md`:
```markdown
# Dependencies — agency-gig-api

## Called by
| Consumer | Protocol |
|---|---|
| agency-gig-web | REST (JSON) |

## Calls out to
| Service | Protocol | Purpose |
|---|---|---|
| PostgreSQL | TCP (EF Core) | Primary data store |
| (future) Email service | SMTP/HTTP | Booking notifications |

## Infrastructure
- PostgreSQL 16 (Docker in dev, Azure Database for PostgreSQL in prod)
- (future) Azure Service Bus for async events
```

### Step 7 — Architecture Decision Records (ADRs)

Seed at least 3 ADRs in `agency-gig-api`:

`docs/decisions/adr-001-clean-architecture.md`:
```markdown
# ADR-001: Clean Architecture with CQRS

**Date:** 2026-03-05 | **Status:** Accepted

## Context
Agency API needs clear separation between business rules and infrastructure.

## Decision
Clean Architecture (Domain / Application / Infrastructure / API) + CQRS via MediatR.

## Consequences
+ Domain logic testable without DB or HTTP
+ Commands and queries independently evolvable
- More files/folders than a simple CRUD approach
```

`docs/decisions/adr-002-result-pattern.md`:
```markdown
# ADR-002: Result<T> over Exceptions

**Date:** 2026-03-05 | **Status:** Accepted

## Context
Using exceptions for domain validation (e.g. "slot already booked") pollutes
stack traces and obscures expected failure paths.

## Decision
Use FluentResults Result<T> for all application-layer returns.
Map failure reasons to HTTP status codes in a global result filter.

## Consequences
+ Explicit failure paths in method signatures
+ No exception overhead for expected cases
- Requires discipline — easy to accidentally throw instead of return Result.Fail
```

### Step 8 — Link from AGENTS.md

Add to each repo's `AGENTS.md`:
```markdown
## Documentation
- [Architecture](./docs/architecture.md)
- [Coding Standards](./docs/coding-standards.md)
- [Dependencies](./docs/dependencies.md)
- [Business Context](./docs/business-context.md)
- [ADRs](./docs/decisions/)
```

### Step 9 — Track first-pass PR approval rate

From Week 4, tag AI-assisted PRs with `ai-assisted` label in GitHub.
At end of each sprint: total `ai-assisted` PRs vs PRs approved without code-change requests.
Target: 70% first-pass approval by Week 7.

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| Markdown in Git | Docs-as-code — PR reviewable, AI-readable |
| Mermaid diagrams | C4 architecture diagrams embedded in Markdown |
| GitHub PR labels | Track `ai-assisted` PRs for metric |
| GitHub Insights | First-pass approval rate dashboard |

---

## Proof of Knowledge

By completing this goal, the implementor demonstrates:
- C4 architecture documentation for a real multi-repo project
- .NET 9 Clean Architecture + CQRS patterns (agency-gig-api)
- Next.js 15 App Router architecture (agency-gig-web)
- Architecture Decision Record (ADR) writing: when to write one, what to include
- Measurement design: first-pass PR approval rate as a quality signal

---

## Demo Scenario

> Fresh Claude Code session in `agency-gig-api`. Ask:
> *"A band should only be able to have one Pending request per GigSlot. Add this validation."*
>
> Expected: Claude proposes a FluentValidation rule in the `CreateBookingRequestCommand` validator, checking for existing Pending requests via the repository interface — correct layer, correct pattern, no coaching needed.

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Docs diverge from code | PR template: "Did this change require a docs/decisions update?" |
| ADRs not written | ADR is part of DoD for architecture-level decisions |
| 70% target not met by Week 7 | Analyse rejection patterns; update coding-standards.md to address repeated AI mistakes |
