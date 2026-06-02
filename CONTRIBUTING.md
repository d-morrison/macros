# Contributing

## `macros.qmd` must contain only macro definitions — no comments

`macros.qmd` is consumed raw across four different paths, and no comment
syntax works in all of them:

| Path | How `macros.qmd` is used | `%` comment | `<!-- -->` comment |
|---|---|---|---|
| `macros-table.qmd` (MathJax embed + searchable table) | parsed line-by-line in R | stripped, OK | stripped, OK |
| `macros-header.html` loader (HTML / RevealJS) | parsed line-by-line in JS | skipped, OK | skipped, OK |
| `include-in-header: macros.qmd` (PDF) | inserted raw into the **LaTeX preamble** | OK (LaTeX comment) | **breaks** (`Missing \begin{document}`) |
| `{{< include macros.qmd >}}` shortcode (PDF) | included as **markdown body**, run through pandoc | **breaks** (`Missing $`; pandoc emits the prose + stray `\commands`) | OK (pandoc drops it) |

Because the two PDF strategies have opposite requirements, **the only safe
choice is no comments at all**. Keep `macros.qmd` as a flat list of `\def` /
`\providecommand` definitions. Put any explanatory notes here instead.

## Adding a new estimand macro group

When you add a new estimand macro group (probability, risk, odds, rate,
hazard, ratio/factor types, …) near the top of `macros.qmd`, add the matching
estimator (hat-wrapped) versions in the estimator section at the bottom,
following these rules:

1. **Zero-arg:** `\def\hfoo{\hat{\foo}}`
2. **One-arg function:** `\providecommand{\hfoof}[1]{\hfoo\paren{#1}}`
3. **One `\def` alias per estimand alias, pointing at the canonical estimator:**
   `\def\hfooalias{\hfoo}` (zero-arg) and `\def\hfoofalias{\hfoof}` (function
   form). For example, `\risk`/`\prev` are estimand aliases of `\prob`, so
   `\hrisk`/`\hprev` are defined as `\def\hrisk{\hprob}` / `\def\hprev{\hprob}`.
4. **Add an entry for every new macro to `interpretations.tsv`** — the
   `macros-table.qmd` build fails if any macro is missing an interpretation.

See [issue #58](https://github.com/d-morrison/macros/issues/58) for background
and motivation on the estimator macros.

## Before pushing

Render the site and both demos to make sure nothing broke across the four
consumption paths above:

```bash
quarto render                                  # whole site (HTML)
quarto render demo-include-in-header.qmd --to pdf
quarto render demo-shortcode.qmd --to pdf
```
