# Copilot Instructions

## Quarto rendering

Before requesting review on any change to a `.qmd` file:

1. **Always render the document locally** (`quarto render <file>` or `quarto preview`) and inspect the HTML output.
2. **Verify that math renders correctly** — open the rendered HTML and confirm MathJax typesetting is visible and correct.
3. **Do not push until rendering succeeds** and the output looks as intended.

This step is required because MathJax rendering failures are silent in R/knitr output and only visible in the browser.
