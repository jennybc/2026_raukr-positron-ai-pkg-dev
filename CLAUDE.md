# raukr 2026

Workshop materials for two sessions taught by Jenny Bryan as part of [RaukR: Data Science with R](https://nbisweden.github.io/raukr-2026/), 2026 August 10 - 20, Visby, Sweden.

* Introduction to Positron and Posit Assistant (half day, August 10)
* Introduction to R package development (half day, August 12)

## Status and next steps

* Create basic Quarto website Jenny can use as a landing page for workshop participants on both days.
  - `index.qmd`: landing page with welcome, sections for each topic (Positron + Posit Assistant, Package development), info on where to find Jenny online.
  - Section on Positron + Posit Assistant should just link out to existing, external material mentioned in the current README.
  - Section on package development should link out to a landing page for package dev content.
  - Give Jenny nudges to do what ever is necessary to get a GitHub Pages site up.
* Work on inlining package development content adapted from Andy Teucher's workshop offered as posit::conf(2023).
  - Specifically, bring the content of https://posit-conf-2023.github.io/pkg-dev/ into this repo. Source can be found at https://github.com/posit-conf-2023/pkg-dev.
  - Work incrementally. Let's first get a working version of Jenny's workshop site, including all of this package development material. Then we can start to adapt it.
  - We will also want to create a Quarto slide deck that replicates the content of https://posit-conf-2023.github.io/pkg-dev/materials/slides.pdf. Probably use reveal.js. Jenny is not very experienced with Quarto slides and wants to keep this very basic.

## Operating notes

- `quarto preview` is typically started by Jenny in a separate terminal, not by Claude. Wiping `docs/` and `.quarto/` is the right move when stale builds cause weirdness (e.g., old font imports lingering after SCSS changes).
- Local-only workflow: there is no `.devcontainer/` and no Codespaces wiring on purpose.
- The repo is git-initialized. Jenny makes her own commits and PRs; never commit or push on her behalf, stage or prepare changes and stop there.

## Per-user preferences

- No em dashes in any file content, commit messages, or PR text (shareable artifacts). Use commas, semicolons, parens, or two sentences. En dashes and hyphens are fine.
- Jenny prefers iterating in small steps with previews; do not make large structural changes without checking in first.

## Website and Slide deck conventions

Jenny has borrowed everything below here from a colleague.

That workshop's content is at https://github.com/juliasilge/applied-stats-byu-2026/tree/main. But when in doubt, we want to keep the Quarto usage simple! So feel free to ask questions or use simpler approaches.

- `_quarto.yml`: website config; theme is `[zephyr, footer.scss]`. Output dir is `docs/`.
- `footer.scss`: site SCSS. Disables zephyr's Google Fonts import via `$web-font-path: false`, plus footer layout and table-width rules.
- `DESCRIPTION`: R dependency manifest (`Type: Workshop`). Licensing: course content (slides, prose) is CC BY-NC-SA 4.0 (root `LICENSE.md`, matching the site footer and `DESCRIPTION`).
- Renamed `footer.scss` from `footer.css` because it contains SCSS (`$var` syntax). Do not rename back, the CSS linter will complain.

Every deck's YAML uses:

```yaml
format:
  revealjs:
    theme: [default, custom.scss]
    template-partials:
      - title-slide.html
    title-slide-attributes:
      data-background-image: "../images/undraw_<name>.svg"
      data-background-size: "auto NN%"      # tuned per illustration aspect ratio
      data-background-position: "82% 85%"   # uniform across all decks
      data-background-repeat: "no-repeat"
```

- **`title-slide.html`** is a custom Pandoc partial that wraps title / subtitle / author in `<div class="title-slide-inner">` so `custom.scss` can pin it to the top-left.
- **H1 section dividers** repeat the illustration via inline attributes: `# Heading {background-image="..." background-size="..." background-position="..." background-repeat="..."}`. Size and position must match that deck's title slide values.
- **Title and Thanks slides have no `{background-image=...}` attribute** so they show plain (no corner illustration).
- **`# Thanks!` slide** at the end of every deck repeats the contact card from deck 01's `# Hello!` slide, with undraw attribution in `::: footer :::`.

### Activity pattern ("Your turn")

Every place participants do hands-on work uses:

```markdown
## Your turn

::: {.callout-note icon=false}

## Activity

<description, optionally a lead-in plus bullets>
:::

{{< countdown minutes=N >}}
```

The `{{< countdown >}}` shortcode comes from the `gadenbuie/countdown` extension in `_extensions/`.

### Demos

Some sections use an instructor-run demo instead of (or alongside) a participant activity. On the slide this is a `## Demo` (or `## Demos`) heading inside a `::: {.callout-note icon=false}` block, with a code block or path pointing at the matching folder under `demo/`. Demo folders are prefixed by the deck number they support.

### Styling

- Site (HTML pages) uses the `zephyr` Bootswatch theme. Default zephyr colors; no custom palette right now.
- Slides use reveal.js's `default` theme + `slides/custom.scss`. The SCSS is intentionally minimal: just the title-slide layout and section-divider positioning.
- Title slide: title block pinned to the top-left via a `.title-slide-inner` wrapper (custom partial + absolute positioning). Reveal's centering is overridden with `top: 0 !important; height: 100% !important` on `#title-slide`.
- Section divider slides (any `<section>` with an H1 as a direct child) get the same centering override plus `margin-top: 5%` on the H1. The `:has()` selector keeps the rule from affecting the title slide (whose H1 is inside `.title-slide-inner`).
