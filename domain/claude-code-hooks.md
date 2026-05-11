---
name: Claude Code Hook System
description: Hook events, registration patterns, common bugs, and execution environment details
type: reference
---

## Hook Events
Available events: `PreToolUse`, `InstructionsLoaded`, `SessionStart`, `Stop`, `SessionEnd`, `UserPromptSubmit`

## Registration
- Hooks registered in plugin `hooks.json` under `${CLAUDE_PLUGIN_ROOT}/hooks/`
- Root hooks also supported at `~/.claude/hooks/` (for standalone scripts)
- Each plugin needs `.claude-plugin/plugin.json` or it's silently ignored

## Execution Environment Gotchas
- Hooks run in non-interactive shell — `~` does NOT expand in f-strings, use `{Path.home()}/...`
- `pathlib.Path.replace()` replaces path separators — cast to str first: `str(path).replace(...)`
- Environment variables (ANTHROPIC_API_KEY) may not propagate — add fallback to read from `.zshrc`
- Hooks have no stdout capture by default — use stderr for debugging

## Anti-Patterns
- **Double-firing:** Don't register same hook on both Stop AND SessionEnd (they fire simultaneously)
- **Triple-firing:** Don't register on Stop + SessionEnd + UserPromptSubmit unless you want 3x execution
- **Duplicate hooks:** Don't maintain hooks in both root `.claude/hooks/` and plugin directories

## Output Files
- `outputFile` field in hooks.json is deprecated/ignored by Claude Code
- Hook output goes to stderr/stdout — no file capture mechanism

## Session Extraction Pattern
- `session-end-extract.py`: Finds recent JSONL conversation file, extracts text, sends to Anthropic API for structured extraction (5 categories), appends to project MEMORY.md with `---` separators
