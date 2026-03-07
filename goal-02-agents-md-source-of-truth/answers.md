# Goal 2: Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What's the difference between `AGENTS.md`, `CLAUDE.md`, and `.cursorrules`? When would you use each?**

> `AGENTS.md` is a tool-agnostic context file consumed by any AI agent that supports the convention — it describes repo structure, coding standards, and rules that apply regardless of which AI tool is being used. `CLAUDE.md` is Claude Code-specific and is loaded automatically by Claude Code at session start; it can reference or extend `AGENTS.md`. `.cursorrules` is the Cursor-specific equivalent. In practice: write standards in `AGENTS.md` as the single source of truth, then have tool-specific files import or reference it rather than duplicating content.

---

**Q2. What should be in `AGENTS.md` and what should NOT be in it?**

> **In:** coding standards, repo structure, naming conventions, architectural patterns, cross-repo rules, how to run tests, what commands to use, what files the agent should be aware of.
>
> **Not in:** task-specific instructions, one-off session prompts, credentials or secrets, implementation details of individual features, content that changes per-request. The file should describe stable, persistent truths about the project — not what to do right now.

---

**Q3. How does `AGENTS.md` help reduce token consumption across sessions?**

> Without `AGENTS.md`, developers re-explain the project context in every session ("we use .NET 9, we follow this naming convention, don't touch the database directly from the frontend..."). This burns tokens on every request. With `AGENTS.md` loaded automatically, the AI starts every session already knowing the context — no repetition needed. Over many sessions and developers this compounds into significant savings.

---

**Q4. A junior developer asks: "Why do we need AGENTS.md when we can just include the context in the prompt?" How do you respond?**

> Three reasons: consistency, efficiency, and governance. If every developer includes context in their own prompt, you get inconsistent instructions — one person says "use async/await everywhere", another forgets to mention it. `AGENTS.md` ensures every session gets the same baseline. It also saves tokens since the file is loaded once rather than re-typed each time. And it gives the team a single place to review, update, and agree on what the AI should know — turning implicit team knowledge into an explicit, versioned artefact.
