# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page GitHub Pages site at `mikersays.github.io/ai-git-workflow` (Pages is enabled,
serving from `main`). The page is a beginner's field guide to git for people building with
Claude Code, ending with a recommendation to install the `/ship:ship` plugin from
`mikersays.github.io/mikersays-plugins`. The repo description is *"How to use Git when
working with LLM agents."*

There is no build system, no package manager, no tests, no dependencies. The entire site
is one self-contained file: `docs/index.html`. CSS and JS are inline. Fonts come from the Google
Fonts CDN (Fraunces, Newsreader, JetBrains Mono). GitHub Pages is configured to serve from
`main:/docs` — push to `main` and Pages rebuilds automatically.

## Working on the page

- Preview locally: open `docs/index.html` directly in a browser, or `python3 -m http.server -d docs` from the repo root.
- Sanity-check after edits: `python3 -c "import html.parser; html.parser.HTMLParser().feed(open('docs/index.html').read()); print('ok')"` and `grep -c '<section' docs/index.html` should equal `grep -c '</section>' docs/index.html`.
- Ship: this repo uses the `/ship:ship` plugin (mikersays-plugins). Direct-to-`main` is the default workflow — there is no PR process. The page itself recommends `main` to readers, so keep the repo aligned.

## Editorial design system

The aesthetic is intentional and committed — *vintage field guide / Whole Earth Catalog /
workshop manual.* Avoid drift toward generic dev-doc styling. When adding new content,
match the existing components rather than inventing new ones.

Typography axes used:
- Display headings → Fraunces, with the variable `SOFT` axis (30 for solid, 100 for "soft" italic flourishes) and `opsz` 60 or 144.
- Body → Newsreader.
- Code, labels, eyebrows → JetBrains Mono with high letter-spacing (`0.18em`–`0.32em`) and uppercase.

Color palette (CSS custom properties, defined at the top of the `<style>`):
- `--paper` / `--paper-deep` / `--paper-edge` — backgrounds
- `--ink` / `--ink-soft` / `--ink-fade` — text
- `--rust` / `--rust-deep` — primary accent (most labels, highlights, links)
- `--moss` — secondary accent, used **only** for "Ask Claude" callouts and the Plain English section
- `--gold` — used **only** inside the dark cheat sheet section and the marketplace card command chip
- `--plum` — reserved for the rare "lost" margin-note label

Use the existing palette. Don't introduce new colors.

## Page structure (top to bottom)

1. `.masthead` — fixed-height header with section nav.
2. `.hero` — title, lede, `<dl class="hero-meta">` reading-time/prereqs box, decorative compass-rose SVG.
3. `.toc` — table of contents with dot-leader rows.
4. `.prologue` — mental-model section. Contains two figures: `.analogy` (Fig. 01, "the three things") and `.vocab` (Fig. 02, the layman vocabulary table).
5. `.stage` × 8 — numbered chapters `s00`–`s07`. **All stages share the same skeleton**, see the component reference below.
6. `.cheat` — dark inverted section with command reference, grouped by `.cheat-cat` headers and `.cheat-grid` of `.cheat-cell`s.
7. `.plain` — paired natural-language prompts (`.plain-say`) ↔ commands they translate to (`.plain-does`).
8. `.faq` — `<details>` accordion items.
9. `<footer>` — closing mark, rubber-stamp SVG, link list.

Inline `<script>` at the bottom drives the scroll progress bar (`.progress`, sets `--p` on scroll) and IntersectionObserver-based fade-in for `.reveal` blocks.

## Component reference (these recur — match them)

