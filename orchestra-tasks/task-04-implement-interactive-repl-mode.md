# Implement Interactive REPL Mode

## Outcome
When `node calc.js` is invoked with no arguments, the program enters an interactive read-eval-print loop. It displays a prompt, reads an expression from the user (e.g. `10 + 5`), evaluates it using the arithmetic operations module, prints the result, and loops. Typing `exit` or sending EOF (Ctrl-D) terminates the process cleanly. The REPL logic lives in `src/repl.ts` and is consumed by `calc.ts`.

## Why
The CLI calculator requires two modes of operation: single-expression via arguments and interactive REPL. This task delivers the interactive mode, making the calculator usable as a persistent session rather than requiring a new process invocation per expression. It also ensures division-by-zero errors are surfaced gracefully without crashing the process.

## Context

### Dependency: `src/operations.ts`
This task depends on `src/operations.ts` already existing and exporting four pure arithmetic functions. Based on the project structure, that module is expected to export:

```ts
export function add(a: number, b: number): number;
export function subtract(a: number, b: number): number;
export function multiply(a: number, b: number): number;
export function divide(a: number, b: number): number; // throws or returns error on division by zero
```

The REPL must import and use these functions rather than implementing arithmetic inline.

### Entry point: `calc.ts`
`calc.ts` is the main entry point compiled to `calc.js`. It must detect whether command-line arguments are present:
- **Arguments present** (`node calc.js 10 + 5`): single-expression CLI mode (handled separately or already present).
- **No arguments** (`node calc.js`): invoke the REPL.

### Expression parsing
The REPL accepts simple infix expressions of the form `<number> <operator> <number>` where operator is one of `+`, `-`, `*`, `/`. Parsing can be done with a simple split/regex — no need for a full expression parser.

### Input handling
Use Node.js's built-in `readline` module to read lines from `stdin` and write the prompt to `stdout`. The interface should:
- Display a prompt string (e.g. `> `) before each input.
- Handle the `close` event (EOF / Ctrl-D) to exit cleanly.
- Recognize the literal string `exit` (case-insensitive) to close the interface and exit.

### Error handling
- If the expression cannot be parsed (wrong format, non-numeric operands), print a clear error message and continue the loop — do not crash.
- If division by zero is attempted, print a clear error message (e.g. `Error: Division by zero`) and continue the loop — do not crash.

### TypeScript / project conventions
- The project is TypeScript-based and compiles to JavaScript (output is `calc.js`).
- Follow the same module style used in `src/operations.ts` (ES module `import`/`export` or CommonJS `require` — match whatever convention is already in place).
- No external dependencies beyond Node.js built-ins.

## Scope

| File | Action |
|---|---|
| `src/repl.ts` | Create |
| `calc.ts` | Modify |

## Out of Scope
- Single-expression CLI mode (`node calc.js 10 + 5`) — this task does not implement or modify that path beyond ensuring it still works when arguments are present.
- `src/operations.ts` — must not be modified. This task only consumes it.
- Any build configuration (`tsconfig.json`, `package.json` scripts) — do not modify unless the files already exist and a minor path addition is strictly required.
- Support for expressions with more than one operator or parentheses (e.g. `1 + 2 + 3`).
- History, tab-completion, or any readline features beyond basic line input.

**Dependency note:** This task requires `src/operations.ts` to already exist and export `add`, `subtract`, `multiply`, and `divide`. That file is produced by the prior task "Implement the four arithmetic operations as pure functions."

## Acceptance Criteria

1. Running `node calc.js` with no arguments displays a prompt (e.g. `> `) on stdout without immediately exiting.
2. After the prompt, entering `10 + 5` and pressing Enter causes `15` to be printed, followed by a new prompt.
3. Entering `10 - 3` prints `7`.
4. Entering `4 * 6` prints `24`.
5. Entering `20 / 4` prints `5`.
6. Entering `10 / 0` prints an error message containing "division by zero" (case-insensitive) and then shows a new prompt — the process does not exit or crash.
7. Entering a malformed expression (e.g. `abc + def` or `10 % 5`) prints an error message and shows a new prompt — the process does not exit or crash.
8. Entering `exit` (any casing) terminates the process with exit code `0`.
9. Sending EOF (Ctrl-D / closing stdin) terminates the process with exit code `0`.
10. Running `node calc.js 10 + 5` (with arguments) does not enter the REPL — it behaves as the single-expression CLI mode (prints the result and exits).
11. `src/repl.ts` imports its arithmetic operations exclusively from `src/operations.ts` and contains no inline arithmetic logic.