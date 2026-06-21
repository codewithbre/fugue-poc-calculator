# Implement Single-Expression Mode (CLI Args)

## Outcome
A CLI entry point exists (either `calc.ts` at the project root or `src/cli.ts`) that handles single-expression mode. When invoked with exactly three arguments — a left operand, an operator, and a right operand — the program parses the inputs, delegates to the appropriate arithmetic operation from the operations module, prints the numeric result to stdout, and exits with code `0`. Invalid operators or non-numeric inputs print a descriptive error message to stderr and exit with a non-zero code. Division by zero also prints a clear error to stderr and exits with a non-zero code.

## Why
This is the first user-facing mode of the CLI calculator. Without it, the arithmetic operations module has no way to be exercised from the command line. Single-expression mode is the simplest entry point and establishes the argument-parsing and error-handling patterns that the interactive REPL mode will later build on.

## Context

### Project layout
The project is a TypeScript CLI calculator (`fugue-poc-calculator`). There is no existing source code described beyond the README. The operations module is a dependency of this task — it is assumed to already exist and export the four arithmetic functions.

### Operations module contract
The operations module (expected at `src/operations.ts` or a similarly named file at the project root) is produced by the prior task "Implement the core arithmetic operations module." It exports at minimum:

```ts
export function add(a: number, b: number): number;
export function subtract(a: number, b: number): number;
export function multiply(a: number, b: number): number;
export function divide(a: number, b: number): number; // throws or returns error signal on division by zero
```

The exact path and export shape must be confirmed by inspecting the operations module file when implementing this task.

### Argument layout
Node.js populates `process.argv` as:
- `process.argv[0]` — path to node
- `process.argv[1]` — path to the script
- `process.argv[2]` — first user argument (left operand)
- `process.argv[3]` — second user argument (operator)
- `process.argv[4]` — third user argument (right operand)

Single-expression mode is active when `process.argv.length === 5`.

### Supported operators
`+`, `-`, `*`, `/`

### Error handling conventions
- Non-numeric operands → error to stderr, exit code `1`
- Unsupported/missing operator → error to stderr, exit code `1`
- Division by zero → error to stderr, exit code `1`
- Wrong number of arguments (for this mode) → no action required here; the REPL mode task handles the zero-argument case

### TypeScript conventions
The project uses TypeScript. The entry file should be runnable via `ts-node` or compiled to JS. Use `process.stdout.write` or `console.log` for result output and `console.error` or `process.stderr.write` for errors. Call `process.exit(1)` on error.

## Scope

| File | Action |
|---|---|
| `calc.ts` | Create |
| `src/operations.ts` | Modify (only if the import path needs a minor correction — do not change operation logic) |

> If the operations module was created at a different path by the prior task, adjust the import in `calc.ts` accordingly. Do not rename or move the operations module.

## Out of Scope
- Interactive REPL mode — that is a separate task. `calc.ts` may contain a stub or comment reserving space for it, but no REPL logic should be implemented here.
- Installing or configuring TypeScript, `ts-node`, or `package.json` scripts — assume the project environment is already set up.
- Unit test files — no test files are created or modified in this task.
- Changes to the arithmetic logic inside the operations module.

**Dependency:** This task depends on "Implement the core arithmetic operations module," which produces an operations module exporting `add`, `subtract`, `multiply`, and `divide`. Locate that file before writing the import statement in `calc.ts`.

## Acceptance Criteria

1. Running `node calc.js 10 + 5` (or equivalent `ts-node calc.ts 10 + 5`) prints exactly `15` to stdout and exits with code `0`.
2. Running `node calc.js 9 / 3` prints exactly `3` to stdout and exits with code `0`.
3. Running `node calc.js 5 / 0` prints a message containing "division by zero" (case-insensitive) or equivalent clear description to stderr, prints nothing to stdout, and exits with a non-zero code.
4. Running `node calc.js foo + 2` prints a message indicating invalid or non-numeric input to stderr, prints nothing to stdout, and exits with a non-zero code.
5. Running `node calc.js 10 % 5` (unsupported operator) prints a message indicating an unknown or unsupported operator to stderr, prints nothing to stdout, and exits with a non-zero code.
6. Running `node calc.js 3 - 1` prints `2` to stdout and exits with code `0`.
7. Running `node calc.js 4 * 3` prints `12` to stdout and exits with code `0`.
8. `calc.ts` contains an import statement that references the operations module and uses at least the `add`, `subtract`, `multiply`, and `divide` exports — no arithmetic is reimplemented inline.
9. The file `calc.ts` exists at the project root (or `src/cli.ts` if that path was chosen) and is the sole new file introduced by this task.