**Stage skeleton** — every `.stage` must include all of these:
```html
<section class="stage" id="sNN">
  <span class="stage-bg-num">NN</span>      <!-- huge ghosted backdrop number -->
  <div class="wrap">
    <div class="stage-grid">
      <div class="section-rubric">           <!-- left column: number + label -->
        <span class="num">NN</span> Title
        <span class="underline"></span>
        <span class="pill">~N minutes</span>
      </div>
      <div class="prose reveal">             <!-- middle column: main content -->
        <h2>...<em>italic accent</em></h2>
        ... content ...
        <div class="ornament">               <!-- always end with this -->
          <span class="glyph">✦ ✦ ✦</span>
          <span>END OF STAGE NN</span>
          <span class="glyph">✦ ✦ ✦</span>
        </div>
      </div>
      <aside class="stage-margin">           <!-- right column: notes -->
        <div class="note"><div class="label">...</div><p>...</p></div>
      </aside>
    </div>
  </div>
</section>
```
Adding a new stage means: this block + a TOC entry + a masthead nav link.

**Code blocks** — dark terminal styling. Wrap in `.code` with a `.code-head` (label + dots). Inside `<pre>`, use these span classes for syntax: `prompt` (gold `$`/`>`), `cmt` (italic muted), `str` (orange string), `kw` (red keyword/flag), `out` (output text), `var` (commit-message-like green), `arrow` (rust `→`), `tick` (green `✓`).

**`.ask` callouts** — the moss-green "Or just ask Claude" blocks. Each holds one or more italic-serif `<p>` prompts (auto-wrapped in curly quotes via CSS), followed by a `<span class="then">` line that describes what Claude actually runs. Convention: use «guillemets» around values the reader should fill in (e.g. `«your-username/my-cool-app»`). The leading "Ask Claude" label is set in CSS — don't add another one. **Prompts must include explicit URLs or full identifiers** — natural-language prompts cannot rely on shorthand the way slash commands can. For example, "Install the ship plugin from the mikersays marketplace" is wrong; "Install the ship plugin from the marketplace at `https://github.com/mikersays/mikersays-plugins`" is right. The shorthand `mikersays/mikersays-plugins` only resolves when typed literally as a slash-command argument.

**`.inline-callout`** — bordered box with a corner label set via the `data-label` attribute (uppercased letter-spaced label appears via CSS `::before`).

**Margin notes (`.note`)** — `.label` with optional modifier class: `tip` (moss + ✦), `warn` (rust + ⚠), `lost` (plum + ❋). Default has no modifier and uses ◆.

**Figure boxes** — `.analogy`, `.vocab`, `.loop-figure > .loop`, `.marketplace-card`. Each carries a corner label set in the CSS `::before` — for new figures, follow the `FIG. NN — TITLE` pattern.

**Cheat sheet** and **Plain English** — both grouped by category headers (`.cheat-cat` / `.plain-cat`) using lowercase roman numerals (`i.`, `ii.`, …). Entries in each pair: `.cheat-cell` has `.cmd` + `.desc`; `.plain-row` has `.plain-say` (italic serif, the prompt) + `.plain-does` (mono, what runs, with a `.head` label).

## Content invariants

- **The audience is solo builders, not teams.** No pull-request rituals, no code-review etiquette, no "your teammate pushed a conflict" scenarios. Frame all explanations around solo motivations: yesterday-you vs today-you, AI making fast changes you need to see, your laptop ⇄ desktop sync, GitHub as a backup against drive death, commit messages as notes from past-you to future-you. The prologue's "Why git, when it's just you?" sub-section is the load-bearing piece — keep new content consistent with it.
- The reader starts as a complete beginner. Use the analogies established in the prologue (game saves, "Save As" in Word, Google Drive sync, iCloud "do not back up") rather than introducing new technical metaphors.
- The `/ship:ship` workflow is the recommended path throughout. When adding examples, prefer prompting Claude over hand-typed git commands wherever both work.
- Default branch is `main`. The page's Stage 01 sets this via `git config --global init.defaultBranch main` — keep the rest of the page consistent with that (no `master` references).
- If you introduce a new git command anywhere in the prose, add the corresponding entries to **both** the cheat sheet (in the right category) and the Plain English section (paired prompt + what-runs).
