---
name: Playwright MCP Server (@playwright/mcp) — browser automation via Claude Code MCP tools
description: Microsoft's Playwright MCP server provides ~52 tools for full browser automation (navigation, clicking/typing via accessibility snapshots). Tool prefix: mcp__playwright*. Use as fallback for WebFetch when pages require JS execution, login state, or form interaction.
type: reference
originSessionId: 6960811c-8363-4b6f-acca-5c13d326c5af
---
## Overview
- **Package**: `@playwright/mcp` (github.com/microsoft/playwright-mcp)
- **Installed via**: `npx @playwright/mcp` (runs on every Claude Code startup)
- **Tool prefix**: `mcp__playwright*` — all tools accessible via Bash with this naming pattern
- **Runs in**: `~/.claude.json` under `"playwright"` MCP server config

## How It Works
Unlike WebFetch (which fetches raw HTML), Playwright runs a real browser and interacts with pages through **structured accessibility snapshots** — not screenshots. The LLM navigates by reading element labels from the accessibility tree and clicking/typing on them directly.

## Key Tools
- `browser_navigate` — Go to a URL (returns accessibility snapshot)
- `browser_snapshot` — Get current page state as accessibility tree
- `browser_click` / `browser_type` — Interact with elements by label/descriptor
- `browser_evaluate` — Execute JavaScript in the page context
- `browser_console_messages` / `browser_network_requests` — Inspect runtime state

## Common Patterns
1. **Fetch a page**: `browser_navigate` → read snapshot for content/structure
2. **Interact with a page**: `browser_navigate` → `browser_snapshot` → actions by element label
3. **Debug JS issues**: `browser_evaluate("document.title")` or check console/network
4. **Login-required pages**: Use cookies/localStorage manipulation to preserve auth state

## When To Use
- Pages requiring JavaScript execution (SPAs, React/Vue apps)
- Login-required pages where WebFetch can't authenticate
- Forms that need to be filled out (search, filters, multi-step flows)
- When you need to verify UI behavior or DOM state

## Common Gotchas
- Tools are accessed via Bash, not a dedicated MCP tool — check available tools at runtime with `mcp__playwright*`
- Snapshots are text-based accessibility trees — not screenshots. You can't "see" the page visually through them
- The server runs `npx @playwright/mcp` on startup, which launches a browser instance
- If the server is unresponsive (e.g., stale session), restart Claude Code to reset it

## Session State
- Browser sessions persist within a single Claude Code session but not across restarts
- For stateful interactions (login, multi-page flows), do everything in one session

## LinkedIn Profile Creation
**Credentials:** `~/.claude/memory/credentials/linkedin.json` (gitignored). Load before navigating LinkedIn during wiki profile creation.
**Process:** Navigate to login → fill form with email/password from credentials file → submit → wait for redirect before scraping.
