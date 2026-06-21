# Implement Single-Expression Mode (CLI Args)

## Outcome
A CLI entry point exists (either `calc.ts` at the project root or `src/cli.ts`) that, when invoked with exactly three arguments — a numeric operand, an operator symbol, and a numeric operand — parses those arguments, delegates to the appropriate arithmetic operation from `src/operations.ts`, prints the result to stdout, and exits with code `0`. Invalid or unknown operators print a descriptive error to stderr and exit with a non-zero code. Non-numeric operands likewise print a descriptive error to stderr and exit with a non-zero code. Division by zero prints a clear division-by-zero error to stderr and exits with a non-zero code.

## Why
This is the first user-facing mode of the calculator. It allows the program to be used directly from the shell as a one-shot expression evaluator (`node calc.js 10 + 5`), which is the primary stated goal of Goal 1. Without this entry point, the arithmetic operations defined in `src/operations.ts` are not accessible to any user.

## Context

### Dependency: `src/operations.ts`
This task depends on `src/operations.ts` already existing and exporting the four arithmetic operations as pure functions. The expected exports are:

```ts
export function add(a: number, b: number): number;
export function subtract(a: number, b: number): number;
export function multiply(a: number, b: number): number;
export function divide(a: number, b: number): number; // throws or returns error signal on division by zero
```

The `divide` function must handle division by zero — either by throwing an `Error` with a descriptive message, or by returning a sentinel value. The CLI entry point must catch or detect this and print the error to stderr.

### Project structure
The project is a TypeScript project. Based on the README, the compiled output is expected to be runnable as `node calc.js`, which implies a build step compiles TypeScript to JavaScript, or `ts-node` / `tsx` is used directly. The CLI file should be written in TypeScript and placed either at the project root as `calc.ts` or at `src/cli.ts`. Use `calc.ts` at the project root to match the `node calc.js` invocation pattern described in the README.

### Argument parsing
Arguments are accessed via `process.argv`. `process.argv[0]` is the node binary, `process.argv[1]` is the script path, so the three user-supplied arguments are at indices `2`, `3`, and `4`.

- `process.argv[2]` — first operand (should parse to a finite number via `parseFloat` or `Number`)
- `process.argv[3]` — operator (one of `+`, `-`, `*`, `/`)
- `process.argv[4]` — second operand (should parse to a finite number)

### Error handling conventions
- Print errors to `process.stderr`
- Exit with `process.exit(1)` on any error
- Exit with `process.exit(0)` (or natural exit) on success
- Print the result to `process.stdout` (e.g., `console.log(result)`)

### Supported operators
| Symbol | Function |
|--------|----------|
| `+`    | `add`    |
| `-`    | `subtract` |
| `*`    | `multiply` |
| `/`    | `divide` |

Any other operator symbol (e.g., `%`, `^`) is unsupported and must produce a usage/unknown-operator error.

### Example invocations
```
node calc.js 10 + 5     → stdout: 15
node calc.js 10 - 3     → stdout: 7
node calc.js 10 * 4     → stdout: 40
node calc.js 10 / 2     → stdout: 5
node calc.js 10 / 0     → stderr: division by zero error, exit 1
node calc.js 10 % 5     → stderr: unknown operator error, exit 1
node calc.js abc + 5    → stderr: invalid operand error, exit 1
node calc.js 10 + xyz   → stderr: invalid operand error, exit 1
```

## Scope

| File | Action |
|---|---|
| `calc.ts` | Create |

## Out of Scope
- The interactive REPL mode is a separate task and must NOT be implemented here.
- `src/operations.ts` must NOT be modified — it is produced by the prior task "Implement the four arithmetic operations as pure functions" and is consumed here as-is.
- No new npm packages should be added for argument parsing; use `process.argv` directly.
- No changes to `README.md`, `package.json`, `tsconfig.json`, or any other configuration files unless strictly required to make `calc.ts` compile and run.
- No test files are required by this task.

## Acceptance Criteria

1. Running `node calc.js 10 + 5` (or equivalent `ts-node calc.ts 10 + 5`) prints `15` to stdout and exits with code `0`.
2. Running `node calc.js 10 - 3` prints `7` to stdout and exits with code `0`.
3. Running `node calc.js 10 * 4` prints `40` to stdout and exits with code `0`.
4. Running `node calc.js 10 / 2` prints `5` to stdout and exits with code `0`.
5. Running `node calc.js 10 / 0` prints a message containing "division by zero" (case-insensitive) to stderr, prints nothing to stdout, and exits with a non-zero code.
6. Running `node calc.js 10 % 5` prints a message indicating an unknown or unsupported operator to stderr, prints nothing to stdout, and exits with a non-zero code.
7. Running `node calc.js abc + 5` prints a message indicating an invalid or non-numeric operand to stderr, prints nothing to stdout, and exits with a non-zero code.
8. Running `node calc.js 10 + xyz` prints a message indicating an invalid or non-numeric operand to stderr, prints nothing to stdout, and exits with a non-zero code.
9. `calc.ts` imports its arithmetic operations from `src/operations.ts` and does not reimplement any arithmetic logic inline.
10. `calc.ts` contains no interactive/REPL logic — it only handles the three-argument single-expression mode.