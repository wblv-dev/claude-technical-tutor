---
description: End the session-log session — synthesize scratch into a final note, then delete the scratch.
---

Wrap up the current session-log session.

Resolve the notes directory: use `$TUTOR_NOTES_DIR` if set, otherwise default to `~/tutor-notes/`.

1. Read `<notes-dir>/.scratch-active.md`
2. If missing or empty, tell the learner there's no active session and stop.
3. Synthesize entries into a final note following the synthesis structure in the `session-log` skill.
4. Infer a topic from the entries (2-5 words, Title Case).
5. Propose the filename `session_log_YYMMDD_HHMM.md` (using the session start time from the scratch `created:` field) to the learner. Wait for confirmation or a rename.
6. On confirmation, write to `<notes-dir>/<filename>` and delete the scratch.
7. One-line confirmation: `[session ended: <filename>, N entries, review potential: <brief>]`
