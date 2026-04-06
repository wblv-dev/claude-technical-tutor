---
description: Capture the current moment as a Task/Reason/Challenge entry in the active build log scratch.
---

Manually capture the current conversation context as a build-log entry.

Resolve the notes directory: use `$TUTOR_NOTES_DIR` if set, otherwise default to `~/tutor-notes/`.

If arguments are provided ($ARGUMENTS), use them as a hint for the title or topic.

1. Check if `<notes-dir>/.scratch-active.md` exists.
   - If missing → tell the learner: "No active session. Run `/project-start` first." Stop.
2. Determine entry type (bug / build-guide / adr)
3. Assign a signal level (high / medium / low)
4. Write a T/R/C entry based on what we just did, in the learner's voice — terse, specific
5. Confirm with a one-line `[logged: <type> — <title>]`

Do not narrate beyond the one-line confirmation.
