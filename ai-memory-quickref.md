---
name: AI Memory Systems — Quick Reference
description: Operational guide for all 5 memory levels: what they do, where files live, how to query each system
type: reference
---

## Overview

Five memory levels stack together. Each serves a different recall need — use the right one for the question.

---

## Level 1: Project Context (CLAUDE.md + MEMORY.md)
- **What:** Per-project context and decisions. Auto-scaffolded when Claude opens a new project for the first time.
- **Where:** `<project>/CLAUDE.md` and `<project>/MEMORY.md`
- **How to use:** Claude reads automatically at session start. No manual query needed — it's always in context for the active project.
- **Good for:** "What stack does Project X use?", "What did we decide about feature Y in this project?"

## Level 2: Structured Memory (~/.claude/memory/)
- **What:** Cross-project knowledge organized into folders. Auto-extracted from conversations via session-end hooks.
- **Where:** `~/.claude/memory/`
  - `general.md` — cross-project conventions and preferences
  - `tools/` — tool-specific config and patterns (GitHub MCP, Playwright, NotebookLM)
  - `domain/` — topic-specific knowledge (one file per domain)
- **Index:** `~/.claude/memory/MEMORY.md` — one-line index of all topic files
- **How to use:** Claude loads from the index at session start, then reads specific files when relevant.
- **Good for:** "How should I name variables in this project?", "What are the Vault wiki rules?"

## Level 3: Semantic Search (memsearch)
- **What:** BM25 + dense vector search across all indexed memory files. Finds relevant context without loading everything into prompt.
- **Where:** `~/.memsearch/` (Milvus Lite index, ONNX bge-m3 embeddings)
- **Config:** `~/.memsearch/config.toml` (provider: onnx)
- **How to query:** `/memory "what did we decide about X?"` — runs `~/.claude/tools/mem-search.sh`
- **How to re-index:** `~/.claude/tools/index-memory-files.sh --force`
- **Good for:** Finding buried decisions across long memory files, locating context from weeks ago

## Level 4: Memory Palace (mempalace)
- **What:** Verbatim word-for-word recall from ALL past Claude Code conversations. Indexed into ChromaDB (~7,500 drawers).
- **Where:** `~/.mempalace/` (ChromaDB data, JSONL conversation copies)
- **Config:** `~/.mempalace/config.json`, `~/.mempalace/mempalace.yaml`
- **How to query:** Run `~/.claude/tools/history-search.sh "what did we decide about X back in March?"`
- **Hooks:** Auto-filing on session end (`stop`) and before context compaction (`precompact`) in `~/.claude/hooks/`
- **Good for:** "What exactly did we discuss about Y on a specific date?", retrieving past conversation details

## Level 5: Vault Wiki (Obsidian)
- **What:** Interconnected knowledge graph about topics you care about. Source docs in `raw/`, wiki pages in `wiki/`.
- **Where:** `<project>/Vault/raw/` (read-only source material) and `<project>/Vault/wiki/` (Claude-maintained wiki pages)
- **Rules:** Never write to `raw/`. Claude owns `wiki/` — writes all pages, maintains `[[wikilinks]]` and cross-references.
- **Profiles:** Live in `wiki/` as wiki pages, managed through Claude (not direct editing).
- **Good for:** Deep research on interconnected topics, entity profiles, connecting disparate source material

---

## Decision Guide: Which System to Use

| Question type | Use this system |
|---|---|
| Project-specific context (stack, conventions) | Level 1 — auto-loaded |
| Cross-project convention or tool config | Level 2 — read from `~/.claude/memory/` index |
| Buried decision I can't remember the file name for | Level 3 — `/memory "query"` (semantic search) |
| What did we discuss about X on a specific date? | Level 4 — `history-search.sh` (verbatim recall) |
| Research on interconnected topics, entity profiles | Level 5 — Vault wiki (Obsidian graph) |

---

## Key File Paths Reference

| Resource | Path |
|---|---|
| Memory index | `~/.claude/memory/MEMORY.md` |
| Structured memory dir | `~/.claude/memory/` (general.md, tools/, domain/) |
| Semantic search query | `/memory "query"` → runs `~/.claude/tools/mem-search.sh` |
| Re-index memory files | `~/.claude/tools/index-memory-files.sh [--force]` |
| History search | `~/.claude/tools/history-search.sh "query"` |
| Memory Palace data | `~/.mempalace/` (ChromaDB + JSONL) |
| memsearch config | `~/.memsearch/config.toml` |
| Vault wiki rules | `<project>/Vault/CLAUDE.md` (never write to raw/) |
