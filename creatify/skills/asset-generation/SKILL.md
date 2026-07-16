---
name: asset-generation
description: "Use to generate images or videos with Creatify's model catalog (~90 image/video models: Veo, Kling, Seedance, Nano Banana, Flux, GPT-image, etc.). Covers model discovery (asset_list_models, asset_get_model_schema), asset_generate, asset_get, and picking the right model and task variant (text-to-video, image-to-video, reference-to-video, edit)."
---

# Image / Video Asset Generation

## Purpose

Generate standalone image or video assets from Creatify's model catalog — b-roll,
product shots, cinematic clips, edited/upscaled images, etc. For a talking avatar
use `avatar-video` (Aurora) instead; this skill is for general generation models.

## Mandatory discovery flow — never guess params

1. **`asset_list_models`** — browse the catalog (~90 models). Filter with
   `media_type` (`image` | `video`) and/or a `query` substring. Each row has
   `model_name`, `media_type`, `title`, `description`, `plan_level`.
2. **`asset_get_model_schema(model_name)`** — get that model's `input_params`.
   **Do NOT guess input_params** — they differ per model.
3. **`asset_generate`** — start the generation with the model + validated params.
4. **`asset_get`** — poll until the asset is ready, then return its URL.

## Picking the model

- Match `media_type` and the **task variant encoded in the model name suffix** to
  what the user has:
  - `/text-to-video`, `/text-to-image` — prompt only.
  - `/image-to-video`, `/image-to-image` — a starting image.
  - `/reference-to-video` — one or more reference images for subject/style.
  - `/first-last-frame-to-video` — a start and end frame.
  - `/edit`, `/video-to-video/edit` — modify an existing image/video.
  - `/extend-video` — lengthen an existing clip.
- Respect `plan_level` (`free` / `starter` / `pro`): if the user's plan can't use a
  model it will be gated — surface that instead of failing silently.
- The catalog changes over time. Always discover at runtime with
  `asset_list_models`; do not rely on a hardcoded model list.

## Align Before Generating

Generation is paid and time-consuming. Confirm the load-bearing choices before
generating: what to generate, image vs video, aspect ratio/duration, and (if it
matters) which model tier. Establish one sample before batching many.

## Async & Verify

`asset_generate` is long-running. Poll `asset_get`; only report success once it
returns a finished asset URL. See `verification` and `known-errors`.
