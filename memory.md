# Memory Index

Read this file at session start. Load specific topic files only when relevant.

| File | Description | Last updated |
|------|-------------|--------------|
| `general.md` | Cross-project conventions and preferences | 2026-05-08 |
| `ai-memory-quickref.md` | Operational guide for all 5 memory levels, systems, and file paths | 2026-05-08 |
| `tools/github-mcp.md` | GitHub PAT token location, gh CLI patterns for memory repo backup and Git operations | 2026-05-09 |
| `tools/github-mcp-docker.md` | GitHub MCP server config and usage patterns (41 tools) — Docker image at ghcr.io/github | 2026-05-09 |
| `tools/notebooklm.md` | NotebookLM CLI and MCP server docs (BROKEN auth) — use `nlm` CLI, NOT MCP | 2026-05-08 |
| `tools/playwright.md` | Playwright MCP server patterns (includes LinkedIn login for profiles) | 2026-05-07 |
| `tools/comfyui-mcp.md` | ComfyUI MCP server (artokun) — local instance, TypeScript. Entry: `npx -y comfyui-mcp`. Repo at ~/Documents/MCP Tools/comfyui-mcp | 2026-05-09 |
| `tools/davinci-resolve.md` | DaVinci Resolve MCP server (328 tools) — configured in settings.json. Repo at ~/Documents/MCP Tools/davinci-mcp | 2026-05-09 |
| `wiki-maintenance.md` | Wiki entity isolation, wikilink semantics — NCore/MSM/Tamarind separation rules | 2026-05-08 |
| `credentials/linkedin.json` | Bernardo's LinkedIn login (gitignored) — see playwright.md for usage | 2026-05-07 |

## Cross-Memory Sync Rule

At session start, after reading this file:
1. Note the Last updated dates in the table above
2. If any project MEMORY.md has content worth promoting to a global tools/ or domain/ file, flag it
3. Update the Last updated date on this file after any changes
