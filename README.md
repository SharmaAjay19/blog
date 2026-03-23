# Ajay Sharma — Blog

**Sharing technical learnings from building production AI systems at scale.**

This repository is a structured publishing platform for deep technical writing. It is designed to be operated by AI agents following the rules in this document. Every publication, every article, and every design decision has a defined process. Read this entirely before taking any action.

---

## Table of Contents

1. [Platform Philosophy](#1-platform-philosophy)
2. [Repository Structure](#2-repository-structure)
3. [Design System — Shared DNA](#3-design-system--shared-dna)
4. [Creating a New Publication — Full Playbook](#4-creating-a-new-publication--full-playbook)
5. [Root `index.html` — Wiring In a New Publication](#5-root-indexhtml--wiring-in-a-new-publication)
6. [Publication `README.md` — The Template](#6-publication-readmemd--the-template)
7. [Quality Gates — What Must Be True Before Shipping](#7-quality-gates--what-must-be-true-before-shipping)
8. [Existing Publications](#8-existing-publications)

---

## 1. Platform Philosophy

This blog is a **technical journal**, not a dev blog. The intended reader is a senior engineer or architect who has seen production fail in ways textbooks never describe. Every publication exists to serve that reader.

### The Three Rules That Override Everything Else

**Rule 1 — Production over Theory.**
Every article must be grounded in real systems experience. Insights that come only from papers or speculation are not published here. If the author has not seen it break in production, it does not get presented as a principle.

**Rule 2 — Depth over Volume.**
One thorough, well-cited, well-structured article is worth more than ten shallow ones. There are no word count targets. There are no publishing cadence targets. Quality is the only KPI.

**Rule 3 — Design is not decoration.**
The visual design of every publication is an argument about its content. A publication on agentic AI systems should feel precise, technical, and cerebral. The design must earn its aesthetic choices by serving that argument. Generic templates, AI-default color schemes, and predictable layouts are prohibited.

### What "AI Agent Operated" Means

This README is written for AI agents that will:
- Research new topics, synthesize learnings, and draft articles
- Spin up new publication folders with correct structure and design
- Update the root `index.html` to surface new publications
- Maintain consistency with the design system across all publications

An agent reading this document should be able to execute any of those tasks without ambiguity. If a rule is missing, the agent must err toward the strictest interpretation of the existing rules, not fill gaps with defaults.

---

## 2. Repository Structure

```
blog/                           ← GitHub Pages root
├── index.html                  ← Root hub: author page + publication grid
├── README.md                   ← This file — the Y Combinator
│
└── {publication-slug}/         ← One folder per publication
    ├── index.html              ← Publication homepage + markdown renderer
    ├── README.md               ← Publication-specific writing rules
    └── {article-slug}.md       ← Articles as markdown files
```

### Naming Conventions

| Thing | Convention | Example |
|---|---|---|
| Publication folder | lowercase, single word preferred | `agentic/`, `systems/`, `leadership/` |
| Article file | `lowercase_with_underscores.md` | `agentic_context_engineering.md` |
| Publication title | Single evocative noun or short phrase | "Agentic", "Systems", "Leadership" |
| Article title | Statement or declarative fragment, no more than 9 words | "The RAM Doesn't Lie" |

### What Lives Where

- **Root `index.html`** — Author bio, publication cards, philosophy statement. No articles. No markdown rendering.
- **Publication `index.html`** — Publication masthead, article listing, full markdown rendering engine. No author bio (link back to root).
- **Publication `README.md`** — Writing rules specific to that publication's topic and standards. Consumed by AI agents before writing any article in that publication.
- **Article `.md` files** — The actual content. Plain markdown. No front-matter. No YAML. Structure is determined by the article itself, not by templates.

---

## 3. Design System — Shared DNA

All publications share a design lineage. They are recognizably from the same author even though each has its own personality.

### Shared Invariants (Never Break These)

**Background palette:**
```css
--bg:    #0a0905;   /* near-black warm base — every publication uses this */
--bg2:   #100f0a;   /* card surfaces, about strips */
--bd:    #222018;   /* default hairline borders */
--bd2:   #302e24;   /* emphasis dividers */
--tx:    #e8e4d8;   /* primary text — warm off-white, never pure white */
--tx2:   #857f6e;   /* secondary text — metadata, subtitles */
--tx3:   #3c3929;   /* faint text — decorative separators, footer */
```

**Typography stack (load from Google Fonts):**
```
Fraunces — display, headings, masthead (variable: ital, opsz, wght)
DM Mono  — UI chrome: nav, metadata, tags, dates, code labels
```

**Body font:** Each publication chooses its own body serif. This is the primary typographic differentiator between publications. Options used or available:
- `Lora` — balanced reading serif, good for technical/analytical writing
- `Instrument Serif` — refined, slightly editorial, good for author/hub pages
- New publications must choose a distinct serif not already used by a sibling publication.

**Layout constants:**
```css
--wide: 1080px;   /* max-width for all full-bleed sections */
--max:  740px;    /* max-width for article body text */
--r:    3px;      /* border-radius default */
--ease: cubic-bezier(.4,0,.2,1);
```

**Typography scale (article body):**
- Display / H1: Fraunces 800, `clamp(2rem, 5.5vw, 3.25rem)`, `letter-spacing: -.035em`
- H2: Fraunces 700, `1.45rem`, with `border-bottom: 1px solid var(--bd)`
- H3: Fraunces 700, `1.1rem`
- Body: publication body font, `1.05rem`, `line-height: 1.82`
- Metadata / UI: DM Mono, `0.62–0.72rem`, `letter-spacing: .08–.18em`, uppercase

### Per-Publication Differentiators

Each publication defines exactly one accent color. This color appears in:
- Tags and tag borders
- Left-border hover reveals on cards
- The reading progress bar
- The blinking cursor (if used in masthead)
- Blockquote left borders in articles
- Inline code text color

**Accent color registry — claim a color when creating a publication:**

| Publication | Accent (Hex) | CSS Name |
|---|---|---|
| `agentic/` | `#b8f050` | Acid green |
| `systems/` | `#60b4f0` | Sky blue *(reserved)* |
| *(next)* | Choose from: `#f07060` coral, `#c084fc` violet, `#34d399` emerald, `#fb923c` orange | |

**Rule:** No two publications may share an accent color. The root hub uses `#f0b429` (amber) — that color is reserved for the root and must not be used by any publication.

### Prohibited Design Choices

The following are banned across the entire platform:

- ❌ Inter, Roboto, Arial, or system-ui as any font
- ❌ Purple gradients on light or dark backgrounds
- ❌ Generic card shadows (box-shadow with blur)
- ❌ Rounded pills as the primary border radius (use `3px` sharp corners)
- ❌ Emoji in navigation, headings, or tags
- ❌ Full-bleed hero images or background photographs
- ❌ Any color that appears "electric" or "neon" as a primary surface color
- ❌ Sticky sidebars with table-of-contents (use a clean reading progress bar instead)

---

## 4. Creating a New Publication — Full Playbook

Follow these steps in order. Do not skip steps. Do not reorder.

---

### Step 1 — Define the Publication

Before writing a line of HTML, answer these four questions. Write the answers down; they drive every subsequent decision.

1. **Topic:** What is the precise domain? (e.g., "Production failure analysis in distributed systems" — not "systems" in general)
2. **Reader:** Who reads this? What do they already know? What brings them here?
3. **Tone:** Pick one from this list and commit: `analytical`, `narrative`, `tutorial`, `polemical`, `investigative`. The publication's README will enforce this tone on every article.
4. **Accent color:** Choose from the available colors in the registry above. Register it by updating this README.

---

### Step 2 — Create the Folder Structure

```bash
mkdir {publication-slug}
touch {publication-slug}/index.html
touch {publication-slug}/README.md
```

Nothing else. Do not create placeholder articles. Do not create a `css/` or `js/` subfolder. Everything is self-contained in `index.html`.

---

### Step 3 — Build `index.html`

The publication `index.html` is a single-file SPA with hash-based routing. It must implement all of the following:

#### 3a. Required Structural Elements

```
sticky nav bar
  - Publication title (left, Fraunces 700)
  - Blinking cursor after title (accent color, CSS step-end animation)
  - "All Articles" link (right, DM Mono uppercase, links to same page home)

hero section
  - dot-grid texture: CSS radial-gradient at 32px intervals, ~5% opacity, accent color
  - gradient fade overlay (left side, rgba bg toward transparent)
  - kicker line: "Journal of [Domain] · Vol. N" (DM Mono, accent color)
  - masthead: publication title (Fraunces 900, clamp 4rem–8.5rem)
  - blinking cursor in masthead
  - tagline: what this publication covers (publication body font, italic, tx2)
  - meta row: Issue number · Month Year · N articles (DM Mono, tx3)

articles section
  - section label: "Latest Issue" with rule line extending to edge
  - featured card (latest article with `featured: true`)
  - article grid (remaining articles, auto-fill columns)

article page (rendered on #read= hash)
  - back button: "← All Articles"
  - article header: tags, title, subtitle, byline (date · read time)
  - article body: full markdown rendered with styled typography
  - reading progress bar: 2px, fixed top, accent color
```

#### 3b. Article Manifest

At the top of the `<script>` block, define the article manifest array:

```javascript
const ARTICLES = [
  {
    file:     'article-filename.md',     // relative path, no leading slash
    title:    'Display Title',
    subtitle: 'One sentence that earns the click',
    date:     'YYYY-MM-DD',
    tags:     ['Tag One', 'Tag Two'],
    featured: true,                      // only one article is featured at a time
    readTime: 'N min read'               // omit to auto-calculate from word count
  }
];
```

**Rules:**
- Exactly one article has `featured: true` at any time (the most recent)
- When a new article is added, set its `featured: true` and all others to `false`
- Tags are 1–3 words, title case, drawn from the publication's tag vocabulary (defined in publication README)
- `readTime` auto-calculates at ~220 words per minute if omitted

#### 3c. Markdown Rendering Dependencies

Load from CDN. These exact versions are tested and approved:

```html
<script src="https://cdn.jsdelivr.net/npm/marked@9/marked.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/github-dark-dimmed.min.css">
```

Configure `marked` with:
- A custom code renderer that uses `hljs.highlight()` with language detection
- A custom link renderer that opens external URLs in a new tab
- `gfm: true`, `breaks: false`

#### 3d. Article Body Typography Rules

These CSS rules must appear in every publication `index.html` and must not deviate:

```css
/* H2s get a border-bottom hairline — this is a signature element */
.article-body h2 {
  border-bottom: 1px solid var(--bd);
  padding-bottom: .6rem;
}

/* Blockquotes use the accent color — this is how "Insight" callouts look */
.article-body blockquote {
  border-left: 2px solid var(--acc);
  background: var(--surface);
  border-radius: 0 3px 3px 0;
  padding: .9rem 1.5rem .9rem 1.75rem;
}
.article-body blockquote p { font-style: italic; color: var(--tx2); }

/* Inline code gets accent-adjacent coloring — not pure white */
.article-body :not(pre) > code {
  background: var(--surface2);
  color: [accent-adjacent — lighten the accent by ~20%];
}

/* Tables use DM Mono — data should feel like data */
.article-body table { font-family: var(--f-ui); font-size: .78rem; }

/* List markers use accent color */
.article-body li::marker { color: var(--acc-tx); }
```

#### 3e. The Featured Card Hover Effect

This is a signature interaction. It must be implemented exactly:

```css
.featured::before {
  content: ''; position: absolute;
  top: 0; left: 0; width: 3px; height: 100%;
  background: var(--acc);
  transform: scaleY(0); transform-origin: bottom;
  transition: transform .28s cubic-bezier(.4,0,.2,1);
}
.featured:hover::before { transform: scaleY(1); }
```

A vertical 3px bar in accent color slides up from the bottom on hover. This appears on the featured card and on all article grid cards. It is the most recognizable micro-interaction in the platform.

#### 3f. Reading Progress Bar

```html
<div id="progress"></div>
```

```css
#progress {
  position: fixed; top: 0; left: 0;
  height: 2px; width: 0%;
  background: var(--acc);
  z-index: 200;
  transition: width .08s linear;
  display: none; /* shown only on article pages */
}
```

Driven by `scroll` event. Shows only when an article is open. Hides and resets on navigation back to home.

---

### Step 4 — Write the Publication `README.md`

See **Section 6** for the exact template and required sections.

---

### Step 5 — Write the First Article

No publication ships without at least one complete article. The first article sets the standard for everything that follows. Follow the article writing process defined in the publication's `README.md`.

---

### Step 6 — Update Root `index.html`

See **Section 5** for the exact changes required.

---

### Step 7 — Verify Before Committing

Run through every item in **Section 7 — Quality Gates** before the first commit. A publication that does not pass all gates does not ship.

---

## 5. Root `index.html` — Wiring In a New Publication

When a new publication is ready to ship, make the following two changes to the root `index.html`:

### Change 1 — Upgrade the stats

Find the stats block and increment the publication count:

```html
<!-- BEFORE -->
<div class="stat-num">1</div>
<div class="stat-label">Publication</div>

<!-- AFTER -->
<div class="stat-num">2</div>
<div class="stat-label">Publications</div>
```

### Change 2 — Add a publication card to the grid

Replace the first `coming-soon` card (or add a new card) in the `.pub-grid` div:

```html
<a class="pub-card" href="./{publication-slug}/" data-accent="{accent-identifier}">
  <div class="pub-vol">Vol. {N} &middot; {Year}</div>
  <div class="pub-title">{Publication Title}</div>
  <p class="pub-desc">
    {One or two sentences. What does this publication cover?
    Who is it for? Keep it under 25 words.}
  </p>
  <div class="pub-footer">
    <div class="pub-tags">
      <span class="pub-tag">{Tag One}</span>
      <span class="pub-tag">{Tag Two}</span>
      <span class="pub-tag">{Tag Three}</span>
    </div>
    <span class="pub-cta">
      Read publication <span class="pub-arrow">&#8594;</span>
    </span>
  </div>
</a>
```

**Then add the accent CSS** for the new publication's color to the root `index.html` `<style>` block:

```css
.pub-card[data-accent="{accent-identifier}"]::before { background: {accent-hex}; }
.pub-card[data-accent="{accent-identifier}"] .pub-vol   { color: {accent-tx-hex}; }
.pub-card[data-accent="{accent-identifier}"] .pub-tag   { color: {accent-tx-hex}; border-color: rgba({r},{g},{b},.22); background: rgba({r},{g},{b},.06); }
.pub-card[data-accent="{accent-identifier}"] .pub-arrow { color: {accent-hex}; }
```

Where `accent-tx-hex` is the accent color darkened by ~30% for use on dark background text.

**Rules for the publication card description:**
- Maximum 25 words
- Must answer: what domain, what level of depth, what the reader gains
- Written in third person ("Deep technical writing on..."), not first person
- No exclamation marks, no superlatives ("best", "ultimate", "definitive")

---

## 6. Publication `README.md` — The Template

Every publication folder contains a `README.md` that is the authoritative writing guide for that publication. AI agents must read this file before writing any article. The file must contain all of the following sections, customized for the publication's topic and tone.

---

```markdown
# {Publication Title} — Writing Guide

**Publication:** [{Publication Title}](https://{github-pages-url}/{slug}/)
**Accent color:** {hex} — {name}
**Body font:** {Font Name}
**Tone:** {one of: analytical | narrative | tutorial | polemical | investigative}

> One sentence stating the publication's editorial mission.
> Example: "Agentic publishes technical architecture analysis grounded in production systems experience."

---

## Who Reads This

{2–3 sentences. Describe the reader precisely. Their role, experience level,
what they already know, what question brings them here. The more specific,
the better every article will be.}

---

## What Gets Published Here

**In scope:**
- {Topic area 1}
- {Topic area 2}
- {Topic area 3}

**Out of scope (never publish here):**
- {Exclusion 1 — with reason}
- {Exclusion 2 — with reason}

---

## Article Writing Process

Every article follows this four-step process in order. No steps are skipped.

### Step 1 — Source Extraction

If the article is grounded in a primary source (a blog post, a conference talk,
a paper the author was involved with), extract all explicit learnings from that
source first. Preserve the original framing. Note verbatim insights before
any reinterpretation.

### Step 2 — Deep Research

Conduct research across the industry and academic literature to find:
- Independent teams or researchers who reached similar conclusions
- Research papers that provide empirical evidence for or against the insights
- Counter-examples or edge cases that complicate the narrative
- Precise citations: author, title, venue, year, arXiv ID if applicable

Minimum research bar: at least 3 independent sources beyond the primary source.
For strong claims, find peer-reviewed evidence.

### Step 3 — Synthesis

Unify the learnings. Identify the underlying principle behind multiple surface
observations. Reframe insights at the level that generalizes — not "we saw X
in our system" but "X happens because of Y, which is fundamental to Z."

Every principle must answer two questions before it earns its place:
- **Why does it work?** (mechanical explanation, ideally with research backing)
- **Why does it matter?** (consequence of ignoring it in production)

### Step 4 — Write the Article

See "Article Writing Standards" below.

---

## Article Writing Standards

### Voice and Register

{Customize this section for the publication's tone.}

**Default guidance:**
- Write as a senior engineer explaining hard-won lessons to a peer, not as a
  teacher explaining fundamentals to a student
- Assume technical competence. Do not over-explain known concepts.
- First person plural ("we discovered", "we built") when drawing on personal
  production experience. Third person for general industry observations.
- Confident but not arrogant. Strong claims require citations.

### Structure

Every article must open with a provenance note if grounded in specific production
work. Format:

```
> **A note on provenance.** [1–3 sentences establishing where these insights
> came from — specific system, what was built, what scale. Link to original
> source if public.] What follows is...
```

Then: a scene-setting paragraph that establishes the problem space and its stakes.
No bullet-point summaries at the top. The reader earns the structure by reading.

### Prose Rules

- **No bullet points in the body of articles.** Lists are for reference sections
  (Key Principles tables, citations) only. All insights are written as prose paragraphs.
- **No headers with single sentences.** If a section has fewer than two paragraphs,
  collapse it into the preceding section.
- **Blockquotes are for key insights only.** The format is:
  `> **Insight label:** Statement of the insight.`
  Use them 3–7 times per article. Not more — scarcity gives them weight.
- **Bold for genuine emphasis only.** Not for decoration. A paragraph with more
  than two bolded phrases has too many.
- **Analogies must be precise.** If a system is compared to RAM, the comparison
  must hold structurally (finite, addressed, managed), not just evocatively.

### Citations

- All empirical claims must be cited.
- Citation format: `(Author et al., Venue Year)` in text, full reference in
  a "Selected Research Citations" section at the end.
- arXiv papers are acceptable. Blog posts from credible engineering organizations
  are acceptable. Wikipedia is not acceptable as a primary citation.
- When citing a paper, state the specific finding — not just the paper title.

### Article Length

- Minimum: 1,500 words. Below this, the topic is either too narrow (split it out)
  or insufficiently developed (keep writing).
- Target: 2,500–4,500 words. This is the range where depth and readability coexist.
- Maximum: No hard cap, but articles over 6,000 words should be considered for
  splitting into a series.

### The Opening

The first paragraph must:
- Establish the problem or tension the article resolves
- Signal who the article is for (by the specificity of its assumptions)
- Not begin with "I", "We", or the article's own title

The first paragraph must not:
- Summarize what the article is about
- Ask rhetorical questions
- Use the phrase "In this article, we will..."

### The Closing

The last section must:
- Restate the core insight at a higher level of abstraction than the body
- End with a single declarative sentence that lands cleanly
- Not use "In conclusion", "To summarize", or "As we have seen"

---

## Tag Vocabulary

Only use tags from this list. To add a new tag, add it here first.

{List the 8–15 canonical tags for this publication. Example for Agentic:}

- Context Engineering
- Architecture
- Multi-Agent
- Tool Design
- Memory Systems
- Production AI
- LLM Behavior
- Distributed Systems

---

## Article File Naming

`{topic}_{subtopic}.md` — all lowercase, underscores between words, no hyphens.

Examples:
- `agentic_context_engineering.md`
- `tool_design_wide_vs_narrow.md`
- `memory_architectures_production.md`

---

## Adding an Article to the Publication

When an article `.md` file is complete and passes all quality gates:

1. Add an entry to the `ARTICLES` array in `index.html`
2. Set `featured: true` on the new article, `false` on all others
3. Increment the article count in the hero meta row
4. Update the root `index.html` article count stat if the total count is displayed

---

## Quality Gates for This Publication

In addition to the platform-wide gates in the root README, every article in
{Publication Title} must pass these publication-specific checks:

{Customize these for the publication. Example for Agentic:}

- [ ] Article is grounded in at least one production system, not purely theoretical
- [ ] At least 3 research citations with specific findings stated
- [ ] No section consists only of bullet points
- [ ] Every principle answers both "why it works" and "why it matters"
- [ ] The opening paragraph does not summarize the article
- [ ] Blockquotes are used 3–7 times, never fewer, never more
- [ ] All code examples are syntax-highlighted and runnable in principle
- [ ] Tags are drawn from the tag vocabulary above
```

---

## 7. Quality Gates — What Must Be True Before Shipping

### Publication-Level Gates

Before committing a new publication for the first time:

- [ ] `{slug}/index.html` exists and renders correctly in a browser with no console errors
- [ ] `{slug}/README.md` exists and contains all required sections from Section 6
- [ ] At least one complete article exists in `{slug}/`
- [ ] The article appears correctly in the featured card on the publication homepage
- [ ] The reading progress bar activates on the article page and disappears on back navigation
- [ ] The blinking cursor renders in accent color in both nav and masthead
- [ ] The featured card left-border hover effect works at all viewport widths
- [ ] Hash navigation works: `#` loads home, `#read=filename.md` loads article
- [ ] All fonts load from Google Fonts (test on a clean connection or incognito)
- [ ] The publication accent color does not clash with or duplicate any sibling publication's accent
- [ ] The root `index.html` has been updated with the new publication card
- [ ] The root `index.html` publication count stat has been incremented
- [ ] Mobile layout renders correctly at 375px width (no horizontal overflow, no clipped text)

### Article-Level Gates

Before adding any article to the manifest:

- [ ] The article opens with a provenance note (if grounded in personal production work)
- [ ] The article is a minimum of 1,500 words
- [ ] No section of the article is written entirely in bullet points
- [ ] Every claim labeled as a principle has both a "why it works" and a "why it matters" explanation
- [ ] All research citations include: author, title, venue, year
- [ ] The article closes with a landing sentence, not a summary paragraph
- [ ] Code blocks are syntax-highlighted (the language identifier is specified in the markdown fence)
- [ ] Blockquotes are used for genuine key insights only, not for decoration
- [ ] The article file is named in `lowercase_underscores.md` format
- [ ] The article entry in the `ARTICLES` array has all required fields: `file`, `title`, `subtitle`, `date`, `tags`, `featured`
- [ ] `featured: true` is set on this article and `false` on all others in the manifest

### Design Gates

- [ ] No hardcoded colors outside of the defined CSS variables
- [ ] Accent color is used consistently: tags, progress bar, blockquote borders, cursor, card hover
- [ ] `var(--tx)`, `var(--tx2)`, `var(--tx3)` are used for text hierarchy — never hardcoded grays
- [ ] No Inter, Roboto, Arial, or system-ui fonts appear anywhere
- [ ] `max-width: var(--max)` (740px) is applied to article body
- [ ] `max-width: var(--wide)` (1080px) is applied to all full-bleed sections

---

## 8. Existing Publications

### Agentic — `agentic/`

| Property | Value |
|---|---|
| **URL** | `https://SharmaAjay19.github.io/blog/agentic/` |
| **Accent** | `#b8f050` acid green |
| **Body Font** | Lora |
| **Tone** | Analytical |
| **Mission** | Deep technical architecture analysis grounded in production agentic AI systems experience |
| **Articles** | 1 |
| **Writing Guide** | `agentic/README.md` |

**Current articles:**
- `agentic_context_engineering.md` — "The RAM Doesn't Lie: Engineering Context as the Primary Lever for Reliable AI Agents"

---

## A Final Note for AI Agents

This README is the source of truth. When in doubt, re-read it. When two rules appear to conflict, the stricter rule wins. When something is genuinely not covered, document the decision you made and why, so this README can be updated.

The goal of this platform is not to ship content quickly. It is to ship content that is worth reading. Every shortcut taken in process produces an article the reader will not finish. Every design compromise produces a page the reader will not trust. The rules exist because they were not obvious until they were.

---

*Last updated: March 2026*
