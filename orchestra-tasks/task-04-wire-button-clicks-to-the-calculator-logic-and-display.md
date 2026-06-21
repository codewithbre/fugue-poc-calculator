# Wire Button Clicks to the Calculator Logic and Display

## Outcome
Every button in the calculator UI is connected to the JavaScript logic via click event listeners. Clicking a digit or operator appends to the display, clicking `=` evaluates the expression and shows the result, clicking `C` resets the display to `0`, and clicking the backspace button removes the last character. The display element updates after every interaction with no console errors.

## Why
Without event bindings, the calculator UI is a static, non-functional layout. This task is the final integration step that connects the visual interface to the underlying logic, making the calculator fully usable in a browser.

## Context
The project is a single self-contained HTML file (`calculator.html`) with no framework, no build step, and no external dependencies — vanilla JS and CSS only.

Two prior tasks establish the foundation this task builds on:

1. **Core calculator logic** — A `<script>` block in `calculator.html` contains:
   - A state variable (e.g. `expression` or `displayValue`) tracking the current input string.
   - An `evaluate()` or equivalent function that parses and computes the expression (handling division by zero).
   - A `reset()` or `clear()` function that resets state to `0`.
   - An `appendToExpression(value)` or equivalent function that appends a character to the current expression.
   - A `backspace()` or equivalent function that removes the last character.
   - An `updateDisplay()` function that writes the current state to the display DOM element.

2. **CSS and HTML layout** — The `calculator.html` file contains:
   - A display element (e.g. `<div id="display">` or `<input id="display">`) that shows the current value.
   - Button elements for digits `0`–`9`, operators `+`, `-`, `*`, `/`, a decimal point `.`, an equals button `=`, a clear button `C`, and a backspace button.
   - Buttons have identifiable attributes (e.g. `data-value`, `id`, or text content) that can be used to attach event listeners.

The event binding code belongs in the `<script>` block of `calculator.html`, in the section after the logic functions are defined. The pattern to follow is standard vanilla JS: query buttons by selector, attach `addEventListener('click', ...)` handlers, call the appropriate logic function, then call `updateDisplay()`.

Keyboard support is listed in the original goal description and should also be wired up here via a `keydown` event listener on `document` or `window`, mapping keys to the same logic functions.

## Scope

| File | Action |
|---|---|
| `calculator.html` | Modify |

## Out of Scope
- Creating the HTML structure or button layout (produced by the "Add CSS styling for the calculator layout and visual design" task).
- Implementing the core logic functions (`evaluate`, `reset`, `appendToExpression`, `backspace`, `updateDisplay`) — these are produced by the "Implement the core calculator expression state and evaluation logic" task.
- Adding new buttons or changing the visual design.
- Any build tooling, frameworks, or external scripts.
- Unit tests or test files.

## Acceptance Criteria

1. Opening `calculator.html` in a browser shows the calculator with a display reading `0` (or equivalent initial state) and no console errors on load.
2. Clicking digit buttons (e.g. `7`, then `8`) appends each digit to the display in sequence (display shows `78`).
3. Clicking an operator button (e.g. `*`) appends the operator to the display (display shows `78*`).
4. Clicking more digits after an operator (e.g. `8`) appends them (display shows `78*8`).
5. Clicking `=` evaluates the expression and updates the display with the result (e.g. `78*8` → `624`).
6. Clicking `C` after any input resets the display to `0`.
7. Clicking the backspace button removes the last character from the display (e.g. `123` → `12`).
8. Clicking `C` after a result (e.g. after `=` shows `56`) resets the display to `0`, allowing a fresh calculation.
9. A full sequence — `7` → `*` → `8` → `=` — displays `56`, and then pressing `C` resets the display to `0`.
10. Division by zero (e.g. `8` → `/` → `0` → `=`) shows an error message (e.g. `Error`) rather than crashing or showing `Infinity`, with no console errors.
11. Keyboard input works: pressing digit keys, operator keys, `Enter` (for `=`), `Backspace`, and `Escape` (for `C`) trigger the same behavior as clicking the corresponding buttons.
12. Rapid sequential button clicks do not produce console errors or leave the display in an inconsistent state.