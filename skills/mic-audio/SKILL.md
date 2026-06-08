---
name: mic-audio
description: "Use when the user asks to record from the microphone or play audio out of the speaker on this device — e.g. 'record this', 'record a 10 second clip', 'take a voice note', 'play that back', 'play this file', 'replay the last recording'. Captures the mic and plays audio through the device's speaker."
version: 1.0.0
author: Iva / Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [smart-home, audio, microphone, record, playback, voice]
    related_skills: [volume-control]
---

# Mic & Audio Playback (this device)

## Overview

Records from this device's microphone and plays audio out of its speaker through
the `iva-audio` helper. The helper records the **FL channel** of the XVF3800 mic
array (the only channel that hears the user clearly — a plain capture downmixes
all six channels and dilutes the voice ~5×) and plays through the same audio
path the voice replies use. Always go through `iva-audio` — do **not** call
`arecord`/`aplay`/`ffplay` directly, because those use the wrong channel/device.

## When to Use

- User asks to record something, take a voice note/memo, capture a clip, or
  record for N seconds.
- User asks to play an audio file, or to play back / replay what was just
  recorded.
- Don't use for: changing the volume (use `volume-control`), or the normal
  wake-word conversation flow (that's the voice daemon, not this skill).

## How To

Run the helper with the terminal tool, then tell the user the result in one
short spoken sentence.

| User intent | Command |
|---|---|
| record until they stop talking | `/home/iva/.local/bin/iva-audio record` |
| record a fixed length (e.g. 5s) | `/home/iva/.local/bin/iva-audio record 5` |
| record to a specific file | `/home/iva/.local/bin/iva-audio record 5 /tmp/note.wav` |
| play an audio file (wav/mp3/…) | `/home/iva/.local/bin/iva-audio play /path/to/file` |
| play back / replay the last recording | `/home/iva/.local/bin/iva-audio play-last` |

- `record` first speaks a short cue ("I'll start recording after the beep, for N
  seconds"), then beeps, then captures — recording begins cleanly *after* the
  beep, so nothing is lost and the user knows when to speak.
- `record` with no seconds waits for speech to start, then stops after ~1.5s of
  silence (times out if no one speaks). `record N` captures exactly N seconds.
- Recordings auto-save under `~/.local/share/iva-voice/recordings/rec_<ts>.wav`
  unless a file path is given. `play-last` replays the most recent one.
- `play`/`play-last` **block until playback finishes**, so the command returning
  means the audio has fully played.
- It prints `recorded <path> (N.Ns)` or `played <path>`.

## Response Style (voice)

Keep the spoken reply to one short line stating the result — e.g.
"Recorded a five second clip." or "Okay, playing it back." Don't read the file
path or the raw command output aloud.

## Common Pitfalls

1. Using `arecord`/`aplay`/`ffplay` directly — they capture the wrong (downmixed)
   channel or play to the wrong device. Always use `iva-audio`.
2. Forgetting `play`/`play-last` block until done — don't say "playing" and then
   immediately start another action expecting overlap; it plays to completion.
3. Reading the literal `recorded <path>` output aloud — paraphrase it.

## Verification Checklist

- [ ] Ran `iva-audio <record|play|play-last>` via the terminal tool (not raw
      arecord/aplay).
- [ ] Confirmed the result back to the user in one short sentence.
