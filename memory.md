# Memory Index

Read this file at session start. Load specific topic files only when relevant.

| File | Description | Last updated |
|------|-------------|--------------|
| `general.md` | Cross-project conventions and preferences (WebSearch fallback, hook config) | 2026-05-11
| `ai-memory-quickref.md` | Operational guide for all 5 memory levels, systems, and file paths | 2026-05-09
| `tools/github-cli.md` | GitHub PAT, gh CLI patterns, memory backup repo setup | 2026-05-11
| `tools/github-mcp-docker.md` | Docker MCP server config (41 tools), Python subprocess testing pattern, protocol rules and error codes | 2026-05-11
| `tools/notebooklm.md` | NotebookLM CLI and MCP server docs (BROKEN auth) — use `nlm` CLI, NOT MCP | 2026-05-11
| `tools/playwright.md` | Playwright MCP server patterns (includes LinkedIn login for profiles) — global config in settings.json | 2026-05-10
| `tools/obsidian-cli.md` | Obsidian CLI (v1.12+) — terminal interface to vault via IPC, 130+ commands for notes/daily/tasks/search/properties. Skill installed at ~/.claude/skills/obsidian-cli/, plugin in settings.json | 2026-05-11
| `domain/davinci-resolve-mcp.md` | DaVinci Resolve Studio 21 MCP automation: server setup, architecture, tool-to-object map (30 tools), workflow patterns (project lifecycle, media import, clip property control with transform/crop/composite/retiming keys, color node ops, render pipeline), AI features (scriptable vs UI-only) | 2026-05-11
| `domain/claude-code-hooks.md` | Claude Code hook events, registration patterns, execution gotchas (tilde expansion, pathlib.replace), anti-patterns | 2026-05-10
| `domain/comfyui.md` | ComfyUI 0.20.1 macOS app: API format rules, subgraph expansion pattern, node gotchas (TextGenerate, ComfySwitchNode), installed models | 2026-05-11

## Canonical Template (project MEMORY.md)

When creating a new project `MEMORY.md`, use this template:

```markdown
# {Project Name} - Project Memory

## Global Memory

Cross-project memory topics are indexed at ~/.claude/memory/MEMORY.md (loaded automatically).
Read it for the full list of available topic files.

## Project Notes
```

When a new file is added to `~/.claude/memory/`, update only the global MEMORY.md.
