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
