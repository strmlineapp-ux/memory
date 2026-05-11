# Memory Changelog

## 2026-05-11 — Reorg: stale entries removed, notebooklm trimmed
- **What:** Removed CHANGELOG entries from May 9-10 (work completed in previous session)
- **What:** Trimmed notebooklm.md from 175 to ~80 lines — removed redundant sections (14-19: sync, tags, labels, MCP tools, session RAG, agent guide)
- **What:** Moved "Before Reorganizing Memory" meta-instructions from general.md into CHANGELOG
- **Why:** CHANGELOG had accumulated stale entries; notebooklm.md was bloated with sections rarely needed by agents

## 2026-05-10 — Full memory system revision
- **What:** Cleaned project MEMORY.md from 867 lines to ~50 (removed 31 duplicate session extractions)
- **What:** Fixed hooks — removed double-firing (Stop+SessionEnd → Stop only) for extract and git-backup plugins
- **What:** Deleted duplicate root pre-tool-memory.py, cleaned stale plugin cache
- **What:** Created domain/ directory with davinci-resolve-mcp.md and claude-code-hooks.md
- **What:** Added error logging and API key fallback to session-end-extract.py
- **Why:** Project MEMORY.md had accumulated 867 lines of near-duplicate content across 32 sessions; hooks were wasting API calls and git operations running 2-3x per session

## 2026-05-09 — Git backup, PAT security, docs split
- **What:** Created `strmlineapp-ux/memory` GitHub repo and initialized git at `~/.claude/memory/`; PAT moved to `~/.claude/.github-token` (gitignored); GitHub MCP docs split into two files
- **Why:** Memory files were not versioned (critical risk), tokens in memory files are insecure, and the original GitHub MCP file was 150 lines mixing two unrelated topics
