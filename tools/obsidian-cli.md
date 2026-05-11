# Obsidian CLI

## Setup
- **Binary**: `obsidian` — registered in PATH when CLI enabled in Obsidian settings (Settings → Command line interface)
- **Prerequisite**: Obsidian desktop app must be running (CLI communicates via IPC)
- **Skill location**: `~/.claude/skills/obsidian-cli/SKILL.md` (130+ commands)
- **Full reference**: `~/.claude/skills/obsidian-cli/references/command-reference.md`
- **Plugin config**: `~/.claude/settings.json` → `plugins.obsidian-cli.source.repo = pablo-mano/Obsidian-CLI-skill`

## Key Patterns
- Paths are vault-relative: `folder/note.md`, not absolute filesystem paths
- `create` omits `.md` (extension added automatically)
- `move` requires full target path including `.md` extension
- Multi-vault: pass vault name as first arg — `obsidian "Vault Name" command`
- JSON output: use `format=json` on `search` for a JSON array of file paths
- Stderr noise (GPU/Electron warnings on headless) — filter with `2>/dev/null`
- `property:set` stores list values as strings, not YAML arrays — edit frontmatter directly or use `eval` for proper lists
- Colon subcommands (`property:set`, `daily:append`) may fail with exit 127 on Windows Git Bash — use non-colon alternatives

## Common Commands
```bash
obsidian read path="folder/note.md"
obsidian create path="folder/note" content="# Title"
obsidian append path="note.md" content="- [ ] Task"
obsidian search query="keyword" format=json
obsidian tasks                          # All tasks (done + todo)
obsidian orphans                        # Notes with no links
obsidian daily:append content="..."     # Append to today's note
obsidian property:set path="note.md" name="status" value="active"
```

## Troubleshooting
| Problem | Cause | Fix |
|---|---|---|
| Empty output / hangs | Obsidian not running, or admin terminal (Windows) | Start Obsidian; use normal-privilege terminal |
| `command not found` | CLI not registered in PATH | Re-enable CLI in Settings; restart terminal |
| IPC socket not found (Linux) | `PrivateTmp=true` in systemd | Set `PrivateTmp=false` |
| Colon+params exit 127 (Windows) | Missing `Obsidian.com` redirector | Reinstall from obsidian.md/download |
| Multi-vault `"Name" command` fails | Vault name matching issue | Omit vault name; target most recent vault |
