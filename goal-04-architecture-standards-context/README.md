# Goal 4: Project Architecture and Standards Context

**Phase:** Level 0 — Maximizing AI Tool Usage
**Owner:** TPL / Dev
**Target:** Week 7

## Description

Document and maintain project architecture, coding standards, tech stack, and business context in a centralized, AI-accessible knowledge base. This context is linked from `AGENTS.md` and kept up to date so AI tools consistently produce on-standard, high-quality code.

## SMART Breakdown

- **Specific:** Create a structured knowledge base (wiki, markdown files, or linked docs) covering architecture diagrams, coding standards per language, project interdependencies, and business context for each project.
- **Measurable:** AI-generated code passes peer review on first submission for 70% of tasks, tracked over a 4-week sprint cycle starting Week 4.
- **Achievable:** Content already exists across scattered docs and tribal knowledge — this goal curates and formats it for AI consumption.
- **Relevant:** Reduces review cycles, rework, and token waste from repeated context re-injection in every AI session.
- **Time-bound:** Baseline documentation complete by end of Week 3; first review cycle measured by Week 7.

## Deliverables

- [ ] Architecture overview document per project (diagrams + narrative)
- [ ] Coding standards documented per language/stack (.NET, React/TypeScript, Python)
- [ ] Project interdependency map (who calls whom, shared infrastructure)
- [ ] Business context summary per project (purpose, users, critical flows)
- [ ] All docs linked from relevant `AGENTS.md` files
- [ ] Metric baseline established: first-pass PR approval rate tracked from Week 4

## Acceptance Criteria

- Every project has an architecture doc and coding standards doc accessible via `AGENTS.md`.
- Developers report reduced need to manually explain context to AI tools.
- First-pass PR approval rate for AI-assisted tasks reaches 70% by end of Week 7.
