---
name: General — Cross-Project Conventions
description: User profile, workspace conventions, and workflow preferences
type: general
---

# General — Cross-Project Conventions

## User Profile (read at session start)
**Bernardo Resende** — freelancer, based in London. No formal coding background; explain technical concepts when relevant. Collaborates with AI tools for development across multiple projects (Core, Ncore, Marky Sparky Media).

## Workflow Preferences

### Web Search Fallback Rule
- **Never use the `WebSearch` tool** — it errors out with API 422 responses.
- **Use `WebFetch`** for fetching and summarizing a URL, or Playwright (`mcp__playwright*`) for interactive browsing.
- **Why:** WebSearch is not functional in this environment, so any attempt wastes a turn.
- **How to apply:** When the user asks me to look up something on the web, use WebFetch with a relevant URL or Playwright for more complex navigation.

### Hook Configuration
- Hooks go inside plugins' `hooks.json`, not in `~/.claude/settings.json`.
- Hook command format: `"command": "python3 ${CLAUDE_PLUGIN_ROOT}/hooks/pre-tool-memory.py"`
- Available hook events: `PreToolUse`, `SessionStart`, `Stop`, `UserPromptSubmit`
