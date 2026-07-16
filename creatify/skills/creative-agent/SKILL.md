---
name: creative-agent
description: "Use for a full brief-to-ad creative flow in Creatify — hand a creative brief to the Creatify creative agent and let it plan and produce a multi-scene ad, rather than assembling assets tool-by-tool. Covers creative_create_session, creative_send_turn, creative_get_session, creative_stop_turn, and the turn/polling model."
---

# Creative Agent (brief → ad)

## Purpose

The creative agent takes a **creative brief** and orchestrates a full ad on its
own (scenes, script, visuals, voice). Use it when the user wants a finished ad
from a prompt/brief, not when they want a single specific asset.

Use this instead of hand-assembling when the ask is "make me an ad for X". Use
the direct tools (`avatar-video`, `asset-generation`, `voice-clone`) when the ask
is one specific artifact.

## Tools

- **`creative_create_session`** — open a session with the user's opening prompt /
  creative brief. Returns a session handle.
- **`creative_send_turn`** — send a follow-up instruction to iterate on the
  session.
- **`creative_get_session`** — read session state and the current headline
  output. Poll this to watch progress and to fetch results.
- **`creative_stop_turn`** — stop an in-progress turn.

Read each tool's schema for exact params.

## Flow

1. `creative_create_session` with the brief (align on product, use case, format
   first if the brief is vague — see `product-and-brand`).
2. Poll `creative_get_session` to follow progress and read outputs.
3. Iterate with `creative_send_turn` for revisions; `creative_stop_turn` to
   cancel a running turn.
4. Surface the session's output (and its live URL if returned).

## Async

Turns run asynchronously. After `create_session` / `send_turn`, poll
`creative_get_session` for progress and results; don't assume a turn finished
without reading session state. See `verification`.
