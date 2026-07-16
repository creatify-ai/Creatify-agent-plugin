---
name: voice-clone
description: "Use to clone a voice from an audio sample in Creatify and to browse available voices. Covers voice_clone, voice_clone_get, voice_list, and the voice-clone widget. Cloned and catalog voices are what Aurora talking-avatar videos speak with."
---

# Voice Clone & Voices

## Purpose

Clone a voice from an audio sample, and browse the voices available to the
workspace. Both cloned and catalog voices are consumed by `aurora_generate`
(see `avatar-video`).

## Tools

- **`voice_list`** — browse voices usable with Aurora. Returns id, name, gender,
  preview_url, is_cloned. Use it to pick an existing voice before cloning.
- **`voice_clone`** — clone a voice from an audio sample. Returns
  `{clone_id, status}`. Read the schema for the audio-sample input.
- **`voice_clone_get`** — poll a clone job; the voice/accent id populates once
  `done`.
- **`voice_clone_widget`** — opens a record/upload panel; host-specific and does
  not apply to Codex. In Codex, take the audio sample through the normal upload
  path (see `media-upload`) or a provided URL, then call `voice_clone`.

## Flow

1. If the user just wants an existing voice, `voice_list` and pick one — no clone
   needed.
2. To clone: obtain the audio sample, call `voice_clone`, then poll
   `voice_clone_get` until done.
3. Use the resulting voice (or `clone_id`) in `avatar-video` / `aurora_generate`.

## Notes

- Cloning consumes a workspace voice slot (in-app quota gates). If the slot is
  full or a creation-time cap is hit, surface that to the user rather than
  retrying blindly (see `known-errors`).
- Don't clone without an explicit request — it's a stateful, quota-bound action.

## Async & Verify

Poll `voice_clone_get`; only report success once the clone is `done` with a
usable voice. See `verification`.
