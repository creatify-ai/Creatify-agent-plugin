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

Pick the workflow that matches the request and load the matching skill; read the
tool schema for exact args.

- **Talking-avatar video (Aurora)** — an avatar or a face image + a spoken script
  (+ an optional voice) → a talking video. `aurora_generate`, poll `aurora_get`.
  This is the tool for "make an avatar say this". See `avatar-video`.
- **Custom avatars** — build a reusable avatar identity from your own talking
  video (BYOA, needs a consent clip), one face photo, or a text description
  (`avatar_create_video` / `avatar_create_image` / `avatar_create_text` +
  `avatar_choose_photo`); poll `avatar_create_get`; ready avatars appear in
  `avatar_list` and can then be used in Aurora. See `avatar-create`.
- **Voice clone + voices** — clone a voice from an audio sample (`voice_clone` →
  `voice_clone_get`), browse voices with `voice_list`; use them in Aurora. See
  `voice-clone`.
- **Image / video asset generation** — ~90 models. `asset_list_models` →
  `asset_get_model_schema` → `asset_generate` → `asset_get`. See `asset-generation`.
- **Creative agent (ad creatives)** — full brief-to-ad flow: `creative_create_session`
  / `creative_send_turn` / `creative_get_session` / `creative_stop_turn`. See
  `creative-agent`.
- **Products / brands** — target a brand space and set up products before
  product-specific creative work (`product_*` / `brand_*`). See `product-and-brand`.
- **Media upload** — `upload_file` for media the agent holds. See `media-upload`.

Two cross-cutting skills apply throughout: `verification` (confirm output before
reporting done) and `known-errors` (recover from common failures).

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

## Browser Handoff

Some tool results include a `browser_handoff` object:

```json
{ "required": true, "url": "<authenticated short-lived URL>", "page_url": "<clean page URL>" }
```

When a tool result includes `browser_handoff`:

- Open `browser_handoff.url` in the host's in-app browser via the host's
  browser-control capability. In Codex hosts with `tool_search`, discover it with
  `browser:control-in-app-browser`, `Control In App Browser`, or `node_repl js`; if the
  browser skill requires `node_repl js`, use that tool to initialize the Browser runtime
  and select the `iab` browser.
- If a tab for the same page is already open, reuse/focus it instead of opening a
  duplicate.
- Treat a reported "opened/focused" as handoff complete; do not wait for the page to
  fully load or keep polling the browser.
- The handoff page is meant to be watched by the user, and the in-app browser
  keeps its work in the background by default. After opening
  `browser_handoff.url`, make the browser visible and keep it visible while the
  session works — use the Browser skill's visibility capability, e.g.
  `await (await iab.capabilities.get("visibility")).set(true)`. Opening the tab
  without surfacing it defeats the handoff.
- `url` embeds a short-lived login token. NEVER show `url` to the user; any user-facing
  link must use `page_url`.
- If the tab is missing or the page shows "Invalid magic link" (expired token), re-run
  the relevant read tool (e.g. `creative_get_session`) — its result carries a fresh
  `browser_handoff` — or fall back to presenting `page_url` as a Markdown link.
- If no browser-control capability is available in this host, present `page_url` as a
  Markdown link and continue; state which step was unavailable.
- Billing/pricing URLs are not handoffs; open them via the external browser.
