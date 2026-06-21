# Add Keyboard Input Support Mapped to Calculator Actions

## Outcome
`calculator.html` contains a `keydown` event listener on the `document` that maps keyboard input to the same calculator actions as button clicks. Digit keys (0–9) append digits, operator keys (`+`, `-`, `*`, `/`) append operators, `Enter` or `=` triggers evaluation, `Backspace` removes the last character, and `Escape` or `Delete` triggers clear. The keyboard handler reuses the same logic functions already wired to the buttons, producing identical behaviour.

## Why
A calculator that requires mouse interaction is incomplete. Keyboard support allows users to perform calculations at full typing speed without switching between keyboard and mouse, and is a standard usability expectation for any calculator interface.

## Context
The project is a single self-contained `calculator.html` file — no framework, no build step, vanilla JS and CSS only.

A prior task ("Wire button clicks to the calculator logic and display") has already:
- Implemented core calculator logic functions (append digit, append operator, evaluate, backspace, clear) inside the `<script>` block of `calculator.html`.
- Wired each on-screen button's `click` event to call those functions.
- Connected those functions to the display element so results are shown on screen.

The keyboard handler must be added to the same `<script>` block in `calculator.html`. It must call the **same** logic functions that the button click handlers call — not duplicate logic. This ensures keyboard and button input are always in sync.

The `<script>` block is expected to have clearly separated sections (e.g. logic, display, button wiring). The keyboard handler should be added in its own clearly commented section after the button wiring section.

The event listener must be attached to `document` (not a specific element) so it is active regardless of focus state.

Key mapping:
| Key(s) | Action |
|---|---|
| `0`–`9` | Append digit |
| `+`, `-`, `*`, `/` | Append operator |
| `Enter`, `=` | Evaluate |
| `Backspace` | Remove last character |
| `Escape`, `Delete` | Clear |

Keys that are not in the above list must be ignored silently (no errors, no side effects).

## Scope

| File | Action |
|---|---|
| `calculator.html` | Modify |

## Out of Scope
- No changes to the calculator logic functions themselves.
- No changes to the HTML layout or CSS styling.
- No changes to the button click wiring.
- No new files are created.
- This task does not implement the calculator logic or button wiring — that is produced by the prior task "Wire button clicks to the calculator logic and display" and must already exist in `calculator.html` before this task is applied.

## Acceptance Criteria

1. `calculator.html` contains a `document.addEventListener('keydown', ...)` call in the `<script>` block, in a clearly commented keyboard handler section.
2. Pressing digit keys `0` through `9` appends the corresponding digit to the display, identical to clicking the digit buttons.
3. Pressing `+`, `-`, `*`, or `/` appends the corresponding operator to the display, identical to clicking the operator buttons.
4. Pressing `Enter` or `=` evaluates the current expression and shows the result, identical to clicking the equals button.
5. Pressing `Backspace` removes the last character from the display, identical to clicking the backspace button.
6. Pressing `Escape` or `Delete` clears the display and resets calculator state, identical to clicking the clear button.
7. A complete calculation using only the keyboard — for example typing `1`, `2`, `+`, `3`, `4`, `Enter` — produces the result `46` on the display.
8. Pressing `Backspace` after entering one or more characters removes only the last character, allowing correction before evaluation.
9. Pressing `Escape` after a calculation resets the display to its initial/empty state.
10. Pressing any key not in the defined mapping (e.g. a letter key) produces no visible change and no console error.
11. The keyboard handler does not duplicate logic — it calls the same functions already used by the button click handlers.