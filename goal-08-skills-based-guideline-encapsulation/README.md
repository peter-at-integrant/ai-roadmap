# Goal 8: Skills-Based Guideline Encapsulation

**Phase:** Level 0 — Maximizing AI Tool Usage
**Owner:** TPL
**Target:** Week 6

## Description

Package all team guidelines, coding standards, PR review checklists, and architecture patterns as reusable OpenSkills that any AI tool can install and reference on demand — making team practices portable, consistent, and instantly accessible across tools and team members.

## SMART Breakdown

- **Specific:** Create and publish at least 6 OpenSkills covering: development coding standards, PR review process, architecture patterns, work item templates, testing standards, and documentation requirements.
- **Measurable:** All 6 skills published to the team's OpenSkills registry; every team member can load any skill with a single `npx openskills read <skill-name>` command.
- **Achievable:** Content already exists in `AGENTS.md` and team docs; OpenSkills tooling is available via `npx`; requires formatting and publishing effort only.
- **Relevant:** Makes team guidelines portable across AI tools and simplifies onboarding — new team members and new AI tools get the same standards instantly.
- **Time-bound:** First 3 skills published by end of Week 4; all 6 published by end of Week 6.

## Deliverables

- [ ] OpenSkills registry set up for the team
- [ ] Skill: Development Coding Standards
- [ ] Skill: PR Review Process
- [ ] Skill: Architecture Patterns
- [ ] Skill: Work Item Templates
- [ ] Skill: Testing Standards
- [ ] Skill: Documentation Requirements
- [ ] All skills tested with at least 2 different AI tools (e.g., Cursor, Claude Code)
- [ ] Team walkthrough: everyone able to install and invoke a skill
- [ ] Skills linked from `AGENTS.md` in each repository

## Acceptance Criteria

- All 6 skills are published and loadable via `npx openskills read <skill-name>`.
- AI tools apply the correct standards when a skill is loaded — verified by TPL review of AI output.
- Every team member has successfully loaded and used at least one skill in a real development session.
