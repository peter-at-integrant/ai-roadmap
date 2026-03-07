# Implementation Plan — Goal 5: Business Documentation Automation via MCPs

**Goal:** Auto-generate release notes and changelogs on every Agency PR merge — enforced via pipeline gate.
**Demo Project:** [Agency — Band & Venue Booking Platform](../agency-project-brief.md)
**Owner:** DevOps / Dev
**Target:** Week 6

---

## Approach

Build a **custom MCP server** (`mcp-doc-server`) that exposes a `generate_release_notes` tool. The GitHub Actions workflow for each Agency repo calls it at merge time, passing the PR diff and linked issues. The MCP calls Claude, receives structured release notes, and writes the artifact to the workflow. A gate blocks the merge if the artifact is missing.

---

## Step-by-Step Implementation

### Step 1 — Scaffold the MCP server

In `agency-workspace`:

```bash
mkdir mcp-doc-server && cd mcp-doc-server
npm init -y
npm install @anthropic-ai/sdk @modelcontextprotocol/sdk zod
```

### Step 2 — Implement `generate_release_notes` tool

Create `mcp-doc-server/src/index.ts`:

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import Anthropic from "@anthropic-ai/sdk";
import { z } from "zod";

const server = new McpServer({ name: "agency-doc-automation", version: "1.0.0" });
const client = new Anthropic();

server.tool(
  "generate_release_notes",
  "Generate release notes from a merged Agency PR",
  {
    repo: z.enum(["agency-gig-api", "agency-gig-web", "agency-gig-ui"])
          .describe("Which Agency repo this PR belongs to"),
    prTitle: z.string().describe("Pull request title"),
    prDescription: z.string().describe("Pull request description"),
    linkedWorkItems: z.array(z.object({
      id: z.number(),
      type: z.string(),
      title: z.string(),
    })).describe("GitHub Issues linked to the PR"),
    diffSummary: z.string().describe("git diff --stat output"),
  },
  async ({ repo, prTitle, prDescription, linkedWorkItems, diffSummary }) => {
    const repoContext: Record<string, string> = {
      "agency-gig-api": "backend .NET 9 REST API (bands, venues, bookings, reviews)",
      "agency-gig-web": "Next.js 15 frontend (Band Portal and Venue Dashboard)",
      "agency-gig-ui":  "shared React component library (design system)",
    };

    const workItemsList = linkedWorkItems
      .map(wi => `- [${wi.type} #${wi.id}] ${wi.title}`)
      .join("\n");

    const message = await client.messages.create({
      model: "claude-sonnet-4-6",
      max_tokens: 1024,
      messages: [{
        role: "user",
        content: `Generate release notes for a merged PR to the Agency platform.

Repo: ${repo} — ${repoContext[repo]}
PR: ${prTitle}
Description: ${prDescription}

Linked Work Items:
${workItemsList || "None"}

Files Changed:
${diffSummary}

Format:
## What's New
[User-facing features, business-readable, no jargon]

## Bug Fixes
[Resolved issues, if any]

## Technical Changes
[Infrastructure, refactoring, dependency updates]

Be concise. "What's New" must be understandable by a non-developer.`,
      }],
    });

    const text = message.content[0].type === "text" ? message.content[0].text : "";
    return { content: [{ type: "text", text }] };
  }
);

async function main() {
  await server.connect(new StdioServerTransport());
}
main();
```

### Step 3 — Pipeline invocation script

Create `tools/scripts/generate-release-notes.sh`:

```bash
#!/bin/bash
set -e

REPO="$1"          # e.g. agency-gig-api
PR_NUMBER="$2"
GH_PAT="$3"

ORG="peter-at-integrant"

# Fetch PR details
PR=$(curl -s -H "Authorization: token ${GH_PAT}" \
  "https://api.github.com/repos/${ORG}/${REPO}/pulls/${PR_NUMBER}")

PR_TITLE=$(echo "$PR" | jq -r '.title')
PR_DESC=$(echo "$PR" | jq -r '.body // ""')

# Fetch linked issues (from PR body — GitHub links via #<number>)
LINKED_ISSUES=$(echo "$PR_DESC" | grep -oP '#\K[0-9]+' | while read id; do
  curl -s -H "Authorization: token ${GH_PAT}" \
    "https://api.github.com/repos/${ORG}/${REPO}/issues/${id}" \
    | jq '{id: .number, type: "Issue", title: .title}'
done | jq -s '.')

DIFF_SUMMARY=$(git diff --stat HEAD~1 HEAD)

# Call MCP server
node mcp-doc-server/dist/index.js <<EOF > "${RUNNER_TEMP}/release-notes.md"
{
  "method": "tools/call",
  "params": {
    "name": "generate_release_notes",
    "arguments": {
      "repo": "${REPO}",
      "prTitle": "${PR_TITLE}",
      "prDescription": "${PR_DESC}",
      "linkedWorkItems": ${LINKED_ISSUES},
      "diffSummary": "${DIFF_SUMMARY}"
    }
  }
}
EOF

echo "DOCS_GENERATED=true" >> "$GITHUB_ENV"
echo "Release notes written to artifact."
```

### Step 4 — GitHub Actions workflow integration

Add to each Agency repo's `.github/workflows/ci.yml`:

```yaml
jobs:
  documentation:
    name: Generate Documentation
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Build MCP doc server
        run: cd mcp-doc-server && npm ci && npm run build

      - name: Generate release notes
        env:
          GH_PAT: ${{ secrets.GH_PAT }}
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          bash tools/scripts/generate-release-notes.sh \
            "${{ github.event.repository.name }}" \
            "${{ github.event.pull_request.number }}" \
            "${{ secrets.GH_PAT }}"

      - name: Upload release docs artifact
        uses: actions/upload-artifact@v4
        with:
          name: release-docs
          path: ${{ runner.temp }}/release-notes.md

  docs-gate:
    name: Documentation Gate
    needs: documentation
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: release-docs
          path: release-docs

      - name: Confirm release notes present
        run: |
          if [ ! -f "release-docs/release-notes.md" ]; then
            echo "::error::Release notes artifact missing"
            exit 1
          fi
```

### Step 5 — End-to-end test

1. Open a real PR in `agency-gig-api` (e.g. "Add BookingRequest confirmation endpoint")
2. Link it to an issue: Issue #12 "As a venue manager, I want to confirm booking requests"
3. Merge the PR
4. Verify `release-notes.md` artifact appears with correct content
5. Get TPL to review quality — confirm "What's New" is business-readable

### Step 6 — Team walkthrough

30-minute session:
- Show the GitHub Actions Documentation job
- Open a real generated `release-notes.md`
- Show how to update the prompt template in `mcp-doc-server/src/index.ts`

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| MCP SDK | Expose Claude call as a structured, reusable MCP tool |
| Claude API (`claude-sonnet-4-6`) | Generate release notes content |
| GitHub Actions | Trigger on PR merge, gate on artifact |
| GitHub REST API | Fetch PR title, description, linked issues |
| GitHub Secrets | Store `ANTHROPIC_API_KEY` and `GH_PAT` |

---

## Proof of Knowledge

By completing this goal, the implementor demonstrates:
- MCP server construction using the MCP SDK (tool registration, schema, handler)
- Claude API integration within a CI/CD pipeline context
- GitHub Actions workflow design: jobs, artifact publishing, gate conditions
- Secure secrets management with GitHub Secrets

---

## Demo Scenario

> Merge PR "Add post-gig review endpoint" in `agency-gig-api`.
> Open the GitHub Actions run → Documentation job → `release-docs` artifact.
> Show `release-notes.md`:
>
> ```markdown
> ## What's New
> Bands and venues can now leave reviews after a completed gig, with a 1–5 star
> rating and optional comment. Reviews are visible on the other party's profile.
>
> ## Technical Changes
> - Added Review entity, EF Core migration, and REST endpoint POST /api/reviews
> - Review submission gated on booking status = Completed and gig date passed
> ```

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| API key in pipeline logs | Use GitHub Secrets; never echo the key |
| Generated notes poor quality first time | Iterate prompt template; TPL reviews first 5 outputs |
| Pipeline latency | Run Documentation stage in parallel with tests |
| MCP server cold start | Cache `node_modules`; pre-build in earlier pipeline step |
