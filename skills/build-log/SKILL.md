---
name: build-log
description: Capture Task/Reason/Challenge notes during learning sessions (bugs, build guides, ADRs) into the configured notes directory. Load during hands-on technical work — lab builds, debugging, setup, architecture decisions.
---

# Build Log — Session Note Capture

Rolling capture of noteworthy moments during technical work, structured as Task/Reason/Challenge entries, written to the learner's notes directory.

## Configuration

The notes directory is set via the `TUTOR_NOTES_DIR` environment variable. If not set, defaults to `~/tutor-notes/`.

Set it in your Claude Code settings.json:
```json
{
  "env": {
    "TUTOR_NOTES_DIR": "/path/to/your/notes"
  }
}
```

Works with any markdown-based note tool: Obsidian, Logseq, OneNote (via markdown), or just a folder of `.md` files.

## Why this exists

Learners are bad at taking notes mid-session. The things worth recording — *why* a decision was made, what assumption broke, what the root cause actually was — are obvious in the moment but gone by tomorrow. This skill makes Claude the note-taker, filtering for signal as it happens.

## The rubric — Task / Reason / Challenge

Every entry fits this shape:

- **Task** — what did we do?
- **Reason** — why did we do it?
- **Challenge** — what broke, and how did we fix it? (omit if nothing broke)

Three entry types:

| Type | Task | Reason | Challenge |
|---|---|---|---|
| `bug` | Symptom | Root cause | Fix + what it taught |
| `build-guide` | What got set up | Why this approach | Gotchas |
| `adr` | Decision made | Tradeoff articulated | Alternative rejected + why |

## What counts as noteworthy

Capture if ANY of these:
- New concept named (standard ID, RFC, framework term, vendor-specific term)
- A decision with the tradeoff articulated — future-you won't remember *why*
- Debugging breakthrough / root cause identified
- An assumption that broke
- A reusable pattern worth extracting
- A gotcha that cost real time
- Anything the learner reacted to ("huh", "oh", "interesting", "that's annoying")

## Signal levels

Every entry gets a signal tag. This drives synthesis priority.

| Signal | Criteria | Examples |
|---|---|---|
| `high` | Broken assumption, debugging breakthrough, decision with real tradeoff, security finding | Root cause identified after long hunt; chose X over Y because of constraint |
| `medium` | New concept learned, reusable pattern, gotcha that cost time | Discovered a relevant standard applies here; this config bites everyone |
| `low` | Minor observation, small build step worth noting but not standout | Installed package X; tweaked config value |

## What to filter out

- Routine tool calls, typo fixes
- Dead-ends that taught nothing
- Waffle, meandering, tangents
- Things already obvious from code or commit history

## Files & paths

- **Notes directory:** Value of `$TUTOR_NOTES_DIR` (default: `~/tutor-notes/`)
- **Active scratch:** `<notes-dir>/.scratch-active.md`
- **Final notes:** `<notes-dir>/YYYY-MM-DD - Topic.md`

## Scratch file format

On creation, write this header:

```markdown
---
created: YYYY-MM-DD HH:MM
status: active
topic: <topic or "untitled">
---

# Session scratch — <short title if known, else "untitled">
```

Then append entries as they happen. Each entry:

```markdown
## [HH:MM] <type>: <one-line title>

**Signal:** high | medium | low
**Task:** ...
**Reason:** ...
**Challenge:** ... (omit if nothing broke)

Tags: #bug #networking #dns
```

## Behavior rules

1. **Session gate:** Only capture if `.scratch-active.md` exists. If it doesn't, the learner hasn't started a session with `/project-start` — stay completely silent on build-log matters. Don't suggest starting a session. Don't create scratch on your own.

2. **Writing is silent.** Don't narrate every capture. One-line confirmation (`[logged: DNS fix]`) is enough.

3. **Terse, in the learner's voice.** Direct, specific, no filler. Reference standard IDs when they apply.

4. **Never capture secrets.** If a fix involves a token/key/credential, log the shape of the fix ("rotated API key, updated env var") not the value.

5. **Don't nag mid-flow.** Don't ask "should I log this?" — just log it. The learner can prune at synthesis.

## Synthesis (when `/project-end` runs)

Read scratch, write final note to `<notes-dir>/YYYY-MM-DD - <Topic>.md`.

**Ordering principle: failures first.** Challenges and bugs are the most valuable material for future reference. Surface them prominently.

```markdown
---
date: YYYY-MM-DD
topic: <inferred, 2-5 words title case>
tags: [<from entries>]
session-duration: <HH:MM start – HH:MM end>
entries: [bug, build-guide, adr]
---

# <Topic>

## Summary
<2-4 line plain-English summary: what the session was about, what got done>

## Key Challenges
<high-signal challenges extracted from ALL entry types — the things that broke, surprised, or cost time. Omit section if no challenges were logged.>

## Bugs
<each bug entry, cleaned up, ordered high to low signal>

## Build Guides
<each build-guide entry, ordered high to low signal>

## ADRs
<each adr entry, ordered high to low signal>

## Links
<cross-references to related notes, if applicable>

## Open questions
<anything unresolved that surfaced during the session — omit if none>

## Review potential
<1-3 bullets: which entries are worth revisiting, sharing, or writing up>
```

After writing and confirming success, delete `.scratch-active.md`.
