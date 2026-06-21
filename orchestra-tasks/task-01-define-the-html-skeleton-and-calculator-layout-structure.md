# Define the HTML Skeleton and Calculator Layout Structure

## Outcome
A new file `calculator.html` exists at the root of the repository. It contains a complete, unstyled HTML scaffold for a web calculator: a display area and a full button grid covering digits 0–9, operators (`+`, `-`, `*`, `/`), decimal point (`.`), equals (`=`), clear (`C`), and backspace (`⌫`). Every button has either a `data-value` or `data-action` attribute for later targeting by CSS and JavaScript. No styles and no scripts are included.

## Why
This is the foundational markup layer for the web calculator. Separating the HTML structure from styling and logic allows each concern to be implemented and reviewed independently. Subsequent tasks that add CSS layout and JavaScript behavior depend on stable, well-identified element IDs and data attributes established here.

## Context
The repository is `fugue-poc-calculator`. Its README describes Goal 2 as building a web calculator as a single self-contained HTML file using vanilla JS and CSS only — no framework, no build step. This task produces only the HTML scaffold; styling and logic are handled in later tasks.

There are currently no source files in the repository beyond `README.md`. This is the first file to be created.

The calculator must support:
- **Digits:** `0` through `9`
- **Operators:** `+`, `-`, `*`, `/`
- **Decimal point:** `.`
- **Equals:** `=`
- **Clear:** `C`
- **Backspace:** `⌫`

Conventions to follow:
- Digit and operator buttons that produce input values use `data-value="<character>"` (e.g., `data-value="7"`, `data-value="+"`)
- Action buttons (clear, backspace, equals) use `data-action="<action-name>"` (e.g., `data-action="clear"`, `data-action="backspace"`, `data-action="equals"`)
- The display area has `id="display"` so it can be targeted unambiguously
- The button grid container has `id="button-grid"`
- The top-level calculator container has `id="calculator"`
- Buttons use the standard `<button>` element
- The document uses semantic HTML5 (`<!DOCTYPE html>`, `<main>`, etc.)

A standard calculator grid layout groups buttons as follows (row by row):
```
C   ⌫   /   *
7   8   9   -
4   5   6   +
1   2   3   =  (= spans two rows or is placed beside 0)
0   .
```
A common arrangement places `=` spanning the last two rows on the right, with `0` spanning two columns on the bottom left. The exact visual spanning is not required at this stage (no CSS), but the DOM order should reflect a logical left-to-right, top-to-bottom reading of the grid.

## Scope

| File | Action |
|---|---|
| `calculator.html` | Create |

## Out of Scope
- No CSS or `<style>` blocks — zero styling
- No JavaScript or `<script>` blocks — zero logic
- No external dependencies, CDN links, or framework references
- No keyboard event handling (belongs in a later task)
- No `calc.js` or any other file (those belong to Goal 1, the CLI calculator)
- No MCP server files (those belong to Goal 3)

## Acceptance Criteria

1. A file named `calculator.html` exists at the root of the repository.
2. Opening `calculator.html` in a browser renders without errors in the browser console.
3. The document contains a `<!DOCTYPE html>` declaration and valid HTML5 structure (`<html>`, `<head>`, `<body>`).
4. An element with `id="calculator"` wraps the entire calculator UI.
5. An element with `id="display"` is present inside `#calculator` and is visually rendered (even if unstyled and empty).
6. An element with `id="button-grid"` is present inside `#calculator` and contains all calculator buttons.
7. Exactly one button exists for each of the following: `C`, `⌫`, `/`, `*`, `7`, `8`, `9`, `-`, `4`, `5`, `6`, `+`, `1`, `2`, `3`, `=`, `0`, `.` — totalling 18 buttons.
8. Each digit button (`0`–`9`) and operator button (`+`, `-`, `*`, `/`, `.`) has a `data-value` attribute whose value matches the character displayed on the button.
9. The `C` button has `data-action="clear"`.
10. The `⌫` button has `data-action="backspace"`.
11. The `=` button has `data-action="equals"`.
12. No `<style>` or `<link rel="stylesheet">` elements are present anywhere in the document.
13. No `<script>` or `<noscript>` elements are present anywhere in the document.
14. The DOM order of buttons reads logically top-to-bottom, left-to-right: `C`, `⌫`, `/`, `*`, then `7`–`9`, `-`, then `4`–`6`, `+`, then `1`–`3`, `=`, then `0`, `.`.