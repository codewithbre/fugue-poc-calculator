# Scaffold the TypeScript Project with Entry Point and Build Config

## Outcome
A minimal TypeScript project exists at the root of the repository with:
- A `package.json` containing project metadata and scripts for building and running the calculator
- A `tsconfig.json` configured to compile TypeScript to JavaScript
- A `calc.ts` entry point (at the root or in `src/`) that compiles without errors and exits cleanly when run

Running `tsc` produces a compiled `calc.js` (or equivalent output file) with no TypeScript errors. Running `node` on the compiled output exits with code 0 and produces no crash.

## Why
Every subsequent task that adds calculator logic, REPL support, or CLI argument parsing depends on a working TypeScript compilation pipeline. Without this scaffold, there is no foundation to build on — no entry point to extend, no `tsconfig.json` to govern compilation, and no `package.json` scripts to standardize how the project is built and run. This task establishes the skeleton that all other tasks build on.

## Context
The repository `fugue-poc-calculator` currently contains only a `README.md` describing three calculator goals. The README describes Goal 1 as a CLI calculator in TypeScript with two modes: single expression via args (`node calc.js 10 + 5`) and an interactive REPL. The final compiled entry point is expected to be invocable as `node calc.js`.

No `package.json`, `tsconfig.json`, or any TypeScript source files exist yet. This task creates all three from scratch.

**Conventions to follow based on the README:**
- The compiled output must be reachable as `node calc.js` (i.e., the output file should be named `calc.js` and placed where it can be run directly, either at the root or with a path that resolves simply)
- The project is plain TypeScript with no framework
- The entry point filename is `calc.ts`

**Recommended structure:**
- Place `calc.ts` at the project root (simplest path to `node calc.js` after compilation)
- `tsconfig.json` should emit output to the same directory or a predictable location
- `package.json` should include at minimum a `build` script (`tsc`) and a `start` script (`node calc.js`)

**`tsconfig.json` should include at minimum:**
- `"target"`: `"ES2020"` or later
- `"module"`: `"commonjs"` (for Node.js compatibility)
- `"strict"`: `true`
- `"outDir"` or `"rootDir"` as appropriate — if `calc.ts` is at the root and `outDir` is omitted or set to `"."`, the output `calc.js` will land at the root

**`calc.ts` content:**
- May be empty or contain only a no-op (e.g., an empty `export {}` or a comment) — no logic is required
- Must not produce any TypeScript compilation errors

**`package.json` should include:**
- `"name"`: `"fugue-poc-calculator"`
- `"version"`: `"1.0.0"`
- `"scripts"`: at minimum `"build": "tsc"` and `"start": "node calc.js"`
- `"devDependencies"`: `typescript` (version `^5.0.0` or similar current stable)

TypeScript should be installed as a dev dependency. A `node_modules` directory and `package-lock.json` will be generated after `npm install` but are not committed (assume a `.gitignore` will handle this, or add one).

## Scope

| File | Action |
|---|---|
| `package.json` | Create |
| `tsconfig.json` | Create |
| `calc.ts` | Create |
| `.gitignore` | Create |

## Out of Scope
- No calculator logic of any kind (no parsing, no arithmetic, no REPL, no CLI argument handling)
- No runtime dependencies beyond TypeScript as a dev dependency
- No test framework or test files
- No linting configuration
- No `src/` subdirectory structure — `calc.ts` lives at the root
- No web calculator files (Goal 2 from the README)
- No MCP server files (Goal 3 from the README)

## Acceptance Criteria

1. A `package.json` exists at the repository root with a `"build"` script that invokes `tsc` and a `"start"` script that invokes `node calc.js`.
2. A `tsconfig.json` exists at the repository root with `"module": "commonjs"` and `"strict": true` (or equivalent strict settings), targeting a Node.js-compatible ES version.
3. A `calc.ts` file exists at the repository root containing valid TypeScript that produces no compilation errors.
4. Running `tsc` (or `npm run build`) completes with exit code 0 and produces a `calc.js` file.
5. Running `node calc.js` on the compiled output completes with exit code 0 and does not throw any runtime error or print any error message.
6. A `.gitignore` exists that excludes at minimum `node_modules/` and any compiled `.js` output files (e.g., `calc.js`) from version control.
7. `typescript` appears as a dev dependency in `package.json`.