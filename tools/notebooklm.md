---
name: NotebookLM — nlm CLI (jacob-bd) + MCP server, auth via profiles
description: Auth at ~/.notebooklm-mcp-cli/profiles/strm.line.app@gmail.com. Full CLI for queries, artifacts (audio/video/slides/reports/quiz/flashcards/mindmap), notes, sharing. MCP server via Claude Code (BROKEN auth).
type: project
originSessionId: 9d5acc4f-f50a-4d3b-9c5c-6c288c719948
---

## Setup — installed and working as of 2026-05-05 (updated with pipx isolation)
- **CLI**: `nlm` (notebooklm-mcp-cli v0.6.5) installed via pip at `/opt/homebrew/bin/nlm`
- **MCP server**: `notebooklm-mcp v2.0.11` installed via **pipx** in isolated env at `~/.local/pipx/venvs/notebooklm-mcp/bin/notebooklm-mcp`
- **MCP config**: `.claude.json mcpServers.notebooklm-mcp.command` → `/Users/bernardoresende/.local/bin/notebooklm-mcp`
- **Auth**: profile-based, stored at `~/.notebooklm-mcp-cli/profiles/strm.line.app@gmail.com` (54 cookies)
- **Account**: strm.line.app@gmail.com

## CRITICAL: Use nlm CLI, NOT MCP server
- **The `nlm` CLI has working auth** and is the reliable approach for ALL notebook operations.
- **The MCP server (`notebooklm-mcp`) has broken authentication** — Selenium/CDP browser auth fails when cookies expire. Returns `needs_auth` on healthcheck, refuses all queries even though CLI works fine with the same profile.
- **Never try to fix MCP server auth by copying cookies** — Chrome encrypted_value uses AES-256-CBC with macOS Keychain-derived key. Complex and fragile to reverse-engineer.
- **Never try `nlm setup add claude-code`** — only configures CLI auth, not MCP server's browser session.

## 1. Querying & Chat
- **Query**: `nlm query notebook <id> "<question>" -p strm.line.app@gmail.com`
- **JSON output**: append `--json` (returns `{value: {answer, conversation_id, sources_used, citations}}`)
- **Follow-up (session RAG)**: append `-c <conversation_id>` from previous response
- **Query specific sources**: add `-s source1,source2` (comma-separated)

## 2. Notebook Management
- **List notebooks**: `nlm notebook list -p strm.line.app@gmail.com --json`
- **Get details**: `nlm get notebook <id> -p strm.line.app@gmail.com`
- **Delete**: `nlm notebook delete <id> -p strm.line.app@gmail.com --confirm`
- **Add source (URL)**: `nlm notebook add-source <id> -p strm.line.app@gmail.com --url <URL>`
- **Add source (text)**: `nlm notebook add-source <id> -p strm.line.app@gmail.com --title "<name>"`

## 3. Source Management
- **List sources**: `nlm source list <id> -p strm.line.app@gmail.com --json` (supports `-j`)
- **Source list options**: `--full/-a`, `--drive/-d` (freshness status), `--quiet/-q` (IDs only)
- **Get source details**: `nlm get source <id> -p strm.line.app@gmail.com`
- **Describe source**: `nlm source describe <id> -p strm.line.app@gmail.com` (AI-generated summary with keywords)
- **Get raw content**: `nlm source content <id> -p strm.line.app@gmail.com` (no AI processing)
- **Rename source**: `nlm rename source <id> -p strm.line.app@gmail.com`
- **Delete source**: `nlm delete source <id> -p strm.line.app@gmail.com`
- **List stale sources**: `nlm list stale-sources -p strm.line.app@gmail.com`

## 4. Studio Artifacts (Generation)
Create artifacts from notebook sources:
- **Audio overview**: `nlm audio create <id> -p strm.line.app@gmail.com --confirm`
  - Options: `-f deep_dive|brief|critique|debate`, `-l short|default|long`, `--language <BCP-47>`, `--focus`
  - Returns artifact ID; check progress with studio status
