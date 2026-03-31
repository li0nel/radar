# Radar — Quick Start

## Setup

1. Copy `.claude/skills/radar/` to your Claude Code project
2. Create `preferences.md` at your project root
3. Create forges in `forges/<name>/` — one directory per topic area

## Concepts

- **Forge** — a self-contained topic area you track (`forges/agentic-coding/`)
- **Spark** — an actionable insight generated from scanning a forge
- **Preferences** — global scoring/voice/sources shared across all forges

## Usage

```
/radar:scan agentic-coding        # Scan a single forge
/radar:scan --all                  # Scan all forges
/radar:debrief agentic-coding     # Review a forge's living document
/radar:feedback agentic-coding go deeper on orchestration patterns
/radar:forge go-concurrency       # Create a new forge
/radar:status                     # Overview of all forges
```

## Project Structure

```
your-project/
├── preferences.md                        # Global preferences (you maintain this)
├── forges/
│   └── agentic-coding/                   # One directory per forge
│       ├── forge.md                      # Forge definition
│       ├── state/
│       │   ├── queries.md                # Derived queries (auto-generated)
│       │   ├── presented.jsonl           # Spark fingerprints
│       │   └── feedback.jsonl            # Feedback log
│       └── sparks/
│           └── index.html                # Living document output
└── .claude/skills/radar/                 # The skill (generic, shareable)
    └── SKILL.md
```

## Forge Template

Create `forges/<name>/forge.md`:

```markdown
# my-topic

**Priority**: high
**Type**: permanent
**Max sparks per scan**: 3

Description of what you're tracking and why.

## Key questions
- Question 1?
- Question 2?

## Source preferences
What sources you trust for this topic.

## Trusted voices
(populated via feedback)
```

Then create `forges/<name>/state/` and `forges/<name>/sparks/` directories, or use `/radar:forge <name>` to scaffold everything.
