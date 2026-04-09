---
description: Capture the current moment as a T/R/C or Q/A/R entry in the active session log scratch.
---

Manually capture the current conversation context as a session-log entry.

Resolve the notes directory: use `$TUTOR_NOTES_DIR` if set, otherwise default to `~/tutor-notes/`.

If arguments are provided ($ARGUMENTS), use them as a hint for the title or topic.

1. Check if `<notes-dir>/.scratch-active.md` exists.
   - If missing → tell the learner: "No active session. Run `/session-start` first." Stop.
2. Determine entry format:
   - **T/R/C** for build work (bug / build-guide / adr)
   - **Q/A/R** for teaching exchanges (concept / framework / reading)
3. Assign a signal level (high / medium / low)
4. Write the entry in meeting-minutes style — faithful to what was actually said, preserving the learner's own words
5. Confirm with a one-line `[logged: <type> — <title>]`

Do not narrate beyond the one-line confirmation.
