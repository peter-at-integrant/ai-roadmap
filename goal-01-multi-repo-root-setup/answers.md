# Goal 1: Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What problem does a multi-repo root setup solve that simply opening each repo separately doesn't?**

> When you open repos separately, each AI session only has context for one repo at a time. Cross-cutting changes — like updating a shared component API and all the places that consume it — require constant context switching and manual coordination. A multi-root setup lets the AI see all repos simultaneously, so it can read, reason about, and propose changes across all of them in a single session.

---

**Q2. What is a [Git submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules)? How does it differ from copying the code into a subfolder?**

> A Git submodule is a pointer from one repo to a specific commit in another repo. The child repo keeps its own independent Git history, branches, and remotes. When you copy code into a subfolder, it becomes part of the parent repo's history — changes are tracked there, not in the original repo, and the two copies diverge over time. With a submodule, you always know exactly which version of the child repo you're referencing, and changes are committed and pushed to the child repo independently.

---

**Q3. If a new AI tool joins the team that isn't [Cursor](https://www.cursor.com) or [Claude Code](https://docs.anthropic.com/en/docs/claude-code), what would you need to add to support it?**

> You'd need to add that tool's context configuration file to the root repo — for example, a `.windsurfrules` file for Windsurf or equivalent for any other tool. The workspace structure itself (submodules + `.code-workspace`) doesn't change; only the tool-specific context file is new. The `AGENTS.md` files in each repo are already tool-agnostic and would be consumed automatically if the new tool supports them.

---

**Q4. What would break if you deleted the [`.code-workspace`](https://code.visualstudio.com/docs/editing/workspaces/workspaces#_multiroot-workspaces) file? What if you deleted `CLAUDE.md`?**

> Deleting `.code-workspace` means VS Code (and tools that rely on it) can no longer open all repos as a single multi-root workspace. You'd have to open each repo folder individually, losing the unified view. The submodules still exist — the workspace file is just the instruction for which folders to surface together.
>
> Deleting `CLAUDE.md` means Claude Code loses its project-level context — coding standards, repo layout, cross-repo rules. It would still work, but without that context it would fall back to generic behaviour and might produce code that violates team conventions. Other tools (Cursor, etc.) would be unaffected.
