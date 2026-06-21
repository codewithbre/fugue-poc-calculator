# Implement Interactive REPL Mode

## Outcome
When `node calc.js` is invoked with no arguments, the program enters an interactive read-eval-print loop. It displays a `> ` prompt, reads a line of input, evaluates it as a `<num> <op> <num>` expression, prints the result, and loops. Typing `exit` or sending EOF (Ctrl-D) exits the process cleanly. Division by zero and malformed input print a descriptive error message and re-prompt without crashing.

## Why
The CLI calculator requires two modes of operation. The single-expression mode (e.g. `node calc.js 10 + 5`) handles one-shot evaluation, but users also need an interactive session where they can evaluate multiple expressions without re-invoking the program. The REPL mode provides that experience and completes the full feature set described in the project goal.

## Context

### Project structure
The project is a TypeScript CLI calculator (`fugue-poc-calculator`). The relevant entry point is `calc.ts` (compiled to `calc.js`). A prior task produces a core arithmetic operations module — either `operations.ts` or `src/operations.ts` — that exports at minimum the following functions (or equivalent):

```ts
export function add(a: number, b: number): number
export function subtract(a: number, b: number): number
export function multiply(a: number, b: number): number
export function divide(a: number, b: number): number  // throws or returns error on division by zero
```

The operations module is the **only** dependency this task imports for arithmetic. Do not reimplement arithmetic inline.

### Entry point behavior
`calc.ts` is the main entry point. It reads `process.argv`. When arguments are present (beyond `node` and the script name), it handles single-expression mode. When **no** extra arguments are present (`process.argv.length <= 2`), it must enter REPL mode.

### REPL implementation approach
Use Node.js built-in `readline` to implement the loop. The `readline` module is available without any additional dependencies:

```ts
import * as readline from 'readline'
```

Create an interface reading from `process.stdin` / `process.stdout`. Use the `line` event (or a manual async loop) to process each line. On EOF (`close` event), exit cleanly.

### Expression parsing
The REPL accepts the same `<num> <op> <num>` format as the single-expression CLI mode. Tokens are separated by whitespace. Valid operators are `+`, `-`, `*`, `/`. Parsing should:
- Trim the input line
- Split on whitespace
- Validate that exactly three tokens are present
- Validate that tokens 1 and 3 are finite numbers
- Validate that token 2 is a recognized operator

### Error handling
- **Division by zero**: print a clear message such as `Error: Division by zero` and re-prompt
- **Invalid input** (wrong token count, unrecognized operator, non-numeric operands): print a descriptive error such as `Error: Invalid expression. Expected format: <num> <op> <num>` and re-prompt
- **`exit` keyword**: when the trimmed input equals `exit`, call `process.exit(0)`
- **EOF (Ctrl-D)**: the `readline` `close` event fires; call `process.exit(0)`

### Conventions
- TypeScript source files; the project compiles `.ts` to `.js`
- No external runtime dependencies beyond Node.js built-ins and the operations module
- The REPL logic may live directly in `calc.ts` or be extracted to `src/repl.ts` and imported — either is acceptable, but the entry point remains `calc.ts`

## Scope

| File | Action |
|---|---|
| `calc.ts` | Modify — add REPL mode branch when no CLI arguments are provided |
| `src/repl.ts` | Create (optional) — extracted REPL loop logic, imported by `calc.ts` if the implementer chooses to separate concerns |

## Out of Scope
- Single-expression CLI mode (`node calc.js 10 + 5`) — this must not be changed or broken
- The arithmetic operations module — it is produced by the prior task "Implement the core arithmetic operations module" and must be imported as-is, not modified
- Any web UI or MCP server functionality
- History, tab-completion, or other advanced readline features
- Unit tests (not part of this task's scope)

## Acceptance Criteria

1. Running `node calc.js` with no arguments displays a `> ` prompt on stdout and waits for input without exiting immediately.
2. Entering `10 * 3` at the prompt causes `30` to be printed, followed by a new `> ` prompt.
3. Entering `1 / 0` at the prompt causes a division-by-zero error message to be printed (e.g. `Error: Division by zero`), followed by a new `> ` prompt — the process does not crash.
4. Entering a malformed expression such as `hello` or `1 + + 2` causes an error message to be printed and a new `> ` prompt to appear — the process does not crash.
5. Entering `exit` causes the process to terminate with exit code `0` and no error output.
6. Sending EOF (Ctrl-D / closing stdin) causes the process to terminate cleanly with exit code `0`.
7. Running `node calc.js 10 + 5` still prints `15` and exits — the existing single-expression mode is unaffected.
8. The REPL imports arithmetic operations from the operations module rather than reimplementing them inline.