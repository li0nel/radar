---
name: radar
description: Radar — Personal State-of-the-Art Tracker. Scans the web for insights across user-defined topic areas (forges), generates sparks (actionable insights), and maintains a living reference document per forge. All data lives in the project root, not in this skill.
---

# Radar — Personal State-of-the-Art Tracker

## What this is

Radar maintains **living reference documents** on topics the user wants to stay on top of. It is NOT a news feed. It is a continuously-updated, opinionated technical reference that represents the current state of the art — what works, what doesn't, who the credible voices are, and what matters to the user specifically.

Each topic area is a **forge** — a self-contained directory with its own definition, state, and output. The skill operates on one forge at a time by default.

## Concepts

- **Forge**: A topic area the user tracks. Each forge is a directory under `forges/` containing a `forge.md` definition, a `state/` directory, and a `sparks/` output directory. Forges are self-contained — all state and output is scoped to the forge.
- **Spark**: An actionable insight generated from scanning a forge. Sparks live in the forge's `sparks/` directory.
- **Preferences**: Global scoring criteria, editorial voice, source reputation, and relevance gates. Shared across all forges. Lives at `preferences.md` in the project root.

## Project Structure (discovered at runtime)

The skill reads all data from the project root. It never stores data inside the skill directory.

```
<project-root>/
├── preferences.md                      # Global preferences (shared across all forges)
├── forges/
│   └── <forge-name>/                   # One directory per topic area
│       ├── forge.md                    # Forge definition (priority, key questions, sources)
│       ├── state/
│       │   ├── queries.md              # Derived query set for this forge
│       │   ├── presented.jsonl         # Spark fingerprints for changelog dedup
│       │   └── feedback.jsonl          # Feedback log scoped to this forge
│       └── sparks/
│           └── index.html              # The living document for this forge
└── .claude/skills/radar/
    └── SKILL.md                        # This skill (generic, shareable)
```

## Search Tools

This skill uses the **Exa MCP** for all web searches. The following tools are available (load via ToolSearch before first use):

- `mcp__exa__web_search_exa` — Primary search tool. Use for all forge query execution. Supports semantic and keyword search.
- `mcp__exa__crawling_exa` — Fetch and extract content from a specific URL. Use when a search result looks promising and you need the full content.
- `mcp__exa__people_search_exa` — Search for people/experts. Use when a forge's trusted voices need discovery.
- `mcp__exa__company_research_exa` — Research companies. Use when forge queries involve specific companies or orgs.

Always prefer Exa over the built-in WebSearch tool — Exa returns higher quality, more relevant results for technical research.

## Commands

### `/radar:scan [forge-name]`

Run a scan cycle on a single forge. If no forge name is given, list available forges and ask which one to scan.

1. Read `forges/<forge-name>/forge.md` for the forge definition
2. Read `preferences.md` from the project root for scoring criteria and relevance gates
3. Read `forges/<forge-name>/state/queries.md` for the current derived query set
4. Read `STYLE.md` in the skill directory for the output design system
5. Load Exa tools via ToolSearch if not already loaded
6. Execute web searches using `mcp__exa__web_search_exa` for each query in the set
7. For promising results, use `mcp__exa__crawling_exa` to fetch full content
8. Score and filter results against preferences
9. If `sparks/index.html` exists, read it to understand current state (for revisions, not duplication)
10. **State of the Art**: Overwrite the State of the Art section in `sparks/index.html` — revise existing sparks, add new ones, remove stale ones. This is always a coherent current reference.
11. **Dispatch**: Prepend a new run-block to the Dispatch section with entries for every spark that was added, revised, or removed. Link each entry to its `#anchor` in the State of the Art view. Never modify previous run-blocks.
12. Generate child pages in `sparks/` for sparks that warrant depth
13. Append changes to `forges/<forge-name>/state/presented.jsonl` with fingerprints

To scan all forges: `/radar:scan --all` (loops over each forge sequentially).

**Rules for scanning:**
- Hard cap per forge: respect the forge's `Max sparks per scan` setting. Default is 3 if not specified.
- Every spark MUST pass the actionability gate: "Does this change what the user would do or say in the next 2 weeks?" If it's interesting but not actionable, skip it.
- Novelty of insight matters more than recency of publication. Another blog post explaining known patterns is NOT a spark. A production postmortem about failure IS.
- Primary sources over aggregators. Always. Conference talks, official docs, RFCs, postmortems > blog posts > tweets > Medium articles.
- When updating the living document, don't just append — revise. If new information supersedes or refines something already in the document, UPDATE that section. The document should always read as a coherent current reference, not an accretion of timestamped entries.

### `/radar:debrief [forge-name]`

Generate the current briefing for a forge. If no forge name is given, list available forges and ask.

