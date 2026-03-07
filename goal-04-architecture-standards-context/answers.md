# Goal 4: Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What's the difference between `architecture.md` and an ADR? When do you write each?**

> `architecture.md` is a living document describing the current state of the system — layers, patterns, dependencies, and how things fit together. It's updated when the architecture changes. An ADR (Architecture Decision Record) documents a specific decision: the problem, the options considered, and the rationale for the choice made. You write an ADR at decision time to capture the "why", and update `architecture.md` to reflect the resulting "what". ADRs are immutable history; `architecture.md` is the current truth.

---

**Q2. If the API team decides to switch from MediatR to Minimal APIs, which files do you update and why?**

> `architecture.md` — update the section describing the API layer pattern so future AI sessions generate code using Minimal APIs instead of MediatR handlers. Also write a new ADR documenting why the switch was made. Optionally update `coding-standards.md` if there are new patterns or conventions that come with Minimal APIs. Without updating these files, the AI would keep generating MediatR-style code because it's still in the context.

---

**Q3. How does providing architecture context upfront improve AI-generated code quality?**

> Without architecture context, the AI makes assumptions — it might generate a service that calls the database directly when your pattern requires going through a repository, or use a pattern inconsistent with the rest of the codebase. With context loaded, the AI generates code that fits the existing layers, respects boundaries, and uses the team's established patterns. It reduces review friction because the output is already aligned with how the team builds things.

---

**Q4. What goes in `business-context.md` that shouldn't go in `coding-standards.md`?**

> `business-context.md` contains domain knowledge: what the system does, who uses it, key entities (band, venue, gig, booking), business rules (a gig can only be confirmed if the venue is available), and glossary terms. `coding-standards.md` contains technical conventions: naming, formatting, error handling, test patterns. Business context helps the AI understand intent and domain logic; coding standards constrain how it expresses that in code. Mixing them makes both files harder to maintain and reason about.
