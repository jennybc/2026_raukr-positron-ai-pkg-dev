# raukr 2026

Workshop materials for two sessions taught by Jenny Bryan as part of [RaukR: Data Science with R](https://nbisweden.github.io/raukr-2026/), 2026 August 10 - 20, Visby, Sweden.

* Introduction to Positron and Posit Assistant (half day, August 10)
* Introduction to R package development (half day, August 12)

Quarto website, deployed to <https://jennybc.github.io/2026_raukr-positron-ai-pkg-dev/> from <https://github.com/jennybc/2026_raukr-positron-ai-pkg-dev>.

## Status

* **Package development content is done.** `pkg-dev/index.qmd` plus modules `01-setup.qmd` through `08-share.qmd`, and the slide deck `slides/pkg-dev.qmd`. Adapted from [Andy Teucher's posit::conf(2023) workshop](https://github.com/posit-conf-2023/pkg-dev), rewritten for Positron, built around the libminer example.
  * Do not pad the module pages. Jenny does the same work as a live demo and presents from slides, so the pages are intentionally terse. They exist so learners can copy/paste and check exact commands.
  * The deck is good enough. Jenny will polish it right before the workshop if there is time. A few `<!-- TODO -->` comments in it mark her open questions; leave them for her.
* **Next up: the Positron and Posit Assistant session.** `positron/index.qmd` is still a stub linking to <http://pos.it/ron-raukr>. Still deciding whether to inline that content.

## Operating notes

- Jenny makes her own commits and PRs; never commit or push on her behalf, stage changes and stop there. Same for anything that publishes or reshapes the remote (`quarto publish`, `gh workflow run`, `gh repo rename`): hand Jenny the command instead of running it.
- **Run only one `quarto preview` at a time.** Because `slides/*.qmd` is in `render:`, a site-wide preview also watches the deck; two previews race to write the same output and the browser appears hung. Stop a preview with Ctrl+C in its own terminal, never `kill` on the wrapper PID, which strands the child deno server. Diagnose with `lsof -nP -iTCP -sTCP:LISTEN | grep deno`, which should show exactly one listener. Avoid running `quarto render` while Jenny has a preview up.
- Jenny usually starts `quarto preview` herself in a separate terminal. Wiping `_site/` and `.quarto/` is the right move when stale builds cause weirdness.
- Local-only workflow: no `.devcontainer/`, no Codespaces, on purpose.
- No poppler or `pdftotext` on this machine, so Read cannot open PDFs. Extract text with macOS PDFKit via JXA (`ObjC.import("Quartz")`, `PDFDocument`, `page.string`). Keynote files are zips; `tar -xf` them (`unzip` chokes on non-UTF8 filenames) and the media is in `Data/`.

## Per-user preferences

- No em dashes in any file content, commit messages, or PR text (shareable artifacts). Use commas, semicolons, parens, or two sentences. En dashes and hyphens are fine.
- Iterate in small steps with previews; do not make large structural changes without checking in first.
- Keep the Quarto usage simple. When in doubt, ask or pick the simpler approach.

## Site conventions

- `_quarto.yml`: website config; theme is `[zephyr, footer.scss]`. Output dir is the Quarto default `_site/`, gitignored.
  - `render:` is an inclusive list, so stray `.md` files never become pages and need no `!` exclusions.
  - No `resources:` key. `pkg-dev-2023.pdf` stays in the repo as a source but is deliberately not published.
  - Navigation is a sidebar, not a navbar: Home, a divider, then the two sessions in chronological order.
    - A session with no module pages is a plain `href:`. A session with module pages is a `section:` carrying its own `href:` to the landing page, with modules listed **explicitly** under `contents:`. Add each new module by hand.
    - Never use `auto:`; it wraps matches in an extra section titled from the directory name, producing a stray "Pkg Dev" entry.
    - `collapse-level: 1`.
  - The slide deck is in `render:` but deliberately **not** in the sidebar.
- `positron/_metadata.yml` and `pkg-dev/_metadata.yml`: shared front matter (`sidebar: main`, `toc: true`) for each session directory.
- No R runs at render time and CI does not install R. Show R in plain ```` ```r ```` blocks, never executable ```` ```{r} ```` chunks. To display chunk syntax literally, double the braces: ```` ```{{r}} ````. Needed in `pkg-dev/03-documentation.qmd`.
- Licensing is stated **once**, in the `page-footer` of `_quarto.yml`. Do not add per-page license sections. Root `LICENSE.md` has the full text plus attribution to Andy Teucher.
- `footer.scss` is SCSS (`$var` syntax), not CSS. Do not rename it to `.css`.
- `DESCRIPTION`: R dependency manifest (`Type: Workshop`). Its `Package:` field cannot match the repo name, since R package names may not start with a digit or contain underscores or hyphens. Intentional; do not "fix" it. Note `usethis::use_github()` derives the repo name from this field.

## Slide deck conventions

`slides/pkg-dev.qmd`, one deck for the whole pkg-dev session, with images in `slides/images/`.

- **Bare bones on purpose.** Plain `format: revealjs`, default theme, `slide-number: true`. No custom SCSS, no template partials, no `_extensions/`, no countdown shortcode. Jenny explicitly opted out of a more elaborate setup.
- `#` for section dividers, `##` for content slides. "Your turn" slides are plain bullet lists, one per module.
- Slides are the projected narrative; the `pkg-dev/*.qmd` pages are the handout. Keep slides sparse and do not paste module pages onto them.
- Where slides and module pages disagree, reconcile deliberately. They already differ on one point: the deck introduces `use_github()` right after `use_git()`, while `pkg-dev/02-create-pkg-and-metadata.qmd` still has it at the end.
- Images use `fig-alt`. The nine workflow build slides are the exception: they are full-bleed `## {background-image="..." background-size="contain"}` slides with no heading, because Jenny's exports already contain their own titles.
- Exports are 1920x1080 while revealjs defaults to 1050x700, so background images letterbox slightly. Setting `width: 1600` / `height: 900` would fix it but resizes text everywhere; not done.

## Sources for the pkg-dev material

The deck was reconstructed from these. Go back to them for a better port, more figures, or content that was skipped.

**Jenny's RaukR 2025 Keynote**, `~/work/2025_raukr-pkg-dev/pkg-dev-whole-game-raukr.key`. The single best source: her own prior version of this exact workshop, same audience, same half-day format. All current figures came from here.

* PNG exports of all 102 slides were made to `~/tmp/pkg-dev-whole-game-raukr/` (a scratch dir, may be gone; regenerate from Keynote via File > Export To > Images).
* `slides/images/workflow-1..9-*.png` are slide exports 29, 36, 51, 52, 61, 69, 70, 71, 87, the progressive build of the workflow diagram.
* `five-forms`, `load-all`, `package-files`, `pain-curve`, `release-issue` were pulled from the `.key`'s `Data/` folder. Several are figures from R Packages (2e).
* Its `Data/` folder holds ~200 assets, including reaction GIFs and more diagrams that were not used.

**Andy Teucher's posit::conf(2023) workshop**, <https://github.com/posit-conf-2023/pkg-dev>. The origin of the module structure and the libminer example.

* `pkg-dev-2023.pdf` in this repo is his 51-slide deck. Kept as a source but deliberately **not** published with the site (not in `resources:`), since `pkg-dev/index.qmd` links to his site and repo instead.
* His repo also has `materials/pkg-dev-with-notes.pdf`, 144 pages: the full deck with builds expanded and his presenter notes attached. Richer than the public PDF.
* `materials/script.qmd` is his live-coding script, punctuated with `***back to slides***` markers that show where he broke between presenting and demoing.
* There is no Quarto source for his slides; they were built in Keynote and he did not ship the `.key`.
* Not ported from him: `vignettes.qmd` (exists in his repo, absent from his sidebar), his "Bonus: factoring out an internal function" section, `use_import_from()`, `use_news_md()`, `rename_files()`.

**Jenny's posit::conf 2024 and 2025 pkg-dev decks**, in `~/rrrchive/`. Newer than the RaukR deck (Sept 2025) but a **different vibe**; do not mine them for structure or tone. The 2025 one does cover Air, which this material does not.

**[R Packages (2e)](https://r-pkgs.org)** is the book behind the whole session, especially [The Whole Game](https://r-pkgs.org/whole-game.html).

## Deployment

`.github/workflows/publish.yml` renders on push to `main` and publishes to `gh-pages` via `quarto-dev/quarto-actions/publish@v2`. Built output is never committed.

- Pinned: `actions/checkout@v7` and `quarto-dev/quarto-actions/{setup,publish}@v2`. `setup@v2` installs the latest Quarto, so CI can drift from Jenny's local version. Left floating on purpose; pin with `with: {version: X.Y.Z}` if it causes trouble.
- The workflow requires a `gh-pages` branch to already exist and fails outright if it is missing. `usethis::use_github_pages()` creates it and configures Pages, but renders nothing, so the site 404s until the publish workflow runs once.