1. Open or present `forges/<forge-name>/sparks/index.html`
2. Show the changelog: what changed since the user's last acknowledged debrief
3. Highlight which sections of the living document were updated and why

The debrief is the user's review session. After reading, they give feedback.

### `/radar:feedback <forge-name> <voice input>`

Process user feedback scoped to a specific forge. This happens IMMEDIATELY, not in batch:

1. Parse the feedback for:
   - **Source-level signals**: "this source is garbage", "always check what X thinks"
   - **Topic-level signals**: "go deeper on X", "I don't care about Y anymore"
   - **Format signals**: "too verbose", "need more code examples", "link to primary sources"
   - **Scoring signals**: "this wasn't relevant to me because...", "this is exactly what I need"
   - **Query refinement signals**: "search for X instead of Y", "add subqueries about Z"
2. Append raw feedback to `forges/<forge-name>/state/feedback.jsonl` with timestamp
3. IMMEDIATELY update `preferences.md` at the project root if the feedback is global (source reputation, editorial voice, scoring)
4. IMMEDIATELY update `forges/<forge-name>/forge.md` if the feedback is forge-specific (key questions, source preferences)
5. IMMEDIATELY regenerate `forges/<forge-name>/state/queries.md` from the updated forge definition + preferences
6. Confirm to user what changed: "Updated: [summary]. Query set now includes: [new queries]. Removed: [dropped queries]."

### `/radar:status`

Show current state across all forges:
1. List all forges (discovered from `forges/*/forge.md`) with priority and type
2. Per-forge: query count, last scan time, feedback count, living document size
3. Overall summary

### `/radar:forge <name>`

Create a new forge. Prompts the user for:
- Priority (high/medium/low)
- Type (permanent / time-boxed with expiry)
- Description
- Key questions
- Source preferences

Creates `forges/<name>/forge.md`, `forges/<name>/state/`, `forges/<name>/sparks/`, and generates initial `state/queries.md`.

## Output Structure

**IMPORTANT**: Before generating or updating any HTML output, ALWAYS read `STYLE.md` in this skill directory. It contains the complete design system, three HTML templates, and rendering rules. Follow it exactly. Do not improvise styles.

### Pages generated per forge

- **`sparks/index.html`** — The main page with two tab views:
  - **State of the Art** — two-column broadsheet, overwritten every scan. The current reference.
  - **Dispatch** — single-column feed, append-only. Each scan prepends a run-block with NEW/REVISED/REMOVED entries linking back to State of the Art anchors.
- **`sparks/<child-slug>.html`** — One child page per spark that warrants depth (1000-3000 words). Linked from "Continue:" in State of the Art. Every spark MUST have a child page.

### Root index

- **`index.html`** at the project root lists all forges with links to their `sparks/index.html`. Update this when `/radar:forge` creates a new forge.

## Forge Files

### `forge.md`
The forge definition. Expected fields: Priority, Type, Max sparks per scan, Description, Key questions, Source preferences, Trusted voices. The skill discovers forges by globbing `forges/*/forge.md` — no registry needed.

### `state/queries.md`
The CURRENT derived query set for this forge. Regenerated from `forge.md` + `preferences.md` + recent feedback. This is what scan actually executes.

### `state/presented.jsonl`
Every spark ever surfaced for this forge, with content fingerprint, source, date found, and whether it's currently in the living document. Used for dedup in the changelog.

### `state/feedback.jsonl`
Append-only log of feedback for this forge. Each entry: timestamp, raw text, parsed signals, what it changed.

### `sparks/index.html`
The living document for this forge. The product.

## Global Files

### `preferences.md`
The distilled preference model at the project root. Shared across all forges. Updated on feedback events that affect global preferences. Contains:
- Editorial voice preferences
- User context (for relevance scoring)
- Scoring criteria and weights
- Source reputation (trusted, neutral, untrusted sources by name)
- Format preferences (density, code examples, depth)
- Anti-patterns (what to filter out)

## Design Principles

1. **The document is the product.** Not the scan, not the queries, not the feedback loop. Each forge's living document should be something the user genuinely wants to read and reference.
2. **Dense and technical.** No hand-holding, no preamble. Lead with the insight. Write like internal engineering notes at a top-tier company.
3. **Every spark earns its place.** The actionability gate is sacred. "Interesting but not useful to me right now" = filtered out.
4. **Feedback is immediate.** No batching, no deferred processing. The user speaks, the system restructures.
5. **Forges are self-contained.** Each forge owns its state and output. You can delete a forge directory and nothing else breaks.
6. **The skill is the process, the project is the data.** This skill contains zero user data. All forges, preferences, and output live in the project root. The skill is shareable as-is.
