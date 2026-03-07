# Achievements — Goal 2: AGENTS.md as Tool-Agnostic Source of Truth

**Status:** ✅ Done
**Goal file:** [goal-02-agents-md-source-of-truth.md](./README.md)
**Implementation plan:** [implementation-plan.md](./implementation-plan.md)

---

## What was built

| File | Repo |
|---|---|
| `AGENTS.md` | [agency-gig-api](https://github.com/peter-at-integrant/agency-gig-api/blob/main/AGENTS.md) |
| `CLAUDE.md` (symlink → `AGENTS.md`) | [agency-gig-api](https://github.com/peter-at-integrant/agency-gig-api/blob/main/CLAUDE.md) |
| `.cursorrules` (symlink → `AGENTS.md`) | [agency-gig-api](https://github.com/peter-at-integrant/agency-gig-api/blob/main/.cursorrules) |
| `AGENTS.md` | [agency-gig-web](https://github.com/peter-at-integrant/agency-gig-web/blob/main/AGENTS.md) |
| `CLAUDE.md` (symlink → `AGENTS.md`) | [agency-gig-web](https://github.com/peter-at-integrant/agency-gig-web/blob/main/CLAUDE.md) |
| `.cursorrules` (symlink → `AGENTS.md`) | [agency-gig-web](https://github.com/peter-at-integrant/agency-gig-web/blob/main/.cursorrules) |
| `AGENTS.md` | [agency-gig-ui](https://github.com/peter-at-integrant/agency-gig-ui/blob/main/AGENTS.md) |
| `CLAUDE.md` (symlink → `AGENTS.md`) | [agency-gig-ui](https://github.com/peter-at-integrant/agency-gig-ui/blob/main/CLAUDE.md) |
| `.cursorrules` (symlink → `AGENTS.md`) | [agency-gig-ui](https://github.com/peter-at-integrant/agency-gig-ui/blob/main/.cursorrules) |
| [`tools/templates/AGENTS.md`](../../tools/templates/AGENTS.md) | `agency-workspace` — blank template for new repos |

## How to use

Start any Claude Code session inside a service repo — `CLAUDE.md` is loaded automatically. For Cursor, `.cursorrules` is picked up on project open.

To read the standards manually:
```bash
cat services/agency-gig-api/AGENTS.md
```

## How to verify

In a Claude Code session inside `agency-gig-api`, ask: _"How should I implement a new booking status transition?"_

Expected: the answer references the `BookingRequest` state machine, `Result<T>` from FluentResults, and the MediatR command pattern — all sourced from `AGENTS.md`.
