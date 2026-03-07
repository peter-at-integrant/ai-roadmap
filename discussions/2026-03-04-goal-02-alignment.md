# Discussion: Goal 2 Alignment — AGENTS.md as Tool-Agnostic Source of Truth

**Date:** 2026-03-04
**Participants:** Hussein (Engineering Manager), Peter (Tech Lead)
**Subject:** EM reviews Goal 2 implementation plan; aligns with TL on scope and maintenance strategy
**Status:** Resolved — agreed changes applied to Goal 2

---

## Context

Goal 2 defines `AGENTS.md` as the single source of truth for coding standards, architecture context, and team conventions — readable by any AI tool. The EM reviews the implementation plan to ensure it satisfies the Context Management focus area of the roadmap and is maintainable long-term.

---

## Discussion

**Hussein:** Goal 1 set up the workspace structure. Goal 2 is the content layer — the thing that actually tells the AI what to know. My first question: if a developer uses Cursor instead of Claude Code, do they get the same context?

**Peter:** Yes. The AGENTS.md is the source. Claude Code reads it via `CLAUDE.md` (a symlink). Cursor reads it via `.cursorrules` (another symlink). Same file, same content, two entry points. Any new tool can add its own symlink.

**Hussein:** What about drift? The team ships five PRs this sprint, changes the booking status state machine, adds a new entity. Two weeks later someone asks the AI about BookingRequest — it gives the old answer because AGENTS.md was never updated.

**Peter:** That's the critical risk. The mitigation is making AGENTS.md maintenance part of the Definition of Done. Every PR checklist: "Did this change require an AGENTS.md update?" If yes, it's a PR blocker. I'll add a maintenance section to the template with exactly that wording.

**Hussein:** Who owns it? The whole team owning something means no one owns it.

**Peter:** TPL is the DRI for AGENTS.md. Any PR touching AGENTS.md requires TPL approval. The maintenance section in each repo's AGENTS.md should state that explicitly.

**Hussein:** Three repos means three AGENTS.md files. That's three documents to keep in sync for shared conventions — like the PR process, or the branching strategy. Guaranteed to drift from each other over time.

**Peter:** Shared conventions live in the workspace-level `tools/templates/AGENTS.md`. Repo-specific files inherit from that template and extend it with stack-specific details. Shared things are only written once.

**Hussein:** Is the template checked into the workspace repo?

**Peter:** Yes — `tools/templates/AGENTS.md` in `agency-workspace`. When the template changes, each service repo's AGENTS.md is updated via a PR. Not automated at this stage — manual but explicit.

**Hussein:** The Measurable says "AI assistant can answer questions about the project." That's untestable. Answer which questions? With what accuracy?

**Peter:** Replace with a concrete test: prompt the AI with "How should I implement a new booking status transition?" — the answer must reference the state machine in `agency-gig-api`, `Result<T>` from FluentResults, and the MediatR command pattern. All three must appear in the response. Binary pass/fail.

**Hussein:** That's testable. I'll accept that. What's the team's rollout plan? Do all three repos get AGENTS.md simultaneously?

**Peter:** Yes, all three in one sprint. The content is largely templated — the per-repo customisation is the stack details and key flows. Week 4 is achievable.

**Hussein:** Security again — AGENTS.md will contain architecture details, repo structure, possibly mentions of secrets patterns. What's the guidance on what not to put in it?

**Peter:** Add a Security Exclusions section to every AGENTS.md: no credentials, no environment variable values, no internal URLs with hostnames, no PII. What goes in is patterns and standards — not configuration values.

**Hussein:** Signed off.

---

## Decisions Made

| # | Issue | Resolution |
|---|---|---|
| 1 | Multi-tool compatibility | `CLAUDE.md` and `.cursorrules` are symlinks to `AGENTS.md` — one file, multiple entry points |
| 2 | Drift risk from shipping PRs | AGENTS.md update is a PR checklist item and a merge blocker if required |
| 3 | Unclear ownership | TPL is DRI; AGENTS.md changes require TPL approval |
| 4 | Shared conventions in three files | Shared conventions in workspace `tools/templates/AGENTS.md`; repos extend, don't duplicate |
| 5 | Measurable was untestable | Replaced with: AI response to booking transition prompt must reference state machine, `Result<T>`, and MediatR pattern |
| 6 | No guidance on exclusions | Security Exclusions section added to every AGENTS.md template |

---

## Outcome

Goal 2 file and implementation plan updated. EM sign-off given. Implementation proceeded — AGENTS.md and symlinks committed to all three service repos in the same sprint.
