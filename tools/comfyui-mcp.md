---
name: ComfyUI MCP Server (artokun/comfyui-mcp) — Local Instance
description: TypeScript-based local MCP server (artokun/comfyui-mcp) connecting to a running ComfyUI instance. Key tools: enqueue_workflow, get_job_status, cancel_job, list_workflows, visualize_workflow (Mermaid), convert_workflow.
type: reference
---

# ComfyUI MCP Server — `artokun/comfyui-mcp` (Local)

## Setup
- **Repo**: `/Users/bernardoresende/Documents/MCP Tools/comfyui-mcp` (TypeScript)
- **Entry**: `npx -y comfyui-mcp` → stdio transport (`StdioServerTransport`)
- **Plugin config**: `plugin/.mcp.json` — command is `"npx", "-y", "comfyui-mcp"`
- **Server name**: `comfyui-mcp` (v0.1.0)
- **Connects to**: Local ComfyUI instance via REST API + WebSocket

## Plugin File Sync
- **Cache**: `~/.claude/plugins/cache/comfyui-mcp/comfy/0.1.0/`
- **Marketplace**: `~/.claude/plugins/marketplaces/comfyui-mcp/plugin/`
- After code changes: copy to both locations, then restart Claude Code or `/mcp`.

## Development
- `npm run build` → dist/ output (used by npm link)
- Developer uses `npm link` so local build is used

## Key Files in Repo (`/Users/bernardoresende/Documents/MCP Tools/comfyui-mcp`)
- **Repo path**: `/Users/bernardoresende/Documents/MCP Tools/comfyui-mcp`
- `src/index.ts` — main entry, registers tools via `registerAllTools()`
- **Execute**: `src/tools/workflow-execute.ts` — enqueue_workflow, get_system_stats
- **Library**: `src/tools/workflow-library.ts` — list_workflows, get_workflow (UI↔API), save_workflow
- **Manipulate**: `src/tools/workflow-manipulate.ts` — create_workflow, modify_workflow (incl. upscale_inpaint)
- **Visualize**: `src/tools/workflow-explorer.ts` — visualize_workflow (Mermaid), convert_workflow, search_models
- **Validate**: `src/tools/workflow-validate.ts` — validate workflow before execution
- **Compose**: `src/tools/workflow-compose.ts` — compose workflows from components

## Services
- **Executor**: `src/services/workflow-executor.ts` — enqueueWorkflow() with seed randomization
- **Converter**: `src/services/workflow-converter.ts` — UI→API conversion with subgraph expansion
- **Validator**: `src/services/workflow-validator.ts` — validates workflow structure/inputs
- **Composer**: `src/services/workflow-composer.ts` — composes workflows from parts
- **Settings extractor**: `src/services/workflow-settings-extractor.ts` — extracts generation settings for tracking
- **Job watcher**: `src/services/job-watcher.ts` — background completion detection via WebSocket

## Available Tools
- **Execution**: `enqueue_workflow`, `get_job_status`, `cancel_job`
- **Library**: `list_workflows`, `get_workflow`, `save_workflow`, `analyze_workflow`
- **Visualization**: `visualize_workflow` (Mermaid), `convert_workflow` (UI→API)
- **Manipulation**: `create_workflow`, `modify_workflow`, `upscale_inpaint`
- **Validation**: validate workflow before enqueueing

## Workflow API Format (what enqueue_workflow expects)
```json
{
  "node_id": {
    "class_type": "...",
    "inputs": { ... }
  },
  "_meta": { "title": "...", "mode": "api" }
}
```

Connections are references: `[nodeId, outputIndex]` (e.g., `["3", 0]`).

## How enqueue_workflow Works
1. **Input**: Accepts either UI format (`{nodes, links}`) or API format
2. **Seed randomization**: By default walks all nodes and replaces `seed`/`noise_seed` inputs with 32-bit random values. Pass `disable_random_seed: true` to keep seeds as-is
3. **UI→API conversion** (if input is UI format): runs `convertUiToApi()` which:
   - Calls `expandComponents()` to recursively flatten UUID-typed subgraph nodes into the outer workflow
   - Remaps all link IDs to avoid collisions during expansion (up to depth 10)
   - Rewires external connections: outer links pointing into a subgraph get re-wired to point at the expanded inner nodes
   - Maps `widgets_values` arrays → named widget inputs (handles phantom widgets for control_after_generate)
   - Skips internal litegraph types: `Reroute`, `Note`, `PrimitiveNode`, `MarkdownNote`
   - Handles special Get/Set nodes (not in object_info but important for data flow)
4. **Enqueue**: POSTs to `POST /internal/enqueue` on local ComfyUI instance
5. **Output**: Returns `{prompt_id, queue_remaining}` immediately (fire-and-forget)
6. **Background**: `JobWatcher.watch(prompt_id)` starts background completion detection via WebSocket

## Key Workflow Concepts
- **Node IDs are strings** (can be numeric or UUID for subgraphs) in API format
- **Subgraph nodes**: Have `class_type` like `"03921aea-a70e-44b4-bc77-f6bda10f2120"` (UUID)
- Subgraph nodes have `definitions.subgraphs[]` with nested `{nodes, links}` arrays
- The converter expands subgraph nodes in-place before enqueueing

## Important: `convert_workflow` vs auto-conversion
- **Do NOT** call `convert_workflow` separately before `enqueue_workflow`. The MCP server handles conversion internally.
- Only call `convert_workflow` if you specifically need the API-format output as a file/result.
- Calling it separately is redundant and can introduce errors (the converter may warn about nodes not in object_info).

## How to Discover Available Node Types
- Query the local ComfyUI instance at `GET /object_info` to see all installed node types and their input/output specs
- Each entry shows required/optional inputs, widget types (INT, FLOAT, STRING, COMBO), etc.

## Official ComfyUI Documentation
- Cloud MCP Server (different — hosted service): https://docs.comfy.org/development/cloud/mcp-server
- Cloud API Reference: https://docs.comfy.org/development/cloud/api-reference
