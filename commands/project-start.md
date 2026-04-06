---
description: Start a build-log recording session. Creates the scratch file that gates all note capture.
---

Begin a build-log session.

Resolve the notes directory: use `$TUTOR_NOTES_DIR` if set, otherwise default to `~/tutor-notes/`. If the directory doesn't exist, create it.

1. Check if `<notes-dir>/.scratch-active.md` already exists.
   - If present and `created:` is recent (<6h, same day) → tell the learner a session is already active with its topic. Ask: resume or start fresh?
   - If present but stale (>6h or different day) → warn there's an unwrapped session. Ask: wrap it first, discard, or resume?
   - If not present → create it.

2. If arguments are provided ($ARGUMENTS), use them as the session topic. Otherwise mark as "untitled".

3. Write the scratch header per `build-log` skill format:

```markdown
---
created: YYYY-MM-DD HH:MM
status: active
topic: <topic or "untitled">
---

# Session scratch — <topic or "untitled">
```

4. One-line confirmation: `[session started: <topic>]`

Do not narrate beyond the one-line confirmation.
