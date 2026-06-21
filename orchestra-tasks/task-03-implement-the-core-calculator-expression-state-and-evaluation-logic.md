# Implement the Core Calculator Expression State and Evaluation Logic

## Outcome
`calculator.html` contains a `<script>` block (before `</body>`) that manages all calculator state and logic. The script exposes functions for appending digits and operators, handling decimal points, evaluating expressions, and clearing state. The display updates reactively as the user interacts. Division by zero produces the string `'Error'` rather than `Infinity` or `NaN`.

## Why
The HTML skeleton provides the visual layout and buttons, but without logic those buttons do nothing. This module is the brain of the calculator — it connects user input to meaningful computation and drives the display. Without it, the calculator is a static mockup.

## Context

### Project
A single self-contained HTML file calculator (`calculator.html`). No framework, no build step — vanilla JS and CSS only. The prior task ("Define the HTML skeleton and calculator layout structure") produces `calculator.html` with:
- A display element (expected `id="display"`) showing the current expression or result.
- Buttons for digits `0–9`, operators `+`, `-`, `*`, `/`, a decimal point `.`, an equals button `=`, and a clear button `C`.
- Each button calls a JS function when clicked (e.g. `onclick="appendValue('7')"`, `onclick="evaluate()"`, `onclick="clearDisplay()"`).

The script block to be added goes **before `</body>`** inside `calculator.html`.

### State model
The calculator maintains a single string representing the current expression being built (e.g. `"10+5"`, `"1.5*2"`). This string is passed to `eval()` (or parsed manually) when `=` is pressed.

### Key behaviors
| Action | Behavior |
|---|---|
| Digit/operator button | Append the character to the expression string; update display |
| Decimal `.` | Only append if the current operand does not already contain a decimal point |
| `=` (evaluate) | Evaluate the full expression; show result; on division by zero show `'Error'` |
| `C` (clear) | Reset expression to empty string; display shows `'0'` or empty |

### Decimal guard logic
Before appending `.`, extract the last operand from the expression string (split on `+`, `-`, `*`, `/`) and check whether it already contains a `.`. If it does, do nothing.

### Division by zero
After evaluation, check if the result is `Infinity`, `-Infinity`, or `NaN`. If so, set the display to `'Error'` and reset state.

### Display update
Every state-mutating function must update the display element's `innerText` (or `value` if it is an `<input>`) to reflect the current expression or result.

### Exposed functions
The following functions must exist in the global scope (so `onclick` attributes in the HTML can call them):
- `appendValue(val)` — appends a digit or operator character
- `appendDecimal()` — appends `.` only if safe
- `evaluate()` — computes the expression and updates display
- `clearDisplay()` — resets all state

## Scope

| File | Action |
|---|---|
| `calculator.html` | Modify — add `<script>` block before `</body>` |

## Out of Scope
- CSS styling changes (handled by a separate task).
- Keyboard event support (handled by a separate task).
- Any changes to the HTML button layout or display element structure — those are produced by the prior task ("Define the HTML skeleton and calculator layout structure") and must not be altered.
- Multi-step memory or history features.
- Replacing `eval()` with a custom parser (using `eval()` on a sanitized numeric expression string is acceptable here).

## Acceptance Criteria

1. After loading `calculator.html` in a browser, opening the console and calling `appendValue('1')`, `appendValue('0')`, `appendValue('+')`, `appendValue('5')`, then `evaluate()` causes the display to show `15`.
2. Calling `appendValue('9')`, `appendValue('/')`, `appendValue('0')`, then `evaluate()` causes the display to show `'Error'` (not `Infinity` or `NaN`).
3. Calling `appendValue('1')`, `appendDecimal()`, `appendValue('5')`, `appendValue('*')`, `appendValue('2')`, then `evaluate()` causes the display to show `3`.
4. Calling `appendDecimal()` twice in a row (without an intervening operator) results in only one `.` being appended to the current operand — the display does not show `..` or two decimal points in the same operand.
5. Calling `clearDisplay()` resets the expression so the display shows `'0'` or is empty, and a subsequent `evaluate()` does not throw or display a stale result.
6. All four functions (`appendValue`, `appendDecimal`, `evaluate`, `clearDisplay`) exist on the global `window` object and are callable from the browser console without errors.
7. The `<script>` block appears in `calculator.html` before the closing `</body>` tag.
8. No external scripts, libraries, or modules are imported — the logic is entirely inline vanilla JavaScript.