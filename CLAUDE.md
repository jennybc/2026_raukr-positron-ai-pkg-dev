# raukr 2026

Workshop materials for two sessions taught by Jenny Bryan as part of [RaukR: Data Science with R](https://nbisweden.github.io/raukr-2026/), 2026 August 10 - 20, Visby, Sweden.

* Introduction to Positron and Posit Assistant (half day, August 10)
* Introduction to R package development (half day, August 12)

## Status

* Site builds and deploys end to end. Repo is <https://github.com/jennybc/2026_raukr-positron-ai-pkg-dev>, live at <https://jennybc.github.io/2026_raukr-positron-ai-pkg-dev/>.
* **Package development content is done.** `pkg-dev/index.qmd` plus modules `01-setup.qmd` through `08-share.qmd`. Adapted from [Andy Teucher's posit::conf(2023) workshop](https://github.com/posit-conf-2023/pkg-dev), rewritten for Positron, built around the libminer example. Do not pad these pages! Jenny will be doing the same work as a live demo and will be presenting from slides, so these pages are intentionally terse. They exist to help learners copy/paste what Jenny is doing or to get a reminder of exact command to execute.
* A revealjs deck for the pkg-dev session lives in `slides/`. Bare bones on purpose: default theme, no custom SCSS, no partials, no extensions.

**Next up: the Positron and Posit Assistant session.** `positron/index.qmd` is still a stub that links out to <http://pos.it/ron-raukr>. We are still considering whether to inline this content.

## Operating notes

- `quarto preview` is typically started by Jenny in a separate terminal, not by Claude. Wiping `_site/` and `.quarto/` is the right move when stale builds cause weirdness (e.g., old font imports lingering after SCSS changes).
- Local-only workflow: there is no `.devcontainer/` and no Codespaces wiring on purpose.
- Jenny makes her own commits and PRs; never commit or push on her behalf, stage changes and stop there. Same for anything that publishes or reshapes the remote (`quarto publish`, `gh workflow run`, `gh repo rename`): hand Jenny the command instead of running it.

## Per-user preferences

- No em dashes in any file content, commit messages, or PR text (shareable artifacts). Use commas, semicolons, parens, or two sentences. En dashes and hyphens are fine.
- Jenny prefers iterating in small steps with previews; do not make large structural changes without checking in first.
- Keep the Quarto usage simple. When in doubt, ask or pick the simpler approach.

## Site conventions

- `_quarto.yml`: website config; theme is `[zephyr, footer.scss]`. Output dir is the Quarto default `_site/`, which is gitignored.
  - `render:` is an inclusive list, so stray `.md` files never become pages and need no `!` exclusions.
  - `resources:` lists `pkg-dev-2023.pdf` so the PDF gets copied into the built site. Consider removing once we are happy with the slide deck here.
  - Navigation is a sidebar (not a navbar). Contents: Home, a divider, then the two sessions in chronological order.
    - A session with no module pages is a plain `href:`. A session with module pages is a `section:` carrying its own `href:` to the landing page, so the section title doubles as that link, with the module files listed **explicitly** under `contents:`. Add each new module by hand, and promote a session from `href:` to `section:` when it gains its first module.
    - List modules explicitly rather than using `auto:`. An `auto:` glob wraps its matches in an extra section titled from the directory name, which shows up as a stray "Pkg Dev" entry.
    - `collapse-level: 1` so sections start collapsed on the home page and auto-expand when you are on a page inside them.
- `positron/_metadata.yml` and `pkg-dev/_metadata.yml`: shared front matter for the pages in each session directory (`sidebar: main`, `toc: true`), so individual pages need not repeat it.
- No R runs at render time, and CI does not install R. Show R code in plain ```` ```r ```` blocks, never executable ```` ```{r} ```` chunks.
  - To *display* chunk syntax literally, use Quarto's escape: double the braces, ```` ```{{r}} ````. This both renders as `{r}` and keeps Quarto's engine detection from firing up knitr. Needed in `pkg-dev/03-documentation.qmd`. Nesting inside a four-backtick fence is not enough on its own; the escape is what matters.
- Licensing is stated **once** on the site, in the `page-footer` of `_quarto.yml`, which links to the CC BY-NC-SA 4.0 deed. Do not add per-page license sections; they clutter every page's TOC. Root `LICENSE.md` holds the full text plus attribution to Andy Teucher.
- `footer.scss`: site SCSS. Disables zephyr's Google Fonts import via `$web-font-path: false`, plus footer layout and table-width rules. It is `.scss`, not `.css`, because it uses `$var` syntax; do not rename it back.
- `DESCRIPTION`: R dependency manifest (`Type: Workshop`).
  - The `Package:` field does not match the repo name, and cannot: R package names may not start with a digit or contain underscores or hyphens. This mismatch is intentional; do not try to "fix" it. Note `usethis::use_github()` derives the repo name from this field when a `DESCRIPTION` is present.

## Deployment

Follows <https://github.com/posit-dev/positron-workshop>: `.github/workflows/publish.yml` renders on push to `main` and publishes to the `gh-pages` branch via `quarto-dev/quarto-actions/publish@v2`. Built output is never committed.
