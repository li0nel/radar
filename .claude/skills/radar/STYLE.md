# Radar Output Style Guide — The Broadsheet

When generating or updating a forge's `sparks/index.html`, follow this design system exactly. The aesthetic is a newspaper broadsheet: warm cream background, serif body text, double-rule borders, justified columns, italic nameplate. It should feel like a beautifully typeset technical newspaper written for one reader.

## Fonts

- **Nameplate**: Georgia, italic, 28-32px, weight 400
- **Headlines**: Georgia, bold (700), 16-18px, line-height 1.3, color #1a1a1a
- **Body text**: Georgia, 13px, line-height 1.7, color #333, text-align justify, text-indent 1em on continuation paragraphs (not first paragraph after a headline)
- **Kickers** (category labels above headlines): Helvetica Neue / sans-serif, 10px, uppercase, letter-spacing 0.08em, color #999
- **Continue links**: Georgia italic, 11px, color #8b4513
- **Metadata / timestamps**: Helvetica Neue, 10px, color #999

Do NOT use: Inter, Roboto, IBM Plex, monospace fonts, or any sans-serif for body text.

## Colors

- **Background**: #f8f6f1 (warm cream)
- **Text**: #333 (body), #1a1a1a (headlines), #999 (kickers/meta)
- **Accent**: #8b4513 (saddle brown — links, continue markers)
- **Rules**: #222 (nameplate borders), #ddd (column rules), #ccc (section dividers)
- **Active tab**: border-bottom 2px solid #8b4513
- **Inactive tab**: color #999, no border

No blue links. No colored badges or pills. No background colors on cards. The only color beyond gray tones is the warm brown accent.

## Layout

### Nameplate
Centered. Bordered top and bottom with `3px double #222`. Padding 10px vertical. Contains:
- Title "The Radar" in italic Georgia
- Subtitle line: forge name + date + spark count in sans-serif 10px

### View tabs
Immediately below nameplate. Two tabs: **State of the Art** and **Dispatch**. Pure CSS tab switching using `:target` or hidden checkbox hack — no JavaScript. Tabs are styled as small-caps sans-serif, 11px, with the active tab having a `border-bottom: 2px solid #8b4513`. Clicking a tab shows/hides the corresponding content section.

### State of the Art view (default)
Two-column layout using CSS `column-count: 2`, `column-gap: 24px`, `column-rule: 1px solid #ddd`.

Each spark section within the columns:
1. **Kicker** — category/theme label (ORCHESTRATION, CONTEXT MANAGEMENT, CODE REVIEW, etc.)
2. **Headline** — the key insight in one punchy line
3. **Body paragraphs** — dense technical prose, justified, first paragraph no indent, subsequent paragraphs indented 1em
4. **Relevance line** — italic, prefixed with →, explains why this matters to the user specifically. Color #555.
5. **Continue link** — italic, color #8b4513, links to a child page for depth. Format: "Continue: [subtopic name] →"

Use `break-inside: avoid` on each spark block to prevent awkward column breaks mid-section. Separate sections with a thin rule `1px solid #ddd` with 1rem vertical margin.

This view is **overwritten every scan** to reflect the current state of the art. It is the reference — always reads as a coherent document, not a changelog.

### Dispatch view (feed)
Single column, full width. This is the **append-only feed** showing what changed each run.

Each scan run gets a **run block**:
- **Run header**: date + scan timestamp, styled as a thin double-rule separator with date in small-caps sans-serif
- **Entries** within the run, each containing:
  - **Label**: NEW / UPDATED / REVISED / REMOVED in small-caps sans-serif, color #8b4513
  - **Headline**: what the spark says (links to the corresponding section in State of the Art view via `#anchor`)
  - **One paragraph**: summary of the insight or what changed
  - **Relevance line**: why this matters

Runs are ordered **newest first**. The dispatch grows over time — it is the history of discoveries. Old runs are never deleted, only pushed down.

When scanning: append the new run block at the top of the dispatch section. Do NOT touch previous run blocks.

### Child pages (subtopic deep dives)
When generating child pages (linked from "Continue:" lines), use the same broadsheet aesthetic but single-column layout, wider measure (max-width 680px centered). These pages go deep — 1000-3000 words of dense technical content. Include:
- Back link at top: "← Back to The Radar"
- Same nameplate but smaller (20px title)
- Full body text with code examples where relevant
- Source citations as footnotes at the bottom, not inline
- Related links to other child pages at the end

