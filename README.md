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

**Color palette (light theme):**
```css
--bg:         #ffffff;   /* page background — clean white */
--bg-soft:    #fafafa;   /* card / strip surfaces, hover fills */
--bg-code:    #f6f8fa;   /* inline code + code-block background */
--text:       #242424;   /* primary text — near-black, never pure #000 */
--text-soft:  #5c5c5c;   /* secondary text — subtitles, de-emphasis */
--text-faint: #8a8a8a;   /* faint text — metadata, dates, captions */
--border:     #ececec;   /* default hairline borders */
--border-2:   #dcdcdc;   /* emphasis dividers, hover borders */
--accent:     #1a8917;   /* the single brand green — see below */
```

**Typography stack:**
```
Source Serif 4 — display, headings, AND body copy (the only loaded webfont)
System sans    — UI chrome: nav, metadata, tags, dates, section labels
System mono    — code blocks and inline code
```

Only **one** webfont is loaded — Source Serif 4 (variable: ital, opsz, wght) — and it carries both headings and body. The sans and mono stacks are native system fonts. Declare all three as variables:

```css
--serif: 'Source Serif 4', Georgia, 'Times New Roman', serif;
--sans:  -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
--mono:  ui-monospace, SFMono-Regular, 'SF Mono', Menlo, Consolas, monospace;
```

**Body font:** Source Serif 4 is the shared reading serif across every publication — not a per-publication differentiator. Publications differ through tone and accent usage, not typeface swaps.

**Layout constants:**
```css
--max:  720px;   /* article body measure (the root hub uses 1100px for its grid) */
--wide: 1100px;  /* full-width sections: hero, article list, publication grid */
--ease: cubic-bezier(.4,0,.2,1);
/* Border radius is intentionally varied, not one token:
   12px cards · 8px code blocks · 100px tag/topic pills · 4px inline code */
```

**Typography scale (article page):**
- Article title (header H1): Source Serif 700, `clamp(2.1rem, 5.5vw, 3.25rem)`, `letter-spacing: -.025em`
- Standfirst (opening deck): Source Serif, `1.4rem`, `var(--text-soft)`
- Body H2: Source Serif 700, `1.7rem`, generous top margin — **no** border-bottom
- Body H3: Source Serif 700, `1.35rem`
- Body H4: Source Serif 600, `1.1rem`, `var(--text-soft)`
- Body copy: Source Serif, `1.25rem`, `line-height: 1.7`, `var(--text)`
- Metadata / UI / tags: system sans, `.72–.85rem`; uppercase labels use `letter-spacing: .06–.08em`

### The Accent Color

The platform uses **one** accent green — `#1a8917` — shared by the root hub and every publication. It appears in:
- Tags, the "Read publication" CTA, and card eyebrows
- The reading progress bar
- Blockquote left borders (the "Key takeaway" callouts)
- Links on hover (text + underline)
- The read-time arrow

**Rule:** Do not reintroduce a per-publication accent registry or a dark theme. The shipped design consolidated to a single green for brand cohesion. If a future publication genuinely needs its own accent, scope it as a CSS-variable override **inside that publication's `index.html` only** — never globally, and never as a dark surface.

### Prohibited Design Choices

The following are banned across the entire platform:

- ❌ Pure black (`#000`) text or pure-black backgrounds — use `--text` / `--bg`
- ❌ A second webfont beyond Source Serif 4 — sans and mono stay native
- ❌ Dark-theme surfaces — the platform is a light reading theme
- ❌ Purple / neon gradients, or "electric" colors as a primary surface
- ❌ Full-bleed hero images or background photographs
- ❌ Emoji in navigation, headings, or tags
- ❌ Sticky table-of-contents sidebars (use the reading progress bar instead)

---

## 4. Creating a New Publication — Full Playbook

Follow these steps in order. Do not skip steps. Do not reorder.

---

### Step 1 — Define the Publication

Before writing a line of HTML, answer these four questions. Write the answers down; they drive every subsequent decision.

