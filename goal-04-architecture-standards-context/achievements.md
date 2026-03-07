# Achievements — Goal 4: Project Architecture and Standards Context

**Status:** ✅ Done
**Goal file:** [goal-04-architecture-standards-context.md](./README.md)
**Implementation plan:** [implementation-plan.md](./implementation-plan.md)

---

## What was built

Each service repo received a full `docs/` folder:

| File | agency-gig-api | agency-gig-web | agency-gig-ui |
|---|---|---|---|
| `docs/architecture.md` | [link](https://github.com/peter-at-integrant/agency-gig-api/blob/main/docs/architecture.md) | [link](https://github.com/peter-at-integrant/agency-gig-web/blob/main/docs/architecture.md) | [link](https://github.com/peter-at-integrant/agency-gig-ui/blob/main/docs/architecture.md) |
| `docs/coding-standards.md` | [link](https://github.com/peter-at-integrant/agency-gig-api/blob/main/docs/coding-standards.md) | [link](https://github.com/peter-at-integrant/agency-gig-web/blob/main/docs/coding-standards.md) | [link](https://github.com/peter-at-integrant/agency-gig-ui/blob/main/docs/coding-standards.md) |
| `docs/dependencies.md` | [link](https://github.com/peter-at-integrant/agency-gig-api/blob/main/docs/dependencies.md) | [link](https://github.com/peter-at-integrant/agency-gig-web/blob/main/docs/dependencies.md) | [link](https://github.com/peter-at-integrant/agency-gig-ui/blob/main/docs/dependencies.md) |
| `docs/business-context.md` | [link](https://github.com/peter-at-integrant/agency-gig-api/blob/main/docs/business-context.md) | [link](https://github.com/peter-at-integrant/agency-gig-web/blob/main/docs/business-context.md) | [link](https://github.com/peter-at-integrant/agency-gig-ui/blob/main/docs/business-context.md) |

`agency-gig-api` additionally received 3 Architecture Decision Records:

| ADR | Link |
|---|---|
| `docs/decisions/adr-001-clean-architecture.md` | [link](https://github.com/peter-at-integrant/agency-gig-api/blob/main/docs/decisions/adr-001-clean-architecture.md) |
| `docs/decisions/adr-002-result-pattern.md` | [link](https://github.com/peter-at-integrant/agency-gig-api/blob/main/docs/decisions/adr-002-result-pattern.md) |
| `docs/decisions/adr-003-no-automapper.md` | [link](https://github.com/peter-at-integrant/agency-gig-api/blob/main/docs/decisions/adr-003-no-automapper.md) |

## How to use

Claude Code reads these automatically via the Documentation links in each repo's `AGENTS.md`. They also serve as human reference:
```bash
cat services/agency-gig-api/docs/architecture.md
cat services/agency-gig-api/docs/coding-standards.md
```

## How to verify

In a Claude Code session inside `agency-gig-api`, ask: _"Where should I add a new repository interface?"_

Expected: Claude answers "Application layer — `Application/Interfaces/`" and references the Clean Architecture layering from `docs/architecture.md`.
