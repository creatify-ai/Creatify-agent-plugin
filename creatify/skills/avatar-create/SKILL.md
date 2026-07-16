---
name: avatar-create
description: "Use to build a reusable custom avatar identity in Creatify from the user's own media — a talking video (bring-your-own-avatar, needs a consent clip), a single face photo, or a text description. Covers avatar_create_video / avatar_create_image / avatar_create_text, avatar_choose_photo, avatar_create_get, and the upload widget. This makes an avatar IDENTITY, not a talking video."
---

# Create a Custom Avatar

## Purpose

Build a **reusable avatar identity** the user can then use to render talking
videos. This is distinct from making a talking video: once the avatar is ready it
appears in `avatar_list`, and you generate speech with it via `avatar-video`
(Aurora).

## Three ways to create

- **From a talking video (BYOA)** — `avatar_create_video`. Needs the footage, a
  **consent clip**, and a name. If the consent clip or name is missing, open the
  video wizard with `avatar_create_video_widget` (two upload slots: footage +
  consent) and let the user provide them. Consent is required — do not bypass it.
- **From one face photo** — `avatar_create_image`. A reusable avatar from a single
  face photo (~5–10 min).
- **From a text description (synthetic)** — `avatar_create_text` generates
  candidate faces; then submit the chosen candidate with `avatar_choose_photo` to
  build the avatar.

Read each tool's schema for exact params before calling.

## Flow

1. Pick the tier that matches what the user has (video / photo / text).
2. Start creation; for the video tier, ensure footage + consent + name (widget if
   missing).
3. Poll `avatar_create_get` until done. The `avatar_id` populates when ready and
   the avatar also shows up in `avatar_list`.
4. If the user's goal was a talking video, hand off to `avatar-video` (Aurora)
   using the new `avatar_id`.

## Async & Verify

Creation is long-running. Poll `avatar_create_get`; only report success once an
`avatar_id` is returned. See `verification` and `known-errors`.
