---
name: session-log
description: Capture session notes during learning and build sessions — Task/Reason/Challenge for build work, Question/Answer/Resolution for teaching exchanges. Written to the configured notes directory.
---

# Session Log — Note Capture

Rolling capture of noteworthy moments during sessions, structured as T/R/C (build work) or Q/A/R (teaching exchanges), written to the learner's notes directory. Scratch entries are written in meeting-minutes style — faithful to what was actually said, not paraphrased or sanitised.

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

Learners are bad at taking notes mid-session. The things worth recording — *why* a decision was made, what assumption broke, what the learner actually said and concluded — are obvious in the moment but gone by tomorrow. This skill makes Claude the note-taker, filtering for signal as it happens.

## Entry types

### T/R/C — Task / Reason / Challenge (build work)

For hands-on technical work: building, configuring, hardening, debugging.

- **Task** — what did we do?
- **Reason** — why did we do it?
- **Challenge** — what broke, and how did we fix it? (omit if nothing broke)

| Type | Task | Reason | Challenge |
|---|---|---|---|
| `bug` | Symptom | Root cause | Fix + what it taught |
| `build-guide` | What got set up | Why this approach | Gotchas |
| `adr` | Decision made | Tradeoff articulated | Alternative rejected + why |

### Q/A/R — Question / Answer / Resolution (teaching exchanges)

For learning conversations: framework discussions, reading assignments, guided discovery.

- **Question** — what was asked, and what was the context for asking?
- **Answer** — what did the learner say? Capture their actual words and reasoning faithfully.
- **Resolution** — what did the discussion conclude? What was the final position? Include whether the learner changed their mind or held their ground, and why.

| Type | Question | Answer | Resolution |
|---|---|---|---|
| `concept` | Concept or principle explored | Learner's understanding in their own words | Corrected/confirmed understanding, key takeaway |
| `framework` | Framework/standard question | Learner's interpretation | Final position, how it maps to practice |
| `reading` | Reading assignment given | Learner's response after reading | What landed, what was missed, follow-up needed |

## What counts as noteworthy

Capture if ANY of these:
- New concept named (standard ID, RFC, framework term, vendor-specific term)
- A decision with the tradeoff articulated — future-you won't remember *why*
- Debugging breakthrough / root cause identified
- An assumption that broke
- A reusable pattern worth extracting
- A gotcha that cost real time
- The learner formed or changed an opinion on something
- The learner connected two concepts they hadn't linked before
- A reading assignment was given, completed, or discussed
- Anything the learner reacted to ("huh", "oh", "interesting", "that's annoying")

## Signal levels

Every entry gets a signal tag. This drives synthesis priority.

| Signal | Criteria | Examples |
|---|---|---|
| `high` | Broken assumption, debugging breakthrough, decision with real tradeoff, security finding, learner changed their position based on evidence | Root cause identified after long hunt; learner argued CAF objectives should merge then understood auditor perspective |
| `medium` | New concept learned, reusable pattern, gotcha that cost time, reading assignment completed | Discovered a relevant standard applies here; learner read CSF Section 2 and summarised accurately |
| `low` | Minor observation, small build step worth noting but not standout | Installed package X; tweaked config value; minor clarification |

## What to filter out

- Routine tool calls, typo fixes
- Dead-ends that taught nothing
- Waffle, meandering, tangents
- Things already obvious from code or commit history

## Files & paths

- **Notes directory:** Value of `$TUTOR_NOTES_DIR` (default: `~/tutor-notes/`)
- **Active scratch:** `<notes-dir>/.scratch-active.md`
- **Final notes:** `<notes-dir>/session_log_YYMMDD_HHMM.md`

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

Then append entries as they happen. Each entry is written in **meeting-minutes style** — faithful to what was actually said, preserving the learner's own words and reasoning. Don't paraphrase or sanitise.

### T/R/C scratch entry:

```markdown
## [HH:MM] <type>: <one-line title>

**Signal:** high | medium | low
**Task:** ...
**Reason:** ...
**Challenge:** ... (omit if nothing broke)

Tags: #bug #networking #dns
```

### Q/A/R scratch entry:

```markdown
## [HH:MM] <type>: <one-line title>

**Signal:** high | medium | low
**Question:** ...
**Answer:** <learner's actual words and reasoning>
**Resolution:** ...

Tags: #framework #CSF #hardening
```

## Behavior rules

1. **Session gate:** Only capture if `.scratch-active.md` exists. If it doesn't, the learner hasn't started a session with `/session-start` — stay completely silent on session-log matters. Don't suggest starting a session. Don't create scratch on your own.

2. **Writing is silent.** Don't narrate every capture. One-line confirmation (`[logged: DNS fix]`) is enough.

3. **Faithful, in the learner's voice.** Capture what the learner actually said — their words, their reasoning, their conclusions. Don't clean up or rephrase. Reference standard IDs when they apply.

4. **Never capture secrets.** If a fix involves a token/key/credential, log the shape of the fix ("rotated API key, updated env var") not the value.

5. **Don't nag mid-flow.** Don't ask "should I log this?" — just log it. The learner can prune at synthesis.

6. **Use Claude Code tasks for live tracking.** When assigning reading or questions to the learner, create a task. Mark it complete when they respond. This prevents context slip on what's been asked and answered.

## Synthesis (when `/session-end` runs)

Read scratch, write final note to `<notes-dir>/session_log_YYMMDD_HHMM.md` (using the session start time from the scratch `created:` field).

**Ordering principle: failures and discoveries first.** Challenges, broken assumptions, and opinion changes are the most valuable material for future reference. Surface them prominently.

```markdown
---
date: YYYY-MM-DD
topic: <inferred, 2-5 words title case>
tags: [<from entries>]
session-start: "HH:MM"
session-end: "HH:MM"
session-duration: "<Xh Ym>"
entries: [bug, build-guide, adr, concept, framework, reading]
---

# <Topic>

## Summary
<2-4 line plain-English summary: what the session was about, what got done, what was learned>

## Key Challenges
<high-signal challenges extracted from ALL entry types — the things that broke, surprised, or cost time. Omit section if no challenges were logged.>

## Key Learnings
<high-signal Q/A/R entries — opinions formed, assumptions corrected, concepts that clicked. Omit section if no Q/A/R entries were logged.>

## Bugs
<each bug entry, cleaned up, ordered high to low signal>

## Build Guides
<each build-guide entry, ordered high to low signal>

## ADRs
<each adr entry, ordered high to low signal>

## Teaching Exchanges
<each Q/A/R entry, preserving the learner's actual words and reasoning, ordered high to low signal>

## Links
<cross-references to related notes, if applicable>

## Open questions
<anything unresolved that surfaced during the session — omit if none>

## Review potential
<1-3 bullets: which entries are worth revisiting, sharing, or writing up>
```

After writing and confirming success, delete `.scratch-active.md`.
