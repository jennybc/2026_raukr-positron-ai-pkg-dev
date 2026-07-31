# raukr 2026

Workshop materials for two sessions taught by Jenny Bryan as part of [RaukR: Data Science with R](https://nbisweden.github.io/raukr-2026/), 2026 August 10 - 20, Visby, Sweden.

* Introduction to Positron and Posit Assistant (half day, August 10)
* Introduction to R package development (half day, August 12)

Quarto website, deployed to <https://jennybc.github.io/2026_raukr-positron-ai-pkg-dev/> from <https://github.com/jennybc/2026_raukr-positron-ai-pkg-dev>.

## Status

* **Package development content is done.** `pkg-dev/index.qmd` plus modules `01-setup.qmd` through `08-share.qmd`, and the slide deck `slides/pkg-dev.qmd`. Adapted from [Andy Teucher's posit::conf(2023) workshop](https://github.com/posit-conf-2023/pkg-dev), rewritten for Positron, built around the libminer example.
  * Do not pad the module pages. Jenny does the same work as a live demo and presents from slides, so the pages are intentionally terse. They exist so learners can copy/paste and check exact commands.
  * The deck is good enough. Jenny will polish it right before the workshop if there is time. A few `<!-- TODO -->` comments in it mark her open questions; leave them for her.
* **In progress: the Positron and Posit Assistant session.** `positron/index.qmd` is an annotated inventory of the existing material, written so that it stands on its own as a useful set of pointers for learners. The open design question is how much to inline here versus link out to the Positron workshop site; see "Sources for the Positron material" below.

## Operating notes

- **Never run git write commands on Jenny's behalf, including `git add`.** She does all her own staging, commits, and PRs. Leave edits in the working tree and stop. Read-only git (`log`, `status`, `diff`) is fine. Same for anything that publishes or reshapes the remote (`quarto publish`, `gh workflow run`, `gh repo rename`): hand Jenny the command instead of running it.
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
  - The slide deck is linked twice on purpose: a "Slides" entry at the top of the Package development sidebar section, and a link at the top of `pkg-dev/index.qmd`. Keep both in sync if the deck moves.
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

## Live workflow (day of the workshop)

How Jenny actually presents. Constraints: she is constantly switching between slides, Positron, and a web browser, so she does **not** present fullscreen; and she wants to present from a local file, not the deployed site.

**Build the presenting copy**, from the repo root:

```sh
quarto render slides/pkg-dev.qmd -M embed-resources:true
```

That writes `_site/slides/pkg-dev.html` as one self-contained file, ~7 MB, with images base64-inlined and reveal's CSS and JS embedded. Copy it anywhere; it works with no server and no network. Regenerate after any edit; it is a snapshot.

Do **not** put `embed-resources: true` in the deck's YAML. It would apply to the CI build too and bloat the deployed page. Keep it a flag.

**Presenting**

- Deck goes in its own browser window, not a tab among many, so window switching never exposes the tab bar. Consider a dedicated macOS Space holding just the deck, Positron, and the demo browser.
- Windowed is fine: reveal scales its canvas to the window. The nine full-bleed 16:9 workflow slides letterbox more than text slides in a non-16:9 window, so eyeball those at the real presenting size beforehand.
- Reveal keys: <kbd>Esc</kbd> or <kbd>o</kbd> overview, <kbd>m</kbd> slide menu, <kbd>f</kbd> fullscreen, <kbd>?</kbd> help. `slide-number: true` is on, so slides are citable by number.
- Overview is a single horizontal strip, not a grid, because `navigation-mode` is the default `linear`. Switching to `vertical` or `grid` would give a real light-table view at the cost of two-axis navigation while presenting. Not done.
- No speaker notes exist, so <kbd>s</kbd> opens an empty notes pane.
- `?print-pdf` appended to the URL gives a print view, useful as an offline PDF backup. No PDF is committed, on purpose, since the deck will keep changing.

**Students** reach the deck from the workshop site: the "Slides" sidebar entry or the link atop `pkg-dev/index.qmd`. They also have the module pages for copy/paste.

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

## Sources for the Positron material

Public links live in `positron/index.qmd`. Recorded here are the local paths and internal resources, which are deliberately **not** on the published page since they would be dead links for students.

**`posit-dev/positron-workshop`**, cloned at `~/rrrchive/positron-workshop`, up to date with `origin/main` as of 2026 July 31 (latest upstream commit is useR-era, Aug 2025). Jenny is one of the owners, so updating it or bringing content into this repo are both options.

* Multi-event Quarto site: one landing page per event (`raukr.qmd`, `caskdr.qmd`, `uscots.qmd`, `useR.qmd`) over a shared set of `modules/*.qmd`. RaukR 2025 (June 11) already has a page. The site is built for exactly this, so adding a 2026 event page there is one natural option, keeping `pos.it/ron-raukr` pointed at it.
* **The module pages are nearly empty**, 4 to 17 lines each. Every one is just a title plus `{{< include _slides.md >}}`, which embeds a PDF in an iframe. All the actual content is in the decks. Do not mistake the thin pages for thin material.
* Slides are built in **Keynote**, exported to PDF, and the PDFs are committed to `slides/`. `*.key` is gitignored; the decks live in a Google Drive folder, downloaded by the untracked `get-slides.R` helper.
* `modules/03-organize.qmd` sends learners to the example project repo `posit-dev/positron-workshop-project`, cloned at `~/rrrchive/positron-workshop-project` (fruit.csv, debugging exercises, an Air practice file, a hello.qmd). In practice the 2025 deliveries barely reached it, so it is deliberately **not** mentioned on `positron/index.qmd`. Do not add it back without asking.
* `modules/02-explore.qmd` has a **Positron bingo card** activity, with per-learner unique cards in `_bingo/` and answers in `bingo-solutions/`.
* **Assistant coverage exists, but only in the decks.** Grepping the `.qmd` files for assistant, copilot, or AI returns nothing, which is misleading: the pages are shells and all content is in the slides. `07-beyond` (useR version) devotes about 5 of its 17 slides to Positron Assistant, framed as one of three "elephants in the room" alongside Python and the future of RStudio. That content is from August 2025 and is now dated (it says chat is Anthropic Claude only with a user-supplied API key), so it needs refreshing rather than inventing from scratch.

**Keynote sources**, at `~/work/2025_positron-tutorial/slides/`, one directory per delivery:

* `2025-06-11_raukr-as-delivered/`: 6 decks, `.key` plus `.pdf`. Same audience, same format as 2026, so this is the closest match.
* `2025-08-08_useR-as-delivered/`: 7 decks, `.key` only, adds `07-beyond`. The newest and most developed version.
* Also `2025-07-17_uscots-as-delivered/` (Mine and Hadley's version) and `2025-08-08_useR-under-development/`.
* Read these with the JXA PDFKit trick noted above, or unzip the `.key` for its `Data/` assets.
* **The useR directory has `.key` only, no PDF exports.** The PDFs committed in `~/rrrchive/positron-workshop/slides/` are dated Aug 7 2025 and include `07-beyond.pdf`, so they *are* the useR-era exports and are the easy way to read this content. One caveat: `05-r.key` is dated Aug 8, a day after that export, so the `05-r.pdf` may be one revision behind what was actually presented.

**Elsewhere in `~/work/2025_positron-tutorial/`:**

* `lionel-air-talk/`: a full Quarto revealjs talk on Air, "Donnez de l'Air à votre R" by Lionel Henry and Davis Vaughan, with `index.qmd` source, figures, a custom theme, and five screen-recording demos in `videos/` (autobracing, if-else, line-breaks, manual-workflow x2). Directly useful if Air gets covered in the Positron session, which is the assumption behind the Air `<!-- TODO -->` in `slides/pkg-dev.qmd`. Its closing "How do I use Air? / How do I get Air?" slides are the practical part. `talk.zip` in the parent directory is a zipped duplicate.
* `slide-resources/`: `06_positron-tutorial-base-deck.key`, the shared Keynote base deck the module decks were built from, plus `timer-videos/` and a `kth/` directory.
* `useR/`: unrelated scraped markdown from March 2025 (`appsilon.md`, `heiss.md`), not workshop content.

**Ryan's short intro to Posit Assistant**, Google Slides: <https://docs.google.com/presentation/d/1GT1W22iAjQYTNRYU_ML9_y1rTPxKSZOtVoCF7iXzpTI/edit>. The slides are sparse by design; Ryan also shared a **video** of himself presenting them, so the narration is where the detail lives. Worth watching before drawing on this.

**Posit Academy, Introduction to Positron**, three self-paced courses by the same colleagues: <https://academy.posit.co/path/introduction-to-positron>.

**Julia Silge's `applied-stats-byu-2026`**, <https://github.com/juliasilge/applied-stats-byu-2026>. Two full days, considerably more advanced and intensive than this half day. Topically relevant, but pitched at a different level, so it is a reference rather than a source to adapt. Not cloned locally.

**Positron documentation**, cloned at `~/work/positron-website`. The Assistant docs are `assistant*.qmd`, about a dozen pages, published at `https://positron.posit.co/assistant*.html`. Note the stale `_positron-assistant-deprecated.qmd`; ignore it.

**Timing for 2026:** the half day is two 1.5 hour sessions, and the first one also carries the overall introduction to the two-week workshop, so plan the material against roughly 2.5 hours. The balance favors Positron, with Posit Assistant getting a smaller but real share.

## Deployment

`.github/workflows/publish.yml` renders on push to `main` and publishes to `gh-pages` via `quarto-dev/quarto-actions/publish@v2`. Built output is never committed.

- Pinned: `actions/checkout@v7` and `quarto-dev/quarto-actions/{setup,publish}@v2`. `setup@v2` installs the latest Quarto, so CI can drift from Jenny's local version. Left floating on purpose; pin with `with: {version: X.Y.Z}` if it causes trouble.
- The workflow requires a `gh-pages` branch to already exist and fails outright if it is missing. `usethis::use_github_pages()` creates it and configures Pages, but renders nothing, so the site 404s until the publish workflow runs once.
