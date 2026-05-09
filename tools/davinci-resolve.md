---
name: DaVinci Resolve — MCP Server (328 tools)
description: 328 granular tools for video editing, audio post-production, and color grading
type: reference
---

# DaVinci Resolve MCP Server

- **Repo:** https://github.com/samuelgursky/davinci-resolve-mcp
- **Location:** `/Users/bernardoresende/Documents/MCP Tools/davinci-mcp` (created 2026-05-07)
- **Version:** v2.5.0, 328 granular tools
- **Server entry point:** `MCP Tools/davinci-mcp/src/resolve_mcp_server.py` (stdio)
- **Venv:** `MCP Tools/davinci-mcp/.venv/bin/python`

## Configuration (MCP server in settings.json)
```json
"davinci-resolve": {
  "command": "/Users/bernardoresende/Documents/MCP Tools/davinci-mcp/.venv/bin/python",
  "args": ["/Users/bernardoresende/Documents/MCP Tools/davinci-mcp/src/resolve_mcp_server.py"],
  "env": {"PYTHONPATH": "/Users/bernardoresende/Documents/MCP Tools/davinci-mcp"}
}
```

## Resolved API paths (macOS)
- **API path:** `/Library/Application Support/Blackmagic Design/DaVinci Resolve/Developer/Scripting`
- **Fusion lib:** `/Applications/DaVinci Resolve/DaVinci Resolve.app/Contents/Libraries/Fusion/fusionscript.so`

## Key tool categories
- **Projects:** create, open, close, save, delete, import/export cloud projects
- **Timelines & clips:** create timelines, insert/delete clips, markers, keyframes, tracks
- **Media pool & storage:** import media, manage folders, link proxy/full-res media
- **Color grading (328 tools):** node graph operations, CDL/LUTs, grades from DRX files
- **Fusion:** add/edit/delete compositions and generators
- **Fairlight presets, galleries/stills, rendering jobs**

## Requirements
DaVinci Resolve must be running (currently on Studio 21.0.0b.28).
