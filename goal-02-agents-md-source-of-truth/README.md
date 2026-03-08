# Goal 2: AGENTS.md as Tool-Agnostic Source of Truth

**Phase:** Level 0 — Maximizing AI Tool Usage
**Owner:** All Teams
**Target:** Week 4

## Description

Create and maintain an `AGENTS.md` file at the root of each repository that documents team standards, architecture guidelines, and project context in a format consumable by any AI tool — without being tied to a specific platform.

## SMART Breakdown

- **Specific:** Write and commit an `AGENTS.md` to all 3 project repositories, covering coding standards, architecture overview, tech stack, and team workflows.
- **Measurable:** All 3 projects have a populated `AGENTS.md`; AI tools reference it without additional prompting in 90% of sessions.
- **Achievable:** Documentation effort only — no new tooling required. Content is drawn from existing architecture docs and team knowledge.
- **Relevant:** Reduces repeated context setup per AI session and ensures consistent output across tools and team members.
- **Time-bound:** Initial versions complete by end of Week 2; reviewed and refined by end of Week 4.

## Deliverables

- [ ] `AGENTS.md` template defined and shared with all teams
- [ ] `AGENTS.md` committed to all 3 project repositories
- [ ] Peer review of each `AGENTS.md` by at least one team member
- [ ] AI tool validation: each file loaded and used successfully in a session
- [ ] Maintenance process defined (who updates it, when, and how)

## Acceptance Criteria

- Every repository has an `AGENTS.md` that covers: project purpose, tech stack, coding standards, key architecture decisions, and links to further docs.
- Team members report that AI tools produce on-standard output without needing extra context prompts.
- File is tool-agnostic — works with Cursor, Claude Code, and any other AI tool the team uses.

---

## Learning Objectives

By the end of this goal, you should be able to:

- Explain the difference between `AGENTS.md`, `CLAUDE.md`, and `.cursorrules` — and when each is used
- Know what belongs in `AGENTS.md` and what doesn't
- Write an `AGENTS.md` for a new project without AI assistance

---

## Self-Assessment

Answer the following questions **in your own words** without AI assistance. Reviewed by EM/TL.

1. What's the difference between `AGENTS.md`, `CLAUDE.md`, and `.cursorrules`? When would you use each?
2. What should be in `AGENTS.md` and what should NOT be in it?
3. How does `AGENTS.md` help reduce token consumption across sessions?
4. A junior developer asks: "Why do we need AGENTS.md when we can just include the context in the prompt?" How do you respond?
