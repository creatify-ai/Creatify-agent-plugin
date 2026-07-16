---
name: avatar-video
description: "Use to generate a talking-avatar video in Creatify (Aurora) — make an avatar or a face image speak a script, optionally with a cloned or catalog voice. Covers aurora_generate, aurora_get, choosing an avatar and voice, and script/format alignment. Use this for 'make an avatar say X' / 'talking head from this photo + script'."
---

# Talking-Avatar Video (Aurora)

## Purpose

Aurora turns **(an avatar OR a face image) + a spoken script (+ an optional
voice)** into a talking-avatar video. This is the tool for "make an avatar say
this", spokesperson clips, UGC-style talking heads, and explainer presenters.

This is NOT the tool for:
- general b-roll / cinematic / product clips → use `asset-generation`;
- building a reusable avatar identity → use `avatar-create`.

## Inputs

- **A subject**: an existing `avatar_id` (from `avatar_list`, or one you built via
  `avatar-create`) OR a face image.
- **A script**: the text to speak.
- **A voice** (optional): pick one from `voice_list`, or a `clone_id` from
  `voice-clone`. Voices listed by `voice_list` are the ones usable here.

Read the `aurora_generate` schema for the exact parameters (aspect ratio,
subject/image field, voice field, etc.). Do not guess params.

## Flow

1. Resolve the subject: choose an avatar (`avatar_list`) or confirm the face
   image. If the user wants a brand-new avatar identity first, go through
   `avatar-create`, wait until it appears in `avatar_list`, then return here.
2. Resolve the voice with `voice_list` (or clone one via `voice-clone`).
3. Call `aurora_generate` with the subject + script + voice.
4. Poll `aurora_get` until it reports done (or failed).
5. Surface the finished video URL.

## Align Before Generating

Aurora renders are paid and take time. Before generating, confirm the
load-bearing choices: the exact script text, which avatar, which voice, and the
aspect ratio / format. If any is a real fork (e.g. voice choice, vertical vs
landscape), align first rather than guessing.

## Async & Verify

`aurora_generate` starts a render; it is not ready synchronously. Poll
`aurora_get` and only report success once it returns a finished video URL. On
failure, report the reason. See `verification` and `known-errors`.
