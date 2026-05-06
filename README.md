# macros

This repository contains LaTeX macros for mathematical notation used in statistical and regression modeling, originally from the [rme repository](https://github.com/d-morrison/rme).

## Contents

- `macros.qmd`: LaTeX macro definitions for use in Quarto documents

These macros provide convenient shorthand for common mathematical expressions used in:
- Probability and statistics
- Regression models (linear, logistic, Poisson, survival analysis)
- Maximum likelihood estimation
- Mathematical notation (vectors, matrices, derivatives, etc.)

## Using as a Git Submodule

### Adding the submodule to your project

From the root of your project repository, run:

```bash
git submodule add https://github.com/d-morrison/macros.git macros
git commit -m "Add macros submodule"
```

This will clone the `macros` repository into a `macros/` subdirectory and record it as a submodule.

### Cloning a project that already uses this submodule

When cloning a repository that includes this submodule, initialize and fetch the submodule content with:

```bash
git clone --recurse-submodules <your-repo-url>
```

Or, if you have already cloned the repository without `--recurse-submodules`:

```bash
git submodule update --init
```

### Updating the submodule

To pull the latest changes from this repository into your project:

```bash
git submodule update --remote macros
git commit -m "Update macros submodule"
```

### Including the macros in a Quarto document

There are two main strategies for loading these macros into Quarto output:

- **`include-in-header`** (recommended for multi-format projects and Quarto books): Inserts the macro file verbatim into the document header across all output formats. Configure once in `_quarto.yml` to apply project-wide.
- **`include` shortcode** (convenient for single documents): Processes and embeds the macro file inline at the point of insertion. No YAML changes required, but must be added to each `.qmd` file individually.

**Tip:** Using `\providecommand` instead of `\newcommand` in macro definitions avoids "already defined" errors when macros are included in multiple files (e.g., in a Quarto book where each chapter is a separate `.qmd`). The macros in this repository follow this convention.

#### Via `include-in-header`

In your Quarto document's YAML front matter, reference the `macros.qmd` file using the `include-in-header` or via `_quarto.yml`:

```yaml
---
include-in-header:
  - macros/macros.qmd
---
```

Or, in a shared `_quarto.yml` configuration file:

```yaml
format:
  html:
    include-in-header:
      - macros/macros.qmd
  pdf:
    include-in-header:
      - macros/macros.qmd
```

#### Via the Quarto `include` shortcode

Alternatively, you can load the macros inline using Quarto's [`include` shortcode](https://quarto.org/docs/authoring/includes.html). Place the following block at the top of your `.qmd` file (after the YAML front matter):

````qmd
::: {.hidden}
$$
{{< include macros/macros.qmd >}}
$$
:::
````

The `$$...$$` delimiters cause MathJax to process the macro definitions and make them available throughout the document. The outer `::: {.hidden}` div hides the block from view.

This approach is useful when you prefer to load macros at the document level without modifying YAML front matter or `_quarto.yml`.

**Note for RevealJS:** placing the `{{< include >}}` block before the first heading creates a blank slide. In that case, prefer the `include-in-header` approach above. See [quarto-dev discussion #8376](https://github.com/orgs/quarto-dev/discussions/8376) for details.

**See also:**

- [quarto-dev discussion #2845](https://github.com/orgs/quarto-dev/discussions/2845) — Community discussion on strategies for including LaTeX macros from a file into a Quarto book.
- [quarto-dev discussion #8376](https://github.com/orgs/quarto-dev/discussions/8376) — Follow-up discussing limitations of the `include` shortcode in RevealJS output and the recommended `include-in-header` workaround using a MathJax configuration script.
- [Quarto docs: Equations — Custom TeX macros](https://quarto.org/docs/authoring/markdown-basics.html#:~:text=If%20you%20want%20to%20define%20custom%20TeX%20macros) — Official Quarto documentation on defining custom TeX macros for HTML output.
- [Stack Overflow: How to define LaTeX macros globally for a Quarto book?](https://stackoverflow.com/questions/76398554/how-to-define-latex-macros-globally-for-a-quarto-book) — Detailed community discussion covering both HTML and PDF strategies, including the `\providecommand` trick for avoiding XeTeX redefinition errors in multi-file books.
