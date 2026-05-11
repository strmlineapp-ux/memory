---
name: DaVinci Resolve Studio 21 MCP Automation (30 compound / 328 granular tools)
description: Full AI-assisted automation of DaVinci Resolve Studio via samuelgursky/davinci-resolve-mcp. 30 compound tools, workflow patterns (project lifecycle, media import, color grading, Fusion compositing, rendering), AI features (Magic Mask v2/SuperScale/Speed Warp scriptable; IntelliScript/IntelliCut/Multicam SmartSwitch/Audio Assistant/Voice Convert UI-only), composite modes, codec matrix, error handling.
type: domain
---

## Server Setup
- **Repo:** https://github.com/samuelgursky/davinci-resolve-mcp
- **Location:** `/Users/bernardoresende/Developer/davinci-resolve-mcp`
- **Version:** v2.16.0 (30 compound tools, 328 full/granular)
- **Server entry point:** `src/server.py` (compound, default), add `--full` for 328 tools
- **Venv:** `/Users/bernardoresende/Developer/davinci-resolve-mcp/venv/bin/python3.12`
- **Config:** global in `~/.claude/settings.json`, project `.mcp.json` at repo root (for sharing)
- **API paths:** `/Library/Application Support/Blackmagic Design/DaVinci Resolve/Developer/Scripting` (API), `/Applications/DaVinci Resolve/DaVinci Resolve.app/Contents/Libraries/Fusion/fusionscript.so` (Fusion lib), `.../Modules` (Python modules)

## Architecture
- MCP server: `davinci-resolve-mcp` by Samuel Gursky (github.com/samuelgursky/davinci-resolve-mcp)
- 30 compound tools mapped to specific Resolve API objects (resolve, project_manager, media_pool, timeline_item, timeline_item_node, fusion_comp, render_preset, etc.)
- Strict tool-to-API-object matching required — mismatched calls return empty results or errors

## Prerequisites
- DaVinci Resolve Studio (free version has no scripting API) — currently on Studio 21.0.0b.28
- "Scripting Preference" set to **Local** in Resolve settings (mandatory for MCP handshake)
- Server auto-launches Resolve if not running — may take up to 60s on first call
- Verify connection with `resolve -> get_version` before complex tool chains

## Tool-to-Object Map (30 tools)
| Tool | API Object | Use for |
|------|-----------|---------|
| resolve | Resolve | App control, page navigation, version |
| project_manager | ProjectManager | Create/load/save/list/export projects, database ops |
| media_storage | MediaStorage | Browse volumes and filesystem, add items to pool |
| media_pool | MediaPool | Bins, import, timeline creation, clip selection |
| folder | Folder | Clip lists, subfolder traversal, transcribe audio |
| media_pool_item | MediaPoolItem | Clip name, metadata, markers, properties, proxy linking |
| timeline | Timeline | Track ops, markers, export, detect scene cuts, settings |
| timeline_item | TimelineItem | Per-clip properties, markers, grade copy, magic mask, stabilize |
| timeline_item_fusion | TimelineItem.Fusion | Add/import/export/delete Fusion compositions |
| timeline_item_color | TimelineItem + Graph | LUT, CDL, node add/delete/graph on color nodes |
| timeline_item_version | TimelineItem.Versions | Grade versions (add/load/rename/delete) |
| timeline_item_takes | TimelineItem.Takes | Take management for alternate source clips |
| render | Project.Render | Render queue add/delete/start/stop/status/formats |
| gallery | Gallery | Albums, stills navigation |
| gallery_stills | GalleryStillAlbum | Grab/export/import/apply/delete stills and PowerGrades |
| color_group | ColorGroup | Scene/shot color groups |
| fusion_comp | Fusion Composition | Node graph: add/wire/set params/keyframe/render |

## Core Workflow Patterns
### Project Lifecycle
```
project_manager → create_project → name: "ProjectName"
project_manager → load_project → name: "ExistingProject"
project_manager → save_project
```

### Media Import → Timeline
```
media_pool → import_media → items: ["/path/file.mov", ...]
media_pool → create_timeline_from_clips → name: "Assembly", clips: [...]
```

### Clip Property Control (via timeline_item → set_property)
- Transform: Pan, Tilt, ZoomX, ZoomY, RotationAngle, Pitch, Yaw
- Crop: CropLeft/Right/Top/Bottom, Softness, Retain
- Composite: CompositeMode (0–29), Opacity (0–100)
- Retime: RetimeProcess (0=Nearest, 1=Frame Blend, 2=Optical Flow, 3=Speed Warp)

### Color Node Operations
- Add node: timeline_item_node → add_node
- Apply LUT: set_lut(nodeIndex, lutPath)
- Apply CDL: set_cdl({NodeIndex, Slope, Offset, Power, Saturation})
- Versions: add_version / load_version / delete_version

### Render Pipeline
```
render → set_render_settings → {FormatWidth, FormatHeight, ...}
render → add_render_job
render → start_rendering
```

## Key Gotchas
- **Timeline items need explicit fetch:** Must call `project -> get_current_timeline` then `timeline -> get_items_in_track` before any clip/track operations
- **Absolute paths only:** LUTs, media imports, and exports require absolute paths — relative paths fail silently
- **Alpha codecs:** ProRes 4444/4444XQ, DNxHR 444, Uncompressed, or EXR only
- **Composite modes:** Integer values 0-29 (e.g., 8=Overlay, 17=Multiply), not named strings
- **Render pipeline:** Load preset via `render_preset -> load` before adding render jobs

## Resolve 20+ AI Features (Studio Only)
Scriptable via MCP:
- **Magic Mask v2** — `timeline_item_color → create_magic_mask(mode)` where mode is "F" (forward), "B" (backward), or "BI" (bidirectional)
- **SuperScale Enhanced** — `set_property("SuperScale", 2)` for 2x, or 3/4
- **Speed Warp** — `set_property("RetimeProcess", 3)` for highest-quality retiming

UI-only (not scriptable):
- IntelliScript — auto-assembles timeline from script + transcript sync
- IntelliCut — removes silences, checkerboards dialogue by speaker
- Multicam SmartSwitch — auto-selects camera angles by speaker
- Audio Assistant — generates professional mix automatically
- Voice Convert — AI voice model transformation

## Common Errors
- "No connection to Resolve" → Studio required + scripting set to Local
- "Timeline item not found" → Must get valid item refs from timeline first
- "LUT path not found" → Use absolute paths only
