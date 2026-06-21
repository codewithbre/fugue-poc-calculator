# Scaffold the TypeScript Project with Entry Point and Build Config

## Outcome
A minimal TypeScript project skeleton exists at the root of the repository with:
- `package.json` containing project metadata and scripts for building and running the calculator
- `tsconfig.json` configured to compile TypeScript to JavaScript
- `calc.ts` as the entry point, containing no business logic but printing a usage hint or exiting cleanly

The project compiles without errors and the compiled output runs without crashing.

## Why
Every subsequent task that implements calculator logic, REPL mode, or expression parsing depends on a working TypeScript compilation pipeline and a known entry point file. Without this scaffold, there is no shared structure for other tasks to build on.

## Context
The repository is `fugue-poc-calculator`, a test project for a Fugue runtime POC. The README describes Goal 1 as building a CLI calculator in TypeScript with two modes: single expression via args (`node calc.js 10 + 5`) and an interactive REPL. The entry point is expected to be `calc.js` after compilation (i.e., `calc.ts` is the source).

The README does not describe any existing `package.json`, `tsconfig.json`, or source files — the repository currently contains only `README.md`. This task creates the project skeleton from scratch.

**Conventions to follow:**
- The entry point is `calc.ts` at the project root (not inside `src/`), so that `node calc.js` works from the root after compilation.
- TypeScript should compile to CommonJS JavaScript in the same directory (or a `dist/` folder is acceptable only if the `package.json` `main` and scripts account for it — prefer root-level output for simplicity so `node calc.js` works without a path prefix).
- No external runtime dependencies are required at this stage. `typescript` is a dev dependency.
- `package.json` must include at minimum:
  - `"build"` script: `tsc`
  - `"start"` script: `node calc.js` (or the compiled output path)
- `tsconfig.json` must include at minimum:
  - `"target"`: `"ES2020"` or later
  - `"module"`: `"commonjs"`
  - `"strict"`: `true`
  - `"outDir"` or no `outDir` (root output) — keep it simple so `node calc.js` works from root
  - `"include"`: covers `calc.ts`

**`calc.ts` content:** Should be a valid TypeScript file that compiles cleanly. It may print a usage hint such as `Usage: node calc.js <num> <op> <num>` or simply exit with no output. No calculator logic is implemented here.

## Scope

| File | Action |
|---|---|
| `package.json` | Create |
| `tsconfig.json` | Create |
| `calc.ts` | Create |

## Out of Scope
- No calculator logic (addition, subtraction, multiplication, division) is implemented in this task.
- No REPL or interactive mode.
- No expression parsing.
- No error handling for division by zero or invalid input.
- No additional source files beyond `calc.ts`.
- No test framework or test files.
- No linting configuration.
- `README.md` must not be modified.

## Acceptance Criteria
1. `package.json` exists at the repository root and is valid JSON with a `"build"` script that invokes `tsc` and a `"start"` script that runs the compiled entry point.
2. `tsconfig.json` exists at the repository root, is valid JSON, and includes `"strict": true`, `"module": "commonjs"`, and a target of `ES2020` or later.
3. `calc.ts` exists at the repository root and contains valid TypeScript with no syntax or type errors.
4. Running `npx tsc --noEmit` from the repository root exits with code 0 and produces no error output.
5. Running `tsc` (the build script) produces a `calc.js` file that can be located and executed with `node calc.js`.
6. `node calc.js` exits with code 0 and either produces no output or prints a usage hint string — it does not throw an unhandled exception or print a stack trace.
7. No files other than `package.json`, `tsconfig.json`, `calc.ts`, and any compiled output (`calc.js`, `calc.js.map` if source maps are enabled) are added or modified.