- **Slide deck**: `nlm slides create <id> -p strm.line.app@gmail.com --confirm` (also has `nlm slides revise`)
- **Video overview**: `nlm video create <id> -p strm.line.app@gmail.com --confirm`
- **Infographic**: `nlm infographic create <id> -p strm.line.app@gmail.com --confirm`
- **Report**: `nlm report create <id> -p strm.line.app@gmail.com --confirm`
- **Quiz**: `nlm quiz create <id> -p strm.line.app@gmail.com --confirm`
- **Flashcards**: `nlm flashcards create <id> -p strm.line.app@gmail.com --confirm`
- **Mind map**: `nlm mindmap create <id> -p strm.line.app@gmail.com --confirm`
- **Data table**: `nlm data-table create <id> -p strm.line.app@gmail.com --confirm`

## 5. Studio Artifact Management
- **List artifacts**: `nlm studio status <notebook_id> -p strm.line.app@gmail.com --json`
- **Delete artifact**: `nlm studio delete <artifact_id> -p strm.line.app@gmail.com`
- **Rename artifact**: `nlm studio rename <artifact_id> -p strm.line.app@gmail.com`
- **General CRUD**: `nlm list artifacts`, `nlm delete artifact <id>`, `nlm rename studio`

## 6. Notes
- **List notes**: `nlm note list <notebook_id> -p strm.line.app@gmail.com`
- **Create**: `nlm note create <notebook_id> -p strm.line.app@gmail.com --title "<name>"` (requires `--content/-c`)
- **Update**: `nlm note update <note_id> -p strm.line.app@gmail.com`
- **Delete**: `nlm note delete <note_id> -p strm.line.app@gmail.com`

## 7. Sharing
- **Status**: `nlm share status <notebook_id> -p strm.line.app@gmail.com`
- **Public**: `nlm share public <notebook_id> -p strm.line.app@gmail.com`
- **Private**: `nlm share private <notebook_id> -p strm.line.app@gmail.com`
- **Invite**: `nlm share invite <notebook_id> -p strm.line.app@gmail.com`
- **Batch invite**: `nlm share batch <notebook_id> -p strm.line.app@gmail.com`

## 8. Export (requires prior artifact creation)
- **Artifact to Docs/Sheets**: `nlm export artifact <artifact_id> -p strm.line.app@gmail.com`
- **Report to Docs**: `nlm export to-docs <notebook_id> <artifact_id> -p strm.line.app@gmail.com`
  - **TWO-STEP**: First `nlm report create <id> --confirm` → wait for status to complete → export
  - **Requires BOTH notebook_id AND artifact_id** — audio/slides/video cannot be exported to Docs; create a report first
- **Data table to Sheets**: `nlm export to-sheets <table_id> -p strm.line.app@gmail.com`

## 9. Download
- **Audio**: `nlm download audio <artifact_id> -p strm.line.app@gmail.com`
- **Video**: `nlm download video <artifact_id> -p strm.line.app@gmail.com`
- **Slides (PDF/PPTX)**: `nlm download slide-deck <artifact_id> -p strm.line.app@gmail.com`
- **Infographic (PNG)**: `nlm download infographic <artifact_id> -p strm.line.app@gmail.com`
- **Report (Markdown)**: `nlm download report <artifact_id> -p strm.line.app@gmail.com`
- **Mind map (JSON)**: `nlm download mind-map <artifact_id> -p strm.line.app@gmail.com`

## 10. Auth Management
- **Re-authenticate**: `nlm login -p strm.line.app@gmail.com` (opens Chrome)
- **Check auth**: `nlm login --check -p strm.line.app@gmail.com`
- **List profiles**: `nlm login profile list` (shows default, strm.line.app, strm.line.app@gmail.com)
- **Delete profile**: `nlm login profile delete <profile> -y`

## 11. Aliases & Configuration
- **Set alias**: `nlm set alias <alias> -p strm.line.app@gmail.com`
- **Show aliases**: `nlm show aliases -p strm.line.app@gmail.com`
- **Chat config**: `nlm configure chat <notebook_id> -p strm.line.app@gmail.com`

## 12. Research & Discovery
Use `nlm research` to discover new sources on a topic — searches the web or Google Drive, then imports results into your notebook.
- **Start**: `nlm research start "<query>" -p strm.line.app@gmail.com`
  - `-s web|drive`, `-m fast (~30s, ~10) | deep (~5min, ~40)` (web only), `-n <notebook_id>`, `--auto-import`
  - **IMPORTANT**: If a research task already exists for the notebook, use `--force` to overwrite it (interactive confirmation required)
