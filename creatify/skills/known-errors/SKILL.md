---
name: known-errors
description: "Use when a Creatify tool call fails or returns an error — auth/access problems, wrong brand space, quota/plan gating, not-found ids, long-running jobs, or missing source media. Covers how to recover for each without blind retries or repo debugging."
---

# Known Errors & Recovery

When a Creatify tool call errors, match it to one of these and recover
accordingly. Do not blindly retry, and do not start debugging the repo or local
services — these are access/state/quota conditions, not code bugs.

## Auth / access

- Symptom: unauthorized, or a tool says it needs authentication.
- Recover: run `codex mcp login creatify` and retry. Confirm the account is the
  one that owns the target brand/product.

## Wrong brand space

- Symptom: a product/brand/folder "not found" even though the user says it exists.
- Recover: the data likely lives in another brand space. `brand_list`, then
  `brand_switch` to the right one, and retry with the correct id. See
  `product-and-brand`.

## Quota / plan gating

- Symptom: voice-slot full or a creation-time cap (voice cloning); a model
  rejected by `plan_level` (asset generation).
- Recover: surface the limit to the user (they may need to free a slot or upgrade)
  instead of retrying. Offer a lower-tier model when one fits.

## Not-found / bad id

- Symptom: an id-scoped call fails.
- Recover: use ids returned by list/get tools (`avatar_list`, `voice_list`,
  `product_list`, `asset_list_models`, session results); never guess ids.

## Long-running / "still processing"

- Symptom: a `*_get` poll isn't done yet.
- Recover: keep polling the matching status tool; a not-yet-done state is not a
  failure. Only conclude failure on a terminal failed state. See `verification`.

## Missing source media

- Symptom: a workflow needs a photo/video/audio the agent doesn't have.
- Recover: check whether it already exists (e.g. in the brand/product or project)
  before asking. If genuinely missing, get it in via `media-upload` or ask the
  user to provide it. If a host policy blocks the upload, tell the user to upload
  directly in the Creatify web app.