## Writing voice

Dense, technical, opinionated. Write like a Principal Engineer's internal notes published as a broadsheet.

- Lead with the insight, not the background
- No preamble ("In recent years...", "It's worth noting...")
- Assume deep engineering context — don't explain basics
- Every section ends with a relevance line connecting the insight to the user's specific projects, interviews, or tools
- Use concrete specifics: version numbers, throughput numbers, tool names, pattern names
- Headlines are assertions, not questions: "Fan-out/fan-in wins the orchestration debate" not "Is fan-out/fan-in the right approach?"

## HTML Templates

There are three templates. Use these exactly. All share the same CSS design system (defined in the shared stylesheet below).

### Shared stylesheet

All pages include this stylesheet. Do not modify or improvise — copy it verbatim into each page's `<style>` tag.

```css
* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  font-family: 'Georgia', serif;
  background: #f8f6f1;
  color: #333;
  line-height: 1.7;
  max-width: 860px;
  margin: 0 auto;
  padding: 2rem 1.5rem;
}

.nameplate {
  text-align: center;
  border-top: 3px double #222;
  border-bottom: 3px double #222;
  padding: 10px 0;
  margin-bottom: 1.25rem;
}

.nameplate h1 {
  font-size: 28px;
  font-weight: 400;
  font-style: italic;
  color: #1a1a1a;
}

.nameplate .edition {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
  font-size: 10px;
  color: #999;
  letter-spacing: 0.06em;
  margin-top: 4px;
}

/* Tab navigation */
.tabs {
  display: flex;
  gap: 1.5rem;
  border-bottom: 1px solid #ddd;
  margin-bottom: 1.5rem;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
  font-size: 11px;
  text-transform: uppercase;
  letter-spacing: 0.08em;
}

.tabs a {
  color: #999;
  text-decoration: none;
  padding: 6px 0;
  border-bottom: 2px solid transparent;
}

.tabs a:hover { color: #555; }

/* Tab switching via checkbox hack (no JS) */
#tab-toggle { display: none; }
#tab-toggle:not(:checked) ~ .tabs .tab-sota { color: #1a1a1a; border-bottom-color: #8b4513; }
#tab-toggle:not(:checked) ~ .tabs .tab-dispatch { color: #999; border-bottom-color: transparent; }
#tab-toggle:checked ~ .tabs .tab-sota { color: #999; border-bottom-color: transparent; }
#tab-toggle:checked ~ .tabs .tab-dispatch { color: #1a1a1a; border-bottom-color: #8b4513; }
#tab-toggle:not(:checked) ~ #state-of-the-art { display: block; }
#tab-toggle:not(:checked) ~ #dispatch { display: none; }
#tab-toggle:checked ~ #state-of-the-art { display: none; }
#tab-toggle:checked ~ #dispatch { display: block; }

/* State of the Art — two columns */
.cols {
  column-count: 2;
  column-gap: 28px;
  column-rule: 1px solid #ddd;
}

.section {
  break-inside: avoid;
  margin-bottom: 1.25rem;
  padding-bottom: 1.25rem;
  border-bottom: 1px solid #ddd;
}

.section:last-child { border-bottom: none; }

.kicker {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
  font-size: 10px;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: #999;
  margin-bottom: 4px;
}

.headline {
  font-size: 17px;
  font-weight: 700;
  color: #1a1a1a;
  line-height: 1.3;
  margin-bottom: 8px;
}

p {
  font-size: 13px;
  line-height: 1.7;
  color: #333;
  text-align: justify;
  margin-bottom: 0.6rem;
}

p + p { text-indent: 1em; }

.relevance {
  font-style: italic;
  font-size: 12.5px;
  color: #555;
  margin-top: 6px;
  text-indent: 0;
  text-align: left;
}

.relevance::before { content: '→ '; color: #8b4513; }

.cont {
  font-size: 11px;
  color: #8b4513;
  font-style: italic;
  text-indent: 0;
  text-align: left;
  margin-top: 4px;
}

.cont a { color: #8b4513; text-decoration: none; }
.cont a:hover { text-decoration: underline; }

a { color: #8b4513; text-decoration: none; }
a:hover { text-decoration: underline; }

/* Dispatch — feed view */
.run-block {
  margin-bottom: 2rem;
  padding-bottom: 1.5rem;
  border-bottom: 1px solid #ddd;
}

.run-header {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
  font-size: 10px;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: #999;
  padding: 6px 0;
  border-top: 1px double #ccc;
  border-bottom: 1px double #ccc;
  margin-bottom: 1rem;
}

.dispatch-entry {
  margin-bottom: 1.25rem;
}

.dispatch-label {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
  font-weight: 600;
  font-size: 10px;
  text-transform: uppercase;
  letter-spacing: 0.04em;
  color: #8b4513;
  margin-right: 6px;
}

.dispatch-headline {
  font-size: 15px;
  font-weight: 700;
  color: #1a1a1a;
  line-height: 1.3;
  margin-bottom: 4px;
}

.dispatch-headline a { color: #1a1a1a; }
.dispatch-headline a:hover { color: #8b4513; }

code {
  font-family: 'Courier New', monospace;
  font-size: 0.9em;
  background: #f0ece0;
  padding: 1px 4px;
}

pre {
  background: #f0ece0;
  border: 1px solid #ddd;
  padding: 10px 14px;
  font-family: 'Courier New', monospace;
  font-size: 12px;
  line-height: 1.5;
  overflow-x: auto;
  margin: 0.75rem 0;
  text-align: left;
  text-indent: 0;
}

footer {
  margin-top: 2rem;
  padding-top: 0.75rem;
  border-top: 3px double #222;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
  font-size: 10px;
  color: #999;
  text-align: center;
  letter-spacing: 0.04em;
}

/* Single-column mode for child pages */
.single-col {
  max-width: 680px;
  margin: 0 auto;
  column-count: 1;
}

.back-link {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
  font-size: 11px;
  color: #8b4513;
  text-decoration: none;
  margin-bottom: 1rem;
  display: inline-block;
}

/* Responsive: single column on narrow screens */
@media (max-width: 640px) {
  .cols { column-count: 1; }
  body { padding: 1.5rem 1rem; }
}
```

