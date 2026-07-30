# raukr 2026

Workshop materials for two sessions taught by Jenny Bryan as part of [RaukR: Data Science with R](https://nbisweden.github.io/raukr-2026/), 2026 August 10 - 20, Visby, Sweden.

* Introduction to Positron and Posit Assistant (half day, August 10)
* Introduction to R package development (half day, August 12)

## Status and next steps

Done (as of 2026-07-29):

* Basic Quarto website exists, with a landing page participants can use on both days.
  - `index.qmd` is deliberately thin: welcome, a table of the two sessions linking to their landing pages, where to find Jenny online.
  - The two sessions are peers, one directory each: `positron/` and `pkg-dev/`, each with an `index.qmd` landing page and a `_metadata.yml`.
  - `positron/index.qmd` is a stub that links out to <http://pos.it/ron-raukr>.
* Build and deploy work end to end. Repo is <https://github.com/jennybc/2026_raukr-positron-ai-pkg-dev>, site is live at <https://jennybc.github.io/2026_raukr-positron-ai-pkg-dev/>. All pages plus the PDF were verified to return 200.

Next steps:

* Inline package development content adapted from Andy Teucher's workshop offered as posit::conf(2023).
  - Specifically, bring the content of https://posit-conf-2023.github.io/pkg-dev/ into this repo. Source can be found at https://github.com/posit-conf-2023/pkg-dev.
  - Work incrementally, roughly a module at a time, so Jenny can preview as we go. First get a working version of the site including all of the package development material, then start to adapt it.
  - `pkg-dev/01-whole-game.qmd` is currently a bare placeholder, listed explicitly in the sidebar `contents:`. Replace it with real content.
* Create a Quarto slide deck that replicates the content of https://posit-conf-2023.github.io/pkg-dev/materials/slides.pdf (also in this repo as `pkg-dev-2023.pdf`). Probably use reveal.js. Jenny is not very experienced with Quarto slides and wants to keep this very basic.

## Operating notes

- `quarto preview` is typically started by Jenny in a separate terminal, not by Claude. Wiping `_site/` and `.quarto/` is the right move when stale builds cause weirdness (e.g., old font imports lingering after SCSS changes).
- Local-only workflow: there is no `.devcontainer/` and no Codespaces wiring on purpose.
- The repo is on GitHub with `origin` set. Jenny makes her own commits and PRs; never commit or push on her behalf, stage or prepare changes and stop there. Same goes for anything that publishes or reshapes the remote (`quarto publish`, `gh workflow run`, `gh repo rename`): hand Jenny the command instead of running it.

## Per-user preferences

- No em dashes in any file content, commit messages, or PR text (shareable artifacts). Use commas, semicolons, parens, or two sentences. En dashes and hyphens are fine.
- Jenny prefers iterating in small steps with previews; do not make large structural changes without checking in first.

## Website and Slide deck conventions

Jenny originally borrowed this section from a colleague. The website bullets below now describe what this repo actually does. The slide deck conventions further down are still aspirational: there is no `slides/`, `custom.scss`, `title-slide.html`, `images/`, `demo/`, or `_extensions/` in this repo yet, so treat that part as a spec to build toward, not a description of existing files.

That workshop's content is at https://github.com/juliasilge/applied-stats-byu-2026/tree/main. But when in doubt, we want to keep the Quarto usage simple! So feel free to ask questions or use simpler approaches.

Another useful workshop repo is <https://github.com/posit-dev/positron-workshop>. We might eventually inline content from there. For now, the main thing to take from here are the mechanics around deployment.

- `_quarto.yml`: website config; theme is `[zephyr, footer.scss]`. Output dir is the Quarto default `_site/`, which is gitignored.
  - `render:` is an inclusive list (`index.qmd`, `positron/*.qmd`, `pkg-dev/*.qmd`), following Julia. That way stray `.md` files never become pages and need no `!` exclusions.
  - `resources:` lists `pkg-dev-2023.pdf` so the PDF gets copied into the built site.
  - Navigation is a sidebar (not a navbar), following positron-workshop. Contents: Home, a divider, then the two sessions in chronological order.
    - A session with no module pages is a plain `href:` (currently `positron/index.qmd`). A session with module pages is a `section:` carrying its own `href:` to the landing page, so the section title doubles as that link, with the module files listed **explicitly** under `contents:` (currently "Package development"). Add each new module by hand, and promote a session from `href:` to `section:` when it gains its first module.
    - List modules explicitly rather than using `auto:`. An `auto:` glob always wraps its matches in an extra section titled from the directory name, which shows up as a stray "Pkg Dev" entry.
    - `collapse-level: 1` so sections start collapsed on the home page and auto-expand when you are on a page inside them.
- Deployment follows <https://github.com/posit-dev/positron-workshop> rather than Julia's repo: `.github/workflows/publish.yml` renders on push to `main` and publishes to the `gh-pages` branch via `quarto-dev/quarto-actions/publish@v2`. Built output is never committed.
  - Action versions: `actions/checkout@v7` (v4 emits a Node 20 deprecation warning on the runners; anything >= v5 clears it) and `quarto-dev/quarto-actions/{setup,publish}@v2`, which is the current major, with the `v2` tag pointing at the latest release. This deliberately diverges from positron-workshop, which still pins `checkout@v4`.
  - `quarto-actions/setup@v2` installs the latest Quarto release, so the CI version can drift from Jenny's local Quarto. Left floating on purpose. Pin with `with: {version: X.Y.Z}` if that ever causes trouble.
  - Gotcha, recorded in case this ever needs redoing: the workflow can only push to a `gh-pages` branch that already exists, and fails outright if it is missing. `usethis::use_github_pages()` handles that, creating an empty orphan `gh-pages` branch and configuring the Pages settings. But it does not render or populate anything, so the site 404s (while GitHub reports a successful deploy of the empty branch) until the publish workflow runs once.
- `positron/_metadata.yml` and `pkg-dev/_metadata.yml`: shared front matter for the pages in each session directory (`sidebar: main`, `toc: true`), so individual pages need not repeat it.
- Licensing is stated **once** on the site, in the `page-footer` of `_quarto.yml`, which links to the CC BY-NC-SA 4.0 deed. Do not add per-page license sections; they were repetitive and cluttered every page's TOC. Root `LICENSE.md` holds the full text plus attribution to Andy Teucher's workshop, for people reading the repo.
- `footer.scss`: site SCSS. Disables zephyr's Google Fonts import via `$web-font-path: false`, plus footer layout and table-width rules.
- `DESCRIPTION`: R dependency manifest (`Type: Workshop`). Licensing: course content (slides, prose) is CC BY-NC-SA 4.0 (root `LICENSE.md`, matching the site footer and `DESCRIPTION`).
  - The `Package:` field does not match the repo name, and cannot: R package names may not start with a digit or contain underscores or hyphens. This mismatch is intentional; do not try to "fix" it. Note `usethis::use_github()` derives the repo name from this field when a `DESCRIPTION` is present, which is why the repo was initially created with the wrong name and had to be renamed.
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