1. **Topic:** What is the precise domain? (e.g., "Production failure analysis in distributed systems" — not "systems" in general)
2. **Reader:** Who reads this? What do they already know? What brings them here?
3. **Tone:** Pick one from this list and commit: `analytical`, `narrative`, `tutorial`, `polemical`, `investigative`. The publication's README will enforce this tone on every article.
4. **Personality:** The platform ships a single shared green accent (`#1a8917`) and a single shared serif (Source Serif 4); a new publication differentiates through its topic, tone, and voice — not a new color or typeface. (If a distinct accent is truly warranted, scope it as a CSS-variable override inside that publication's `index.html` only.)

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
sticky nav bar (frosted: translucent white + backdrop-blur)
  - Publication title (left, Source Serif 700)
  - "All Articles" link (right, system sans, links to home)

hero section
  - kicker line: "Journal of [Domain] · Vol. N" (system sans, accent, uppercase)
  - masthead: publication title (Source Serif 700, clamp 3rem–6rem)
  - tagline: what this publication covers (Source Serif, var(--text-soft))
  - meta row: Issue number · Month Year · N articles (system sans, var(--text-faint))

articles section
  - section label: "Latest Issue" (system sans, uppercase, faint)
  - featured card (latest article with `featured: true`)
  - article grid (remaining articles, auto-fill columns)

article page (rendered on #read= hash)
  - back button: "← All Articles"
  - article header: tags, title, subtitle, byline (Author · Date · Read time)
  - article body: full markdown; the opening deck paragraph is styled as a
    standfirst (larger, var(--text-soft))
  - reading progress bar: 3px, fixed top, accent color

Note: the shipped design has NO blinking cursor and NO dot-grid texture — the
masthead is a clean Source Serif wordmark on white.
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
    author:   'Ajay Sharma',             // optional — defaults to 'Ajay Sharma'
    readTime: 'N min read'               // null/omit to auto-calculate (~220 wpm)
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
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/github.min.css">
```

Configure `marked` with:
- A custom code renderer that uses `hljs.highlight()` with language detection
- A custom link renderer that opens external URLs in a new tab
- `gfm: true`, `breaks: false`

#### 3d. Article Body Typography Rules

These CSS rules must appear in every publication `index.html` and must not deviate:

```css
/* Body is Source Serif at a comfortable reading size */
.article-body {
  font-family: var(--serif); font-size: 1.25rem;
  line-height: 1.7; color: var(--text);
}

/* The opening deck reads as a standfirst / editorial lead */
.article-body > p:first-child {
  font-size: 1.4rem; line-height: 1.5; color: var(--text-soft);
}

/* H2s use generous spacing, NOT a border — clean, airy hierarchy */
.article-body h2 { font-size: 1.7rem; font-weight: 700; margin: 3rem 0 1rem; }

/* Blockquotes are the accent "Key takeaway" callouts: a 3px green rule,
   italic, no background fill */
.article-body blockquote {
  border-left: 3px solid var(--accent);
  padding: .25rem 0 .25rem 1.75rem; margin: 2rem 0;
}
.article-body blockquote p { font-style: italic; color: var(--text-soft); }
.article-body blockquote strong { color: var(--text); }

/* Inline code: soft red on the code-surface tint */
.article-body :not(pre) > code {
  font-family: var(--mono); background: var(--bg-code);
  color: #c7254e; border: 1px solid var(--border);
  padding: .15em .4em; border-radius: 4px;
}

/* Code blocks: light github theme, rounded 8px */
.article-body pre {
  background: var(--bg-code); border: 1px solid var(--border);
  border-radius: 8px; padding: 1.25rem 1.5rem;
}

/* Tables use the system sans — data should read as data */
.article-body table { font-family: var(--sans); font-size: .92rem; }

/* List markers are faint, not accent */
.article-body li::marker { color: var(--text-faint); }
```

#### 3e. Card Hover Effect

The signature interaction is a subtle lift, applied to the featured card and all article / publication cards:

```css
.featured, .article-card {
  border: 1px solid var(--border); border-radius: 12px;
  transition: border-color .2s, box-shadow .2s, transform .2s;
}
.featured:hover, .article-card:hover {
  border-color: var(--border-2);
  box-shadow: 0 12px 28px rgba(0,0,0,.07);
  transform: translateY(-2px);
}
```

On hover the border darkens slightly, a soft shadow appears, and the card rises 2px; the read-more arrow nudges right (`translateX(3px)`). There is no accent side-bar.

#### 3f. Reading Progress Bar

```html
<div id="progress"></div>
```

```css
#progress {
  position: fixed; top: 0; left: 0;
  height: 3px; width: 0%;
  background: var(--accent);
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
<a class="pub-card" href="./{publication-slug}/">
  <div class="pub-vol">Vol. {N} &middot; {Year}</div>
  <div class="pub-title">{Publication Title}</div>
  <p class="pub-desc">
    {One or two sentences. What does this publication cover?
    Who is it for? Keep it under 25 words.}
  </p>
  <div class="pub-tags">
    <span class="pub-tag">{Tag One}</span>
    <span class="pub-tag">{Tag Two}</span>
    <span class="pub-tag">{Tag Three}</span>
  </div>
  <span class="pub-cta">Read publication <span class="pub-arrow">&#8594;</span></span>
</a>
```

No per-publication accent CSS is required — the root uses the single green accent (`var(--accent)`) for every card's eyebrow, tags, and CTA. A not-yet-live publication uses a `coming-soon` modifier card with a `coming-badge` instead of a CTA:

```html
<div class="pub-card coming-soon">
  <div class="pub-vol">Coming soon</div>
  <div class="pub-title">{Title} <span class="coming-badge">Soon</span></div>
  <p class="pub-desc">{One or two sentences.}</p>
  <div class="pub-tags"> ... </div>
</div>
```

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
**Accent color:** `#1a8917` green (shared platform accent)
**Body font:** Source Serif 4 (shared platform serif)
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
- [ ] The article header shows the byline: Author · Date · Read time
- [ ] The card lift/shadow hover effect works at all viewport widths
- [ ] Hash navigation works: `#` loads home, `#read=filename.md` loads article
- [ ] Source Serif 4 loads from Google Fonts; system sans/mono render with no extra requests
- [ ] The single green accent (`--accent`) is used consistently; no dark-theme surfaces were introduced
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
- [ ] Accent color is used consistently: tags, progress bar, blockquote borders, link hover, card eyebrow/CTA
- [ ] `var(--text)`, `var(--text-soft)`, `var(--text-faint)` are used for text hierarchy — never hardcoded grays
- [ ] Source Serif 4 is the only loaded webfont; sans/mono use the native system stacks
- [ ] `max-width: var(--max)` (720px) is applied to the article body
- [ ] `max-width: var(--wide)` (1100px) is applied to full-width sections
- [ ] The light theme is preserved — white background, `--text` (not pure black) copy

---

## 8. Existing Publications

### Agentic — `agentic/`

| Property | Value |
|---|---|
| **URL** | `https://SharmaAjay19.github.io/blog/agentic/` |
| **Accent** | `#1a8917` green (the shared platform accent) |
| **Body Font** | Source Serif 4 (shared platform serif) |
| **Tone** | Analytical |
| **Mission** | Deep technical architecture analysis grounded in production agentic AI systems experience |
| **Articles** | 3 |
| **Writing Guide** | `agentic/README.md` |

**Current articles:**
- `agentic_context_engineering.md` — "The RAM Doesn't Lie: Engineering Context as the Primary Lever for Reliable AI Agents"
- `trust_but_verify.md` — "Trust but Verify — Except Nobody Built the Verify Part" (Part 1 of 2)
- `building_the_verify_part.md` — "Building the Verify Part" (Part 2 of 2)

---

## A Final Note for AI Agents

This README is the source of truth. When in doubt, re-read it. When two rules appear to conflict, the stricter rule wins. When something is genuinely not covered, document the decision you made and why, so this README can be updated.

The goal of this platform is not to ship content quickly. It is to ship content that is worth reading. Every shortcut taken in process produces an article the reader will not finish. Every design compromise produces a page the reader will not trust. The rules exist because they were not obvious until they were.

---

*Last updated: March 2026*
