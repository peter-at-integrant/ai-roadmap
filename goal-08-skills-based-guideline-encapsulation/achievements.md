# Achievements — Goal 8: Skills-Based Guideline Encapsulation

**Status:** ✅ Done
**Goal file:** [goal-08-skills-based-guideline-encapsulation.md](./README.md)
**Implementation plan:** [implementation-plan.md](./implementation-plan.md)

---

## What was built

| File | Repo | Purpose |
|---|---|---|
| [`agency-skills/`](../../agency-skills/) | `agency-workspace` | `@agency/skills` npm package — 6 skill files + CLI |
| [`agency-skills/skills/dev-coding-standards.md`](../../agency-skills/skills/dev-coding-standards.md) | `agency-workspace` | Coding standards for all 3 repos |
| [`agency-skills/skills/architecture-patterns.md`](../../agency-skills/skills/architecture-patterns.md) | `agency-workspace` | Clean Architecture, CQRS, state machine patterns |
| [`agency-skills/skills/pr-review-process.md`](../../agency-skills/skills/pr-review-process.md) | `agency-workspace` | PR reviewer checklist and approval rules |
| [`agency-skills/skills/work-item-templates.md`](../../agency-skills/skills/work-item-templates.md) | `agency-workspace` | Story, Task, Bug templates |
| [`agency-skills/skills/testing-standards.md`](../../agency-skills/skills/testing-standards.md) | `agency-workspace` | Unit, integration, and E2E test standards |
| [`agency-skills/skills/documentation-requirements.md`](../../agency-skills/skills/documentation-requirements.md) | `agency-workspace` | PR + code + API documentation requirements |
| [`agency-skills/bin/skills.js`](../../agency-skills/bin/skills.js) | `agency-workspace` | CLI entry point |

## How to use

**Read a skill in your terminal (and paste into any AI session):**
```bash
npx @agency/skills read dev-coding-standards
npx @agency/skills read architecture-patterns
npx @agency/skills read pr-review-process
npx @agency/skills read testing-standards
npx @agency/skills read work-item-templates
npx @agency/skills read documentation-requirements
```

**Claude Code auto-loads skills** from `.claude/skills/` at session start — no manual step needed.

## How to verify

Run `npx @agency/skills read dev-coding-standards` — the `.NET 9` and `Next.js 15` standards should print to stdout.

Then paste the output into a Claude Code session and ask: _"Add a PATCH endpoint to update a band's bio."_

Expected: Claude proposes an `UpdateBandBioCommand` with MediatR handler, FluentValidation, `Result<T>`, and an xUnit test — no standards violations.
