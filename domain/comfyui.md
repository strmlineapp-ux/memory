---
name: ComfyUI 0.20.1 — API Format, Node Gotchas, Installed Models
description: ComfyUI macOS app (0.20.1): API format rules, subgraph expansion pattern, node gotchas (TextGenerate, ComfySwitchNode), installed models, MCP tools
type: domain
---

# ComfyUI Knowledge

## Environment
- **Version**: ComfyUI 0.20.1 via macOS app
- **Server**: `localhost:8100` (port 8100)
- **Models directory**: `~/Documents/ComfyUI/models/` (subfolders: checkpoints, diffusion_models, text_encoders, vae, loras, etc.)
- **Output directory**: `~/Documents/ComfyUI/output/`
- **Workflows directory**: `~/Documents/ComfyUI/user/default/workflows/`

## API Format Rules
- **Envelope**: `{"prompt": {node_id: {class_type, inputs}}, "client_id": "claude-code-comfy"}`
- **Connection references**: `["node_id", output_slot]` (string node ID + int slot)
- **Widget values**: Direct values for primitives/ints/floats/bools, NOT connection refs
- **Node IDs**: Must be strings in API format (e.g., `"66"`, not `66`)

## Subgraph Workflows
- Stored as JSON with `"definitions": {"subgraphs": [...]}` containing internal nodes, links, proxy widgets
- **Subgraph wrapper node** (UUID type like `03921aea-a70e-44b4-bc77-f6bda10f2120`) must be **omitted** when expanding to API format
- Map proxy widgets from wrapper → internal nodes via `"proxyWidgets"` array: `[["node_id", "widget_name"], ...]`
- Internal connections use link IDs — resolve to `["target_id", target_slot]` in API
- Widget values on wrapper propagate to internal nodes (e.g., seed, steps, cfg)

## Common Node Gotchas
- **TextGenerate**: Required inputs — `clip`, `prompt`, `max_length` (INT), `sampling_mode` (COMBO). Optional nested params: `sampling_mode.temperature`, `sampling_mode.top_k`, etc.
- **ComfySwitchNode**: Required — `on_false`, `on_true`, `switch`. Output type depends on inputs.
- **EmptyFlux2LatentImage**: Required — `width`, `height`, `batch_size`
- **KSampler seed**: Use fixed integer. `[0, "randomize"]` widget format causes validation errors in 0.20.1
- **CLIPTextEncode**: `clip` (connection) + `text` (string or connection). For empty negative conditioning, use text: ""
- **StringReplace**: `string`, `find`, `replace` — all STRING type. Can chain for placeholder substitution

## Installed Models
- **diffusion_models**: `ernie-image.safetensors` (ERNIE Image), z-turbo checkpoint
- **text_encoders**: `ministral-3-3b.safetensors`, `ernie-image-prompt-enhancer.safetensors`
- **vae**: `flux2-vae.safetensors`

## Workflow Patterns
- **Prompt enhancement chain**: PrimitiveStringMultiline (template) → StringReplace {prompt} → StringReplace {width} → StringReplace {height} → TextGenerate
- **Switch routing**: ComfySwitchNode selects between enhanced prompt (on_true) and original/empty (on_false) based on PrimitiveBoolean
- **Negative conditioning**: Empty CLIPTextEncode with text: "" is sufficient for most models

## MCP Tools Available
- `analyze_workflow` — structured analysis of saved workflows
- `get_node_info` — query node inputs/outputs from object_info
- `enqueue_workflow` / `get_job_status` / `get_history` — job lifecycle
- `list_local_models` / `download_model` — model management
- `get_logs` / `clear_vram` — debugging/maintenance

## Common Error Patterns
- **HTTP 400 + `missing_node_type`**: Subgraph wrapper UUID sent in API — omit it
- **HTTP 400 + `required_input_missing`**: Missing required input on a node (check get_node_info)
- **HTTP 400 + `dependent_outputs`**: Fix the root node error, downstream nodes cascade