---

### Template 1: Forge Spark Page (`forges/<name>/sparks/index.html`)

This is the main output per forge. Contains two views: State of the Art (reference) and Dispatch (feed). Tab switching uses a hidden checkbox hack — no JavaScript.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>The Radar — FORGE_NAME</title>
<style>
  /* PASTE SHARED STYLESHEET HERE */
</style>
</head>
<body>

<div class="nameplate">
  <h1>The Radar</h1>
  <div class="edition"><a href="../../../index.html">← All Forges</a> · FORGE_NAME · DATE · N sparks</div>
</div>

<!-- Checkbox hack for tab switching -->
<input type="checkbox" id="tab-toggle">
<div class="tabs">
  <label for="tab-toggle" class="tab-sota" style="cursor:pointer;">State of the Art</label>
  <label for="tab-toggle" class="tab-dispatch" style="cursor:pointer;">Dispatch</label>
</div>

<div id="state-of-the-art">
  <div class="cols">
    <!-- SPARK SECTIONS — overwritten every scan -->
    <!-- Each spark gets a unique id for anchor linking from Dispatch -->

    <div class="section" id="spark-slug-here">
      <div class="kicker">THEME NAME</div>
      <div class="headline">Assertion headline stating the key insight</div>
      <p>First paragraph, no indent. Dense technical prose.</p>
      <p>Continuation paragraph, auto-indented via CSS p + p rule.</p>
      <p class="relevance">Why this matters to me specifically.</p>
      <p class="cont"><a href="child-page-slug.html">Continue: subtopic name →</a></p>
    </div>

    <!-- More sections... -->
  </div>
</div>

<div id="dispatch">
  <!-- FEED — append-only, newest run first. Never delete old blocks. -->

  <div class="run-block">
    <div class="run-header">Scan — March 31, 2026 · 14:30 UTC · 5 new, 2 revised</div>

    <div class="dispatch-entry">
      <div class="dispatch-headline"><span class="dispatch-label">NEW</span> <a href="#spark-slug-here">Assertion headline</a></div>
      <p>One paragraph summary of the insight.</p>
      <p class="relevance">Why this matters to me specifically.</p>
    </div>

    <div class="dispatch-entry">
      <div class="dispatch-headline"><span class="dispatch-label">REVISED</span> <a href="#spark-slug-here">Updated headline</a></div>
      <p>What changed and why the previous version was updated.</p>
      <p class="relevance">Updated relevance note.</p>
    </div>
  </div>

  <!-- Previous run blocks stay here untouched -->
