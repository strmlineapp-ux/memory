---
name: General — Cross-Project Conventions
description: Workspace conventions, workflow preferences, and environment setup
type: general
---

# General — Cross-Project Conventions

## Workflow Preferences

### Web Search Fallback Rule
- **Never use `WebSearch`** — it errors with API 422. Use `WebFetch` for URL fetches or Playwright (`mcp__playwright*`) for interactive browsing.
- **Why:** WebSearch is not functional in this environment, so any attempt wastes a turn.
- **How to apply:** When the user asks me to look up something on the web, use WebFetch with a relevant URL or Playwright for more complex navigation.

### Hook Configuration
- Hooks go inside plugins' `hooks.json`, not in `~/.claude/settings.json`.
- Hook command format: `"command": "python3 ${CLAUDE_PLUGIN_ROOT}/hooks/pre-tool-memory.py"`
- Available hook events: `PreToolUse`, `SessionStart`, `Stop`, `UserPromptSubmit`

### MCP Servers — Global Config in settings.json
- **davinci-resolve** and **playwright** are configured globally in `~/.claude/settings.json` under `"claudeCode.mcpServers"`.
- **davinci-resolve** also has a project `.mcp.json` in `~/Developer/davinci-resolve-mcp/` (for repo sharing), but the global config takes precedence.
- Both are available in every project, not just their respective repos.

