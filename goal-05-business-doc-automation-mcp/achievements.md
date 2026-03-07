# Achievements — Goal 5: Business Documentation Automation via MCPs

**Status:** ✅ Done
**Goal file:** [goal-05-business-doc-automation-mcp.md](./README.md)
**Implementation plan:** [implementation-plan.md](./implementation-plan.md)

---

## What was built

| File | Repo | Purpose |
|---|---|---|
| [`mcp-doc-server/`](../../mcp-doc-server/) | `agency-workspace` | TypeScript MCP server with `generate_release_notes` tool |
| [`mcp-doc-server/src/index.ts`](../../mcp-doc-server/src/index.ts) | `agency-workspace` | MCP server entry point |
| [`tools/scripts/generate-release-notes.sh`](../../tools/scripts/generate-release-notes.sh) | `agency-workspace` | Shell wrapper to invoke the MCP tool |
| [`.github/workflows/doc-automation.yml`](../../.github/workflows/doc-automation.yml) | `agency-workspace` | GitHub Actions workflow — generates release notes on merge; blocks merge if artefact missing |

## How to use

**Run the MCP server locally:**
```bash
cd mcp-doc-server
npm install
npm run build
node dist/index.js
```

**Generate release notes via shell script:**
```bash
bash tools/scripts/generate-release-notes.sh \
  --repo agency-gig-api \
  --from v1.0.0 \
  --to HEAD
```

**Automatic:** the `doc-automation` GitHub Actions workflow runs on every merge to `main` in any service repo — no manual trigger needed.

## How to verify

Merge a PR to `main` in `agency-gig-api`. Open the Actions tab and find the `doc-automation` workflow run — a release notes artefact should be attached. If the artefact is missing, the workflow fails and blocks the next merge.
