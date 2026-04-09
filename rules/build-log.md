---
globs: ["**/tutor-notes/**/*.md", "**/.scratch-active.md"]
---

# Session Log file rules

When editing or creating files in the notes directory:

- **Frontmatter is mandatory.** Every final note must have the full YAML frontmatter (date, topic, tags, session-start, session-end, session-duration, entries). Times in 24h format.
- **Naming convention:** `session_log_YYMMDD_HHMM.md` — 24h session start time.
- **Never include secrets.** No API keys, tokens, credentials, PII, or session IDs in any note file. Log the shape of the fix, not the value.
- **Signal field required on scratch entries.** Every T/R/C and Q/A/R entry in `.scratch-active.md` must have a `Signal: high | medium | low` line.
- **Tags use hashtag format** in scratch entries (`#bug #networking`) and array format in final note frontmatter (`tags: [bug, networking]`).
- **Meeting-minutes style.** Scratch entries preserve the learner's actual words and reasoning. Don't paraphrase.
