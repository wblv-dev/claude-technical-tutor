# Claude Technical Tutor

A Claude Code plugin that turns Claude into a Socratic technical tutor. It guides learners through lab work and exam prep without doing the work for them, and captures structured session notes along the way.

## What it does

**Teaching mode:** Claude asks before answering. When you ask "how do I do X?", you get guiding questions first, then the explanation with the underlying concept named. This builds understanding instead of copy-paste dependency.

**Session notes:** Opt-in structured note-taking using a Task/Reason/Challenge rubric. Claude silently captures noteworthy moments during your work — broken assumptions, debugging breakthroughs, architecture decisions — and synthesizes them into clean markdown notes when you're done.

## Design philosophy

- **Claude never does work for you unless explicitly asked.** This is a learning tool. The default is guidance, not answers.
- **Explicit opt-in for recording.** No notes are captured unless you start a session. Keeps trivial Q&A clean and builds the habit of intentionally entering "work mode."
- **Curation over capture.** Claude filters for signal in real-time rather than dumping everything. Failures and challenges are weighted highest — they're the most valuable material for learning.
- **Works with any markdown note tool.** Obsidian, Logseq, OneNote, or just a folder. Output is plain `.md` files.

## Installation

### As a plugin (recommended)

```bash
# Add as a plugin from GitHub
claude plugin install claude-technical-tutor@wblv-dev
```

### Manual installation

Clone the repo and copy the contents into your Claude Code config:

```bash
git clone https://github.com/wblv-dev/claude-technical-tutor.git

# Copy to your Claude Code directories
cp -r claude-technical-tutor/skills/* ~/.claude/skills/
cp -r claude-technical-tutor/commands/* ~/.claude/commands/
cp -r claude-technical-tutor/rules/* ~/.claude/rules/
```

Then add the CLAUDE.md contents to your own `~/.claude/CLAUDE.md` and the hooks to your `~/.claude/settings.json`.

## Configuration

Set your notes directory via environment variable in `~/.claude/settings.json`:

```json
{
  "env": {
    "TUTOR_NOTES_DIR": "/path/to/your/notes"
  }
}
```

If not set, defaults to `~/tutor-notes/`.

## Commands

| Command | Purpose |
|---|---|
| `/project-start [topic]` | Start a recording session. Optional topic argument. |
| `/note [hint]` | Manually capture a T/R/C entry for the current moment. |
| `/recover` | Safety net — retroactively generates notes from the session transcript when you forgot `/project-start`. |
| `/project-end` | Synthesize all captured entries into a final note and clean up. |

## The T/R/C rubric

Every captured entry answers three questions:

- **Task** — what did we do?
- **Reason** — why did we do it?
- **Challenge** — what broke, and how did we fix it? (omitted if nothing broke)

Entries are tagged with a signal level (`high` / `medium` / `low`) that drives synthesis priority. High-signal challenges surface first in the final note.

## Workflow

```
/project-start Kubernetes networking lab
  ↓
  ... work normally, Claude captures notes silently ...
  ↓
  /note            (manual capture if Claude misses something)
  ↓
/project-end       (synthesize → confirm filename → done)
```

If you forget `/project-start`:

```
... work normally, no notes captured ...
  ↓
/recover           (mines session transcript retroactively)
  ↓
/project-end       (synthesize as normal)
```

## What gets captured

Claude watches for:
- New concepts (standards, RFCs, framework terms)
- Decisions with tradeoffs articulated
- Debugging breakthroughs / root causes
- Broken assumptions
- Reusable patterns
- Gotchas that cost time

Claude filters out:
- Routine tool calls, typo fixes
- Dead-ends that taught nothing
- Things obvious from code or commit history

## Components

| Layer | Component | Purpose |
|---|---|---|
| **CLAUDE.md** | Teaching persona | Socratic mode, never-do-for-you principle |
| **Skill** | `build-log` | Note capture logic, T/R/C rubric, signal levels, synthesis |
| **Commands** | `/project-start` | Session opt-in |
| | `/note` | Manual capture |
| | `/recover` | Transcript recovery (runs in forked context) |
| | `/project-end` | Synthesize and wrap |
| **Hook** | `Stop` | Warns if scratch exists when exiting |
| **Rule** | `build-log` | Format enforcement on note files |

## License

MIT
