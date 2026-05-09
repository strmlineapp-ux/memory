# Memory Changelog

## 2026-05-09 — Git backup of memory files
- **What:** Created `strmlineapp-ux/memory` GitHub repo, initialized git at `~/.claude/memory/`, pushed initial commit
- **Why:** Memory files were not versioned and had no backup — critical risk if machine is lost
- **How:** Git repo with remote to GitHub; auto-push hook added via git-backup plugin (runs at Stop/SessionEnd/UserPromptSubmit)

## 2026-05-09 — GitHub PAT stored in dotfile
- **What:** Token moved from memory files to `~/.claude/.github-token` (gitignored)
- **Why:** Storing tokens in memory files is insecure; dotfile with chmod 600 + gitignore is safer
- **How:** Updated tools/github-mcp.md to document the pattern; added `.github-token` and `history.db` to global gitignore

## 2026-05-09 — GitHub MCP docs split into two files
- **What:** `tools/github-mcp.md` now contains only PAT + gh CLI patterns; Docker MCP server docs moved to `tools/github-mcp-docker.md`
- **Why:** The original file was 150 lines mixing two unrelated topics; splitting makes both easier to find
- **How:** Created new file, updated memory index in `memory.md`