</div>

<footer>
  The Radar · Last scan: TIMESTAMP · /radar:feedback to refine
</footer>

</body>
</html>
```

---

### Template 2: Child Page (`forges/<name>/sparks/<child-slug>.html`)

Deep dive on a single spark. Single column, long-form. Linked from "Continue:" in the main page.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>The Radar — CHILD_TITLE</title>
<style>
  /* PASTE SHARED STYLESHEET HERE */
</style>
</head>
<body>

<a href="index.html" class="back-link">← Back to FORGE_NAME</a>

<div class="nameplate">
  <h1 style="font-size:20px;">The Radar</h1>
  <div class="edition">FORGE_NAME · CHILD_TITLE</div>
</div>

<div class="single-col">
  <div class="kicker">THEME NAME</div>
  <div class="headline" style="font-size:20px;">Full assertion headline for the deep dive</div>

  <p>Dense technical prose. This page goes deep — 1000-3000 words. Lead with the core insight, then expand with evidence, code examples, and source material.</p>

  <p>Continuation paragraphs auto-indent. Use concrete specifics: version numbers, throughput numbers, tool names, pattern names. Every claim should be traceable to a source.</p>

  <pre><code>// Code examples where relevant
// Keep them short — the broadsheet aesthetic doesn't suit long listings
func example() {
    // ...
}</code></pre>

  <p>More analysis, tradeoffs, and practical implications.</p>

  <p class="relevance">Why this deep dive matters to me specifically, tied to current projects and goals.</p>

  <h3 style="font-size:14px; font-weight:700; margin-top:1.5rem; margin-bottom:0.5rem; color:#1a1a1a;">Related</h3>
  <p style="text-indent:0;">
    <a href="other-child-slug.html">Related child page →</a><br>
    <a href="another-child-slug.html">Another related page →</a>
  </p>

  <h3 style="font-size:14px; font-weight:700; margin-top:1.5rem; margin-bottom:0.5rem; color:#1a1a1a;">Sources</h3>
  <ol style="font-size:12px; color:#555; padding-left:1.5em; line-height:1.8;">
    <li><a href="https://example.com/source1">Source title — Author, Date</a></li>
    <li><a href="https://example.com/source2">Source title — Author, Date</a></li>
  </ol>
</div>

<footer>
  The Radar · <a href="index.html">Back to FORGE_NAME</a>
</footer>

</body>
</html>
```

---

### Root index (`index.html` at project root)

Lists all forges with links. Updated when forges are added or removed. Already generated at project root — see `index.html`. Uses the same broadsheet aesthetic but simpler layout (no tabs, no columns). Update the forge list in this file whenever `/radar:forge` creates a new forge.

---

## Rules

1. ALWAYS use the shared stylesheet verbatim. Do not improvise colors, fonts, or layout.
2. The two-column layout is non-negotiable for the State of the Art view. Child pages and Dispatch are single-column.
3. Every spark MUST have a relevance line. No exceptions.
4. Every spark MUST have a "Continue:" link to a child page. The child page is where the depth lives.
5. Headlines are assertions. Never questions, never vague.
6. Body text is justified. This is a broadsheet, not a blog.
7. **State of the Art is overwritten every scan.** It always reads as a coherent current reference. Revise sections in place — don't append.
8. **Dispatch is append-only.** Each scan prepends a new run-block at the top. Never modify or delete previous run blocks.
9. Every spark section in State of the Art gets an `id="spark-slug"` anchor. Dispatch entries link to these anchors.
10. Child pages go in the forge's `sparks/` directory alongside `index.html`, named by slugified topic: `orchestration-patterns.html`, `skill-composition.html`, etc.
11. Code examples use `<pre><code>` blocks with the cream background, not syntax-highlighted. Keep them short.
12. Tab switching uses the checkbox hack — no JavaScript. The page must be fully functional as static HTML.
13. Every child page MUST have a "← Back to FORGE_NAME" link and a Sources section with numbered footnotes.
14. When a scan adds or revises sparks, generate or update corresponding child pages. A spark without a child page is incomplete.
