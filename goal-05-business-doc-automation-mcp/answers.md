# Goal 5: Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What is an MCP server? How does it differ from writing a skill file?**

> An MCP (Model Context Protocol) server exposes tools that an AI can call programmatically — it runs as a separate process and the AI invokes its functions like an API. A skill file is a static markdown prompt the AI reads and follows. The difference is capability: a skill can only instruct the AI to do things using its own built-in abilities, whereas an MCP server can execute arbitrary code, call external APIs, read databases, and return structured data back to the AI. Use a skill for repeatable prompting patterns; use an MCP server when you need real external integrations.

---

**Q2. Walk through how `generate_release_notes` works — what inputs, what logic, what output?**

> **Input:** a target version tag and optionally a previous tag to diff from.
> **Logic:** the tool calls the GitHub API to fetch all merged PRs between the two tags, groups them by label (features, fixes, chores), and passes the structured list to the AI to generate human-readable release notes.
> **Output:** a formatted markdown release notes document, ready to paste into a GitHub Release or a changelog file. The AI writes the prose; the MCP server handles the data retrieval.

---

**Q3. Why is the doc automation in CI (GitHub Actions artifact gate) rather than run manually?**

> Running it manually means it depends on someone remembering to do it, using the right version of the tool, and having the right credentials set up locally. In CI, it runs automatically on every qualifying event (e.g. a release tag), always uses the same environment, and the output is stored as a versioned artifact attached to the workflow run. It also acts as a gate — if doc generation fails, it's visible in the PR status rather than discovered later.

---

**Q4. If you wanted to add a `generate_api_changelog` tool, what would you need to write?**

> 1. A new tool function in the MCP server (e.g. `generate_api_changelog`) with its input schema (version range or date range).
> 2. Logic to fetch the relevant data — likely parsing OpenAPI spec diffs between two commits, or reading merged PRs labelled `api-change`.
> 3. Register the tool in the MCP server's tool list so the AI can discover and invoke it.
> 4. Update the MCP server's README to document the new tool.
> Optionally add a CI step to run it on API-related releases.
