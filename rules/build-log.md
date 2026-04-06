---
globs: ["**/tutor-notes/**/*.md", "**/.scratch-active.md"]
---

# Build Log file rules

When editing or creating files in the notes directory:

- **Frontmatter is mandatory.** Every final note must have the full YAML frontmatter (date, topic, tags, session-duration, entries).
- **Naming convention:** `YYYY-MM-DD - Topic.md` — Title Case, 2-5 words.
- **Never include secrets.** No API keys, tokens, credentials, PII, or session IDs in any note file. Log the shape of the fix, not the value.
- **Signal field required on scratch entries.** Every T/R/C entry in `.scratch-active.md` must have a `Signal: high | medium | low` line.
- **Tags use hashtag format** in scratch entries (`#bug #networking`) and array format in final note frontmatter (`tags: [bug, networking]`).
