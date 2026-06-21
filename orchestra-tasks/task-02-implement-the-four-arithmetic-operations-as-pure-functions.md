# Implement the Four Arithmetic Operations as Pure Functions

## Outcome
A module at `src/operations.ts` exists and exports four pure functions: `add`, `subtract`, `multiply`, and `divide`. Each function accepts two numeric arguments and returns the correct numeric result. `divide` throws an error with a descriptive message when the divisor is zero. The module is importable from the project entry point.

## Why
The arithmetic operations are the core logic of the CLI calculator. Isolating them in a dedicated module keeps business logic separate from I/O concerns (argument parsing, REPL interaction), makes the functions independently testable, and provides a single source of truth for all calculations performed by the calculator.

## Context
The project is `fugue-poc-calculator`, a TypeScript CLI calculator. A prior scaffolding task has already set up the TypeScript project with an entry point and build configuration. That task produces:
- A `tsconfig.json` at the project root configuring TypeScript compilation.
- A `package.json` with TypeScript as a dependency and a build script.
- An entry point file (e.g., `src/index.ts`) that can import from sibling modules.

The source files live under `src/`. The project targets Node.js and uses standard TypeScript conventions. No test framework is assumed to be present; correctness is verified by direct invocation or manual smoke-testing.

The four operations to implement:
- **`add(a, b)`** — returns `a + b`
- **`subtract(a, b)`** — returns `a - b`
- **`multiply(a, b)`** — returns `a * b`
- **`divide(a, b)`** — returns `a / b`; throws a `Error` with a message clearly stating division by zero when `b === 0`

All functions must be pure: no side effects, no global state, deterministic output for any given input.

## Scope

| File | Action |
|---|---|
| `src/operations.ts` | Create |

## Out of Scope
- Argument parsing, CLI entry point logic, or REPL behavior — those belong in `src/index.ts` or a separate module.
- Unit test files or test framework configuration.
- Floating-point edge cases beyond standard JavaScript number arithmetic.
- Support for non-numeric input validation (callers are responsible for passing numbers).
- Any changes to `tsconfig.json`, `package.json`, or `src/index.ts` beyond what is needed to make the module importable.

This task depends on the prior scaffolding task, which produces a valid `tsconfig.json` and `package.json` with TypeScript configured. Do not modify those files.

## Acceptance Criteria
1. `src/operations.ts` exists and contains named exports `add`, `subtract`, `multiply`, and `divide`.
2. Each export is a function that accepts exactly two parameters of type `number` and has a return type of `number`.
3. `add(3, 4)` returns `7`.
4. `subtract(10, 3)` returns `7`.
5. `multiply(3, 4)` returns `12`.
6. `divide(12, 4)` returns `3`.
7. Calling `divide(5, 0)` throws an instance of `Error`.
8. The error thrown by `divide(5, 0)` has a `message` property that contains the phrase "division by zero" (case-insensitive) or an equivalent unambiguous description of the zero-divisor condition.
9. `divide(7, 2)` returns `3.5` (fractional results are preserved, not truncated).
10. The file compiles without TypeScript errors when `tsc` is run using the project's `tsconfig.json`.