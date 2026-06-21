# Add CSS Styling for the Calculator Layout and Visual Design

## Outcome
`calculator.html` contains a `<style>` block in `<head>` that gives the calculator a polished, complete visual design: a centered card on a neutral page background, a dark display area that shows input and result text clearly, and a button grid with visually distinct styles for digit buttons, operator buttons, the equals button, and utility buttons (C, backspace). Hover and active states are present on all buttons. The layout does not overflow or break at typical desktop browser window sizes.

## Why
Without CSS, the HTML skeleton renders as an unstyled collection of buttons and text — unusable and visually meaningless. The styling transforms the raw structure into a recognizable, usable calculator interface, making it possible to verify layout correctness and proceed with wiring up JavaScript logic.

## Context

### Project
This is a single-file web calculator: `calculator.html`. No framework, no build step, no external CSS files — everything lives in one HTML file. Vanilla CSS only.

### Dependency: HTML skeleton
This task depends on the HTML skeleton already being present in `calculator.html`. That prior task produces the following structure (or equivalent):

- A wrapping element (e.g. `<div class="calculator">`) acting as the card
- A display element (e.g. `<div class="display">`) inside the card, containing one or two text areas for current input and result
- A button grid (e.g. `<div class="buttons">`) containing buttons with classes or data attributes that distinguish their type:
  - Digit buttons (0–9, decimal point) — e.g. `class="btn btn-digit"`
  - Operator buttons (+, −, ×, ÷) — e.g. `class="btn btn-operator"`
  - Equals button (=) — e.g. `class="btn btn-equals"`
  - Utility buttons (C/clear, backspace) — e.g. `class="btn btn-utility"`

The CSS must target whatever class names and element structure the HTML skeleton actually uses. Inspect the existing markup in `calculator.html` before writing selectors.

### Conventions
- All CSS goes inside a `<style>` tag in `<head>` — no external stylesheet, no inline `style=` attributes added by this task.
- No CSS preprocessors, no CSS variables that require a build step (native CSS custom properties / `var()` are fine).
- The design should be self-contained and render correctly when the file is opened directly in a browser via `file://`.

### Design targets
| Area | Target appearance |
|---|---|
| Page background | Neutral dark or mid-tone (e.g. `#1a1a2e` or similar) |
| Calculator card | Rounded corners, subtle shadow, fixed or max width (~320–360px), centered horizontally and vertically |
| Display | Dark background (e.g. `#111` or `#222`), light text, right-aligned, padding, min-height to avoid collapse, two lines if showing expression + result |
| Digit buttons | Medium-gray background, white or light text |
| Operator buttons | Accent color (e.g. amber/orange) to stand out from digits |
| Equals button | Strong accent (e.g. green or blue), wider or full-row if layout allows |
| Utility buttons (C, backspace) | Distinct from digits — e.g. muted red or dark gray |
| Hover state | Lightened or brightened version of each button's base color |
| Active/pressed state | Slightly darkened or scaled-down to give tactile feedback |
| Font | System sans-serif or a clean monospace for the display |

## Scope

| File | Action |
|---|---|
| `calculator.html` | Modify — add `<style>` block inside `<head>` |

## Out of Scope
- JavaScript logic, event handling, or keyboard support — those are separate tasks.
- Any changes to the HTML structure or element classes/IDs — only the `<style>` block changes.
- External CSS files, CSS frameworks, or preprocessors.
- Mobile/responsive breakpoints beyond basic usability at desktop widths (the calculator card should simply be centered and not overflow a typical 1024px+ viewport).
- Animations beyond simple hover/active transitions.

## Acceptance Criteria

1. `calculator.html` contains a `<style>` block inside `<head>` with all calculator styles defined there and nowhere else (no new inline `style=` attributes on elements).
2. Opening `calculator.html` in a browser shows the calculator card centered on the page both horizontally and vertically (or near-vertically), with visible padding between the card and the viewport edge.
3. The calculator card has a distinct background color, rounded corners, and a visible drop shadow that separates it from the page background.
4. The display area has a dark background with light, right-aligned text and sufficient padding so text is not flush against the edges; the display does not collapse to zero height when empty.
5. Digit buttons, operator buttons, the equals button, and utility buttons each have a visually distinct background color — all four categories are distinguishable from one another at a glance.
6. All buttons have a hover state (background color or brightness changes when the cursor is over the button).
7. All buttons have an active/pressed state (visible feedback — e.g. slight darkening or `transform: scale()` — when the button is clicked).
8. The button grid is laid out in a regular grid with consistent spacing; no buttons overlap, overflow the card, or collapse to zero size.
9. No horizontal scrollbar appears on the page at a 1280×800 viewport.
10. The display text uses a monospace or clean sans-serif font that is legible at the rendered size.