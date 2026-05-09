---
name: GitHub MCP Server — Docker (ghcr.io/github)
description: 41 tools, Python subprocess testing pattern for tool validation and error handling
type: reference
---

# GitHub MCP Server (Docker) — `ghcr.io/github/github-mcp-server`

## Overview
- **Image**: `ghcr.io/github/github-mcp-server` (v1.0.3)
- **Configured in**: `~/.claude.json` → `"mcpServers"` block
- **Auth via env**: `GITHUB_PERSONAL_ACCESS_TOKEN` (from `~/.zshrc`)
- **Also available**: Remote HTTP server at `api.githubcopilot.com/mcp/` via official plugin (no PAT needed)

## Config
```json
"github-mcp-server": {
  "type": "stdio",
  "command": "docker",
  "args": ["run", "-i", "--rm", "ghcr.io/github/github-mcp-server"],
  "env": {
    "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PERSONAL_ACCESS_TOKEN}"
  }
}
```

## Testing with Python (Recommended)
Python `subprocess.Popen` is the most reliable way to test multiple tool invocations. Avoid expect/heredocs — they struggle with per-request response tracking and have Tcl quoting issues (`[...]` is command substitution).

### Working Pattern: `stdin.write()` + stdout `readline()` per message
```python
import subprocess, json, time

# Extract PAT from zshrc (never hardcode)
pat = None
with open('/Users/bernardoresende/.zshrc') as f:
    for line in f:
        if 'GITHUB_PERSONAL_ACCESS_TOKEN' in line and '=' in line:
            pat = line.strip().split('=', 1)[1].strip().strip("'\"")
            break

# ALL Docker args in ONE list — no extra positional or keyword "args=" argument
docker_args = [
    'docker', 'run', '-i', '--rm',
    '-e', f'GITHUB_PERSONAL_ACCESS_TOKEN={pat}',
    'ghcr.io/github/github-mcp-server'
]

proc = subprocess.Popen(
    docker_args,
    stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE,
    text=True          # enables readline() and auto newline handling
)

# Send initialize FIRST (required before any other request)
def send_and_wait(msg, timeout=30):
    """Send one message and wait for its response by matching id."""
    proc.stdin.write(json.dumps(msg) + "\n")
    proc.stdin.flush()
    deadline = time.time() + timeout
    while time.time() < deadline:
        line = proc.stdout.readline()
        if not line:                  # server exited
            return None
        line = line.strip()
        if not line:
            continue
        try:
            resp = json.loads(line)
            if resp.get("id") == msg["id"]:
                return resp           # ← our response
        except json.JSONDecodeError:
            pass                      # server log line or spurious output

# Every request MUST have a "params" field, even for methods that take no arguments
init_resp = send_and_wait({
    "jsonrpc": "2.0", "id": 1, "method": "initialize",
    "params": {
        "protocolVersion": "2024-11-05",
        "capabilities": {},
        "clientInfo": {"name": "test-client", "version": "1.0"}
    }
})

# List available tools (get names for tool/call)
tools_resp = send_and_wait({"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}})
tool_list = tools_resp["result"]["tools"]

# Call a tool via tools/call (NOT direct method like "users/get_me")
resp = send_and_wait({
    "jsonrpc": "2.0", "id": 3, "method": "tools/call",
    "params": {"name": "get_me", "arguments": {}}
})

# Parse response — MCP tools return content as a list of objects with "type":"text"
if resp and "result" in resp:
    for item in resp["result"]:
        if isinstance(item, dict):
            print(item.get("text", ""))

proc.terminate()
try: proc.wait(timeout=5)
except subprocess.TimeoutExpired: proc.kill()
```

### Full Test Script Template
A working template is at `/tmp/test-github-mcp-tool.py` — it tests any tool with a single command.

### Key Protocol Rules
- **NDJSON**: Each message is a JSON object terminated by `\n`
- **Every request needs `"params"`** — omitting it gives error `-32600` ("invalid request: missing required params")
- **Initialize first**: Must call `initialize` before any other method (use `"tools/call"` with id=1)
- **Tools are called via `tools/call`**: Pass the tool name (from `tools/list`) and an `arguments` dict
- **Response format**: `{jsonrpc, id, result|error}` — match `id` to correlate request/response
- **Tool response structure**: Varies! `result["content"]` is EITHER a list of `{type, text}` objects OR directly nested. Parse both patterns (see each skill for details)

### Common Pain Points
- **`proc.stdin.write()` must be followed by `.flush()`** — without it, the MCP server won't see your message
- **stderr to stdout**: Use `stderr=subprocess.STDOUT` so errors appear on the same stream you're reading from
- **Heredocs with Docker MCP**: EOF too early — only one message processed. Use Python subprocess instead
- **`communicate(input=...)` doesn't work** for MCP — sends everything at once, can't match responses by id

## Error Codes
| Code / Message | Meaning | Fix |
|----------------|---------|-----|
| `-32600` "invalid request" | Missing `params` field | Add `"params": {}` to every request |
| `JSON RPC not handled: unsupported` | Tool name is wrong or toolset disabled | Check `tools/list` for exact names; ensure required toolsets are enabled via `--toolsets=` flag |
| 404 on repo endpoints | Repo doesn't exist or is private without auth | Verify owner/repo names; confirm PAT has correct scopes |

## Available Tools (41)
**Search**: `search_repositories`, `search_code`, `search_issues`, `search_pull_requests`, `search_users`

**Repos**: `create_repository`, `fork_repository`, `get_file_contents`, `list_branches`, `list_commits`, `list_tags`, `delete_file`, `create_or_update_file`, `push_files`

**Issues**: `list_issues`, `issue_read`, `issue_write`, `add_issue_comment`, `add_comment_to_pending_review`, `list_issue_types`, `sub_issue_write`

**Pull Requests**: `list_pull_requests`, `pull_request_read`, `create_pull_request`, `update_pull_request`, `merge_pull_request`, `update_pull_request_branch`, `pull_request_review_write`, `request_copilot_review`

**Users / Account**: `get_me`, `get_teams`, `get_team_members`

**Releases / Tags**: `get_latest_release`, `get_release_by_tag`, `get_commit`, `get_tag`

**Branch / File Ops**: `create_branch`

## Common Pitfalls
- **Orphaned `-e` args**: Old config had `"-e", "GITHUB_PERSONAL_ACCESS_TOKEN"` as separate Docker args without values — broke auth. Fix: use `"-e", f"TOKEN={pat}"` with the actual value, or the `"env:"` block in `~/.claude.json`.
- **PAT value truncated**: When pasting commands inline, the PAT may get cut off. Always extract it from `~/.zshrc` programmatically.
- **Toolset defaults**: The default toolsets are `context, copilot, issues, pull_requests, repos, users`. If a tool is missing, launch with `--toolsets=all` to enable everything.
- **Empty responses**: Tools return empty content arrays for repos/users with no activity (0 issues, 0 commits, etc.). This is correct behavior — not an error.
