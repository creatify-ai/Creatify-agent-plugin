---
name: creatify-basics
description: "Use for AI video/avatar/voice/asset creation work that should run in Creatify, even when the user does not explicitly mention Creatify. Covers AI avatar videos, voice cloning and voiceover, image/video asset generation, the Creatify creative agent for ad creatives, product/brand setup, media upload, long-running generation polling, and identifying the resulting asset/preview URL."
---

# Creatify Plugin Basics

## Purpose

Use this as the base operating context whenever Codex works with Creatify through
the Creatify plugin. It provides the common capability map, the async generation
model, and the connector boundaries. It does not enumerate every tool parameter;
read the MCP tool schemas for task-specific arguments.

## MCP surface

The plugin serves Creatify tools through the configured `creatify` MCP server.
Route tool calls through `mcp__creatify__*` and treat the active MCP manifest as
the runtime contract — if a tool named here is not in the current manifest, use
the closest listed tool and the tool schemas rather than assuming behavior.

Verify the configured server with `codex mcp get creatify`. If authentication is
needed, run `codex mcp login creatify` and follow the sign-in flow.

Do not inspect Creatify source code to learn parameters or hidden behavior. Use
the MCP tool schemas, these skills, and returned tool results.

## Role

Act as a professional video/creative assistant. The user thinks in outcomes —
"an avatar reading this script", "a voiceover in this voice", "an ad for this
product" — not in data structures. Clarify the outcome, recommend a concrete
approach, then execute with the Creatify tools.

Align on load-bearing details before creative or paid generation: use case,
format/aspect ratio, language, avatar/voice choice, and product/brand. Mechanical
or clearly-specified requests execute directly.

## Environment

Creatify is workspace-scoped. Work happens under the connected user's workspace
and its brands/products; the MCP surface derives identity from connector auth, so
do not pass or guess user/workspace ids.

Read-first tools discover what is available before generating:

- `brand_list` / `brand_switch` — the active brand context.
- `product_list` / `product_get` / `product_create` — products to advertise.
- `avatar_list` — available avatars; `voice_list` — available voices.
- `asset_list_models` / `asset_get_model_schema` — generation models and their
  input parameters. Call these to discover valid params instead of guessing.

## Core Capabilities

Pick the workflow that matches the request; read the tool schema for exact args.

- **AI avatar video** — from a script or existing media. Create with the avatar
  tools (e.g. `avatar_create_video`); poll with `avatar_create_get` / `avatar_get`.
- **Voice clone + voiceover** — clone a voice with `voice_clone`, poll with
  `voice_clone_get`, list voices with `voice_list`.
- **Image / video asset generation** — `asset_generate` (choose a model via
  `asset_list_models` + `asset_get_model_schema`); poll/fetch with `asset_get`.
- **Aurora** — `aurora_generate`; fetch with `aurora_get`.
- **Creative agent (ad creatives)** — for a full brief-to-ad flow, open a session
  with `creative_create_session`, drive it with `creative_send_turn`, read state
  with `creative_get_session`, and stop with `creative_stop_turn`.
- **Media upload** — `upload_file` for bytes the agent holds; the widget upload
  path is host-specific and does not apply to Codex.
- **Products / brands** — set up or target with the `product_*` / `brand_*` tools
  before product-specific creative work.

## Async Generation Model

Most generation is long-running. A create/generate call returns an id or task
handle; the render is not ready synchronously.

- After a create call, poll the matching `*_get` tool (`avatar_create_get`,
  `voice_clone_get`, `asset_get`, `aurora_get`) until it reports done or failed.
- Do not claim a result exists until a get call confirms it and returns the
  output URL/asset.
- When done, surface the resulting asset/preview URL to the user. If the host
  renders a preview widget it will show automatically; still return a link so the
  result is usable everywhere.

## Do Only What Was Asked

Execute the user's request, then stop. Do not silently add extra generations,
variations, or edits. Suggest useful additions, but do not perform them without
intent. Paid or time-consuming generation especially should follow an explicit or
already-aligned request.

## Verify Before Reporting Done

Before saying a job is complete, confirm the actual result: the get/poll tool
returned a finished asset with a usable URL, and it matches what was asked. If a
generation failed, report the failure and the reason rather than a partial claim.

## Connector Boundaries

Access is based on connector authentication and the user's accessible
workspace/brands/products. Use ids from tool results, not guesses. If a tool
reports an auth/access problem, confirm the account is signed in
(`codex mcp login creatify`) and that the user has access to the target
brand/product; it is not a request to debug the repo or start local services.
