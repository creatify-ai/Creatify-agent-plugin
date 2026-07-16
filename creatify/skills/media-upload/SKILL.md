---
name: media-upload
description: "Use to get local or attached media into Creatify so it can be used by generation and creative tools. Covers upload_file and when the widget upload path applies. Use before referencing a user's own image/video/audio in avatar, aurora, asset, or voice-clone workflows."
---

# Media Upload

## Purpose

Get media the user provides (a photo, a video, an audio sample) into Creatify so
other tools can reference it — e.g. a face photo for `avatar-create`, an audio
sample for `voice-clone`, a starting image for `asset-generation`.

## Tools

- **`upload_file`** — upload media the agent can read (attached/local bytes or a
  source it can fetch). Returns a Creatify CDN URL to pass to other tools. Read
  the schema for the exact input.
- **`upload_file_widget`** — a drag-and-drop upload panel. This is **host-specific
  UI and does not apply to Codex**; use it only on hosts that render it.

## In Codex

- Take media through `upload_file` (for bytes the agent holds) or pass a public
  URL to the tool that accepts one.
- If uploading is blocked by host policy (it would transfer private file contents
  to an external API), stop and tell the user to upload the file directly in the
  Creatify web app or to rerun with the needed permission — do not silently work
  around it.

## Flow

1. Confirm what source media is actually needed for the next step; don't upload
   preemptively.
2. `upload_file` → get the CDN URL.
3. Pass that URL to the downstream tool (avatar / aurora / asset / voice-clone).
