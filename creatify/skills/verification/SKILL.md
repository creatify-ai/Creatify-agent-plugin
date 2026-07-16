---
name: verification
description: "Use before telling the user a Creatify job is done. Covers how to confirm a long-running generation actually finished with a usable result, using the matching *_get poll tool, and how to report failures honestly instead of claiming false success."
---

# Verification

Creatify generation is asynchronous. A create/generate call returning an id means
the job **started**, not that it succeeded. Never report success from the create
call alone.

## Confirm before claiming done

1. Poll the matching status tool until it reports a terminal state:
   - Aurora video → `aurora_get`
   - Custom avatar → `avatar_create_get`
   - Voice clone → `voice_clone_get`
   - Asset (image/video) → `asset_get`
   - Creative agent → `creative_get_session`
2. Confirm the terminal state is **done/success** and that it returns a usable
   output (a video/image/audio URL, a ready `avatar_id`, a session output).
3. Confirm the result matches what was asked (right subject, right script, right
   media type/format).

## Report honestly

- If the job failed, report that it failed and the reason — do not present a
  partial or fabricated result as success.
- If it's still running after reasonable polling, say it's still processing and
  keep polling or hand back a way to check; don't guess the outcome.
- Return the actual output URL so the result is usable, even when a host also
  renders an inline preview.

Do only what was asked: verify the specific requested output, don't silently add
extra generations to "prove" it.
