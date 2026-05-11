---
name: GitHub CLI (gh) — PAT & Patterns
description: Personal access token location, gh CLI usage patterns, memory backup repo setup
type: tools
---

## GitHub PAT — `~/.claude/.github-token`
- **Token location:** `~/.claude/.github-token` — gitignored, never commit
- **Account:** strmlineapp-ux (Bernardo Resende)
- **Scopes:** full repo + admin, workflow, packages, gist, etc.
- **Usage:** `GITHUB_TOKEN="$(cat ~/.claude/.github-token)"` before calling gh CLI

## Memory Backup Repo
- **Repo:** `strmlineapp-ux/memory` at https://github.com/strmlineapp-ux/memory
- **Path:** `~/.claude/memory/` — git repo, remote: origin https://github.com/strmlineapp-ux/memory.git
- **.gitignore:** history.db, credentials/, .github-token

## gh CLI (standalone)
- **Location:** `$HOME/bin/gh` — macOS arm64 zip from GitHub releases, do NOT use sudo
- **Auth:** `GITHUB_TOKEN="$(cat ~/.claude/.github-token)"` or use PAT URL format

## Common gh CLI Patterns
```bash
# Push with token: GITHUB_TOKEN="$(cat ~/.claude/.github-token)" GIT_ASKPASS=true git push
# Create repo: GITHUB_TOKEN="$(cat ~/.claude/.github-token)" ~/bin/gh repo create ... --public
# Verify auth: ~/bin/gh auth status
```
