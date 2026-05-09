---
name: GitHub PAT & gh CLI Patterns
description: Personal access token location, gh CLI usage patterns for memory repo backup and Git operations
type: tools
---

## GitHub PAT (Personal Access Token) — `~/.claude/.github-token`
- **Token location:** `~/.claude/.github-token` — gitignored, never commit this file
- **Account:** strmlineapp-ux (Bernardo Resende)
- **Scopes:** full `repo` + admin scopes, workflow, packages, gist, etc.
- **Usage:** Set `GITHUB_TOKEN="$(cat ~/.claude/.github-token)"` before calling gh CLI

## Memory Backup Repo
- **Repo:** `strmlineapp-ux/memory` at https://github.com/strmlineapp-ux/memory
- **Path:** `~/.claude/memory/` — git repo, remote: `origin https://github.com/strmlineapp-ux/memory.git`
- **.gitignore:** history.db, credentials/, .github-token

## gh CLI (standalone)
- **Location:** `$HOME/bin/gh` — macOS arm64 zip from GitHub releases, do NOT use sudo
- **Auth:** `GITHUB_TOKEN="$(cat ~/.claude/.github-token)"` or use PAT URL format

## Common Patterns
```bash
# Push with token: GITHUB_TOKEN="$(cat ~/.claude/.github-token)" GIT_ASKPASS=true git push
# gh CLI: GITHUB_TOKEN="$(cat ~/.claude/.github-token)" ~/bin/gh repo create ... --public
# Verify: ~/bin/gh auth status

```