- **Status**: `nlm research status <notebook_id> -p strm.line.app@gmail.com` — polls until done by default
- **Import**: `nlm research import <notebook_id> [task_id] -p strm.line.app@gmail.com` — `-i "1,3"` to select specific sources
- **Full workflow**: `start` → wait for status to complete (use `--force` if task exists) → `import`. Do NOT try to import while status is still running.

## 13. Pipelines
Run multi-step workflows on a notebook (e.g., create audio then slides in sequence):
- **List built-in pipelines**: `nlm pipeline list -p strm.line.app@gmail.com`
- **Run a pipeline**: `nlm pipeline run <pipeline_id> -p strm.line.app@gmail.com`
- **Create custom pipelines**: `nlm pipeline create <yaml_file> -p strm.line.app@gmail.com`

## 14. Sync
Refresh Google Drive sources when their content has changed (e.g., after editing the original Doc):
- **Sync all**: `nlm sync sources -p strm.line.app@gmail.com` — use when you suspect Drive content is stale

## 15. Tags
Labels for organizing notebooks across your workspace:
- **Add**: `nlm tag add <notebook_id> -p strm.line.app@gmail.com`
- **Remove**: `nlm tag remove <notebook_id> -p strm.line.app@gmail.com`
- **List tagged**: `nlm tag list -p strm.line.app@gmail.com`
- **Search by tags**: `nlm tag select "<query>" -p strm.line.app@gmail.com` — finds notebooks matching a query using tags

## 16. Labels
Organize sources within a notebook into categories (e.g., "background", "primary source"):
- **Auto-label**: `nlm label auto <notebook_id> -p strm.line.app@gmail.com` — AI generates thematic categories
- **Reorganize**: `nlm label reorganize <notebook_id> -p strm.line.app@gmail.com` — AI recategorizes into new labels
- **List**: `nlm label list <notebook_id> -p strm.line.app@gmail.com`
- **Create/Rename/Delete**: `nlm label create/rename/delete <label_id> -p strm.line.app@gmail.com`
- **Emoji**: `nlm label emoji <label_id> -p strm.line.app@gmail.com` — set a visual marker
- **Move source**: `nlm label move <source_id> -p strm.line.app@gmail.com` — assign a source to a label

## 17. Agent Guide — Interpreting User Requests
When the user mentions "NotebookLM" or related terms, map them to CLI commands:

- **"use NotebookLM on X"** → query a notebook about topic X (unless they specify which)
- **"check with NotebookLM"** → query a notebook about the subject matter (unless they specify which)
- **"add it to my NotebookLM"** → add a source URL/text via `nlm notebook add-source`
- **"create an audio/slides/report for my Notebook"** → `nlm {audio|slides|report} create <id> --confirm`
- **"what's in my NotebookLM"** → `nlm notebook list` to see available notebooks
- **"show me the artifacts in my Notebook"** → `nlm studio status <notebook_id> --json`
- **"download the slides/audio"** → `nlm download slide-deck|audio <artifact_id>` (after it completes)
- **"share this notebook"** → `nlm share public/private/invite` (or batch invites)
- **"export to Google Docs"** → **report only** — create report first, then `nlm export to-docs <notebook_id> <artifact_id>`
- **"create a note"** → `nlm note create <notebook_id> --title "..." -c "<content>"`
- **"show my notes"** → `nlm note list <notebook_id>`
- **"find sources about X" / "research this topic"** → `nlm research start "<query>" -s web`
- **"discover new sources on X and add to my notebook"** → `nlm research start "<query>" --auto-import`
- **"create audio and slides for my notebook"** → `nlm pipeline run <id>` (or create them manually)
- **"update my Drive sources"** → `nlm sync sources` (when Google Docs content changed)
- **"find notebooks about X"** → `nlm tag select "<query>"` (uses tags for search)
- **"organize my sources" / "categorize these sources by topic"** → `nlm label auto <notebook_id>`

## 18. Notebook inventory (~14 notebooks, varies over time)
Portuguese Cuisine & Culture (7), LLMs @M5 Max (50), Vines of Portugal: A Technical and Narrative History (27), Inferno Blues: The Deluxe Brand Bible (3+ artifacts), Nyxx — Research, Frameworks Series Dev Template, Atalaia Research.
