---
description: Retroactively generate session-log entries from the current session's JSONL transcript. Safety net for when /session-start was forgotten.
context: fork
---

Recover session-log entries from the current session transcript.

This is the safety net for when the learner forgot to run `/session-start` but did meaningful work worth capturing.

Resolve the notes directory: use `$TUTOR_NOTES_DIR` if set, otherwise default to `~/tutor-notes/`.

1. Check if `<notes-dir>/.scratch-active.md` already exists.
   - If present → tell the learner a session is already active. Ask: add recovered entries to it, or skip?

2. Find the current session's JSONL file at `~/.claude/projects/<project-dir>/<session-id>.jsonl`.
   - The project dir matches the current working directory (path-encoded with dashes).
   - If arguments are provided ($ARGUMENTS), treat them as a session ID or hint.
   - If no JSONL found, tell the learner and stop.

3. Read the JSONL transcript. Extract user and assistant messages (type "user" and "assistant"). Skip system messages, attachments, and tool metadata.

4. Scan the transcript for noteworthy moments using the `session-log` skill's rubric and signal levels. Apply the same filters — skip routine tool calls, typo fixes, dead-ends that taught nothing. Identify both T/R/C (build work) and Q/A/R (teaching exchanges) entries.

5. Create `<notes-dir>/.scratch-active.md` with the standard header (use the session's first timestamp as `created:`).

6. Write entries for each noteworthy moment found, with signal levels and timestamps from the JSONL. Preserve the learner's actual words in Q/A/R entries where possible from the transcript.

7. Report: `[recovered: N entries from session, scratch created. Run /session-end to synthesize.]`

**Important:**
- Apply the same "never capture secrets" rule — redact any tokens/keys/credentials found in the transcript.
- Be conservative — only capture moments that clearly meet the noteworthy threshold.
- This is a recovery tool, not a replacement for `/session-start`. The output quality will be lower than real-time capture because context is flattened in the transcript.
