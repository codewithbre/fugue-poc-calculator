# Implement the Core Arithmetic Operations Module

## Outcome
A file `src/operations.ts` exists and exports four pure functions: `add`, `subtract`, `multiply`, and `divide`. Each function accepts two `number` arguments and returns a `number`. The `divide` function throws an `Error` with a descriptive message when the divisor is zero. All four functions are importable by other modules in the project.

## Why
The calculator's core logic must live in a dedicated, reusable module so that both the CLI entry point and any future consumers (REPL, tests, etc.) can import arithmetic operations without duplicating logic. Isolating pure functions here also makes correctness easy to verify and the code easy to maintain.

## Context
This project is a TypeScript CLI calculator (`fugue-poc-calculator`). The prior scaffolding task produces the TypeScript project structure, including a `tsconfig.json` and an entry point (e.g. `src/index.ts`). The build config is already in place, so `src/operations.ts` just needs to be created following the same TypeScript conventions established by the scaffold.

Relevant conventions to follow:
- The project is TypeScript; all source files live under `src/`.
- Functions should be plain, stateless, and exported as named exports (not a default export).
- No external dependencies are needed — this is pure arithmetic logic.
- `divide` must guard against a zero divisor and throw a native `Error` with a message that clearly identifies division by zero (e.g. `"Division by zero is not allowed"`).

There are no existing operation files to modify — this file is created from scratch.

## Scope

| File | Action |
|---|---|
| `src/operations.ts` | Create |

## Out of Scope
- The CLI entry point (`src/index.ts`) wiring up these functions to argument parsing or a REPL — that is handled by a separate task.
- Unit test files — no test framework setup is part of this task.
- Any changes to `tsconfig.json`, `package.json`, or other config files produced by the scaffolding task.
- The scaffolding task produces `tsconfig.json` and `src/index.ts` (or equivalent entry point); those files must not be modified here.

## Acceptance Criteria
1. `src/operations.ts` exists at that exact path.
2. The file exports a named function `add(a: number, b: number): number` that returns the sum of its two arguments (e.g. `add(3, 4)` returns `7`).
3. The file exports a named function `subtract(a: number, b: number): number` that returns the difference (e.g. `subtract(10, 3)` returns `7`).
4. The file exports a named function `multiply(a: number, b: number): number` that returns the product (e.g. `multiply(3, 4)` returns `12`).
5. The file exports a named function `divide(a: number, b: number): number` that returns the quotient for non-zero divisors (e.g. `divide(12, 4)` returns `3`).
6. Calling `divide(n, 0)` for any value of `n` throws an instance of `Error` whose `.message` property contains the phrase "zero" or "division by zero" (case-insensitive), making the cause immediately clear.
7. All four functions are exported as named exports and can be imported with `import { add, subtract, multiply, divide } from './operations'` from another file in `src/`.
8. The file contains no syntax errors and is valid TypeScript consistent with the project's `tsconfig.json` (i.e. it would compile without errors under `tsc`).