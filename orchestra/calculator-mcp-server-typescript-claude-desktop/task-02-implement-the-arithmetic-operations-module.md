# Implement the Arithmetic Operations Module

## Outcome
`src/mcp/operations.ts` exists and exports four named functions: `add`, `subtract`, `multiply`, and `divide`. All four accept two numeric arguments and return correct numeric results for valid inputs. `divide` returns a structured error value (not a thrown exception) when the divisor is zero.

## Why
The MCP server (Goal 3) needs a pure-logic layer that handles arithmetic independently of any transport or protocol concerns. Separating arithmetic into its own module keeps the MCP tool registrations thin, makes the logic independently readable, and mirrors the same separation used in Goal 1 (`operations.ts` at the project root). This module is the foundation that the MCP server entry point will import.

## Context

### Project structure
The project is a TypeScript MCP calculator server (Goal 3). The entry point will live at `src/mcp/index.ts`. The `src/mcp/` directory is the only subdirectory in the project. This task creates `src/mcp/operations.ts`, which the entry point will later import.

### Scaffold prerequisite
The prior task ("Scaffold the MCP server TypeScript project") produces:
- A `package.json` with `@modelcontextprotocol/sdk` and `zod` as dependencies, and `tsx` available for running TypeScript directly.
- A `tsconfig.json` with `"strict": true` and settings appropriate for the MCP server.
- The `src/mcp/` directory.

This task does **not** create those files — it only adds `src/mcp/operations.ts` into the already-scaffolded structure.

### Conventions to follow (from CLAUDE.md)
- **No I/O, no MCP concerns** in this file — pure arithmetic only.
- **No barrel exports** — the module is imported directly by path.
- **CommonJS** is used for Goal 1, but Goal 3 uses the MCP SDK which may use ESM imports (e.g. `@modelcontextprotocol/sdk/server/mcp.js`). Follow whatever module format the scaffold's `tsconfig.json` specifies; do not introduce a conflicting `"type"` field.
- `divide` by zero must **not throw** — it must return a value that signals an error. The pattern established in CLAUDE.md for the MCP layer is `{ content: [...], isError: true }`, but at the pure-logic layer a discriminated union or a plain `{ error: string }` object is appropriate. Use a discriminated union so callers can type-narrow cleanly.

### Division by zero return shape
Return a discriminated union from `divide`:

```typescript
type DivideResult =
  | { ok: true; value: number }
  | { ok: false; error: string };
```

`divide(10, 2)` → `{ ok: true, value: 5 }`  
`divide(10, 0)` → `{ ok: false, error: 'Division by zero' }`

The other three functions (`add`, `subtract`, `multiply`) return `number` directly — they have no error cases.

### Example shape of the finished file
```typescript
export function add(a: number, b: number): number { ... }
export function subtract(a: number, b: number): number { ... }
export function multiply(a: number, b: number): number { ... }

export type DivideResult =
  | { ok: true; value: number }
  | { ok: false; error: string };

export function divide(a: number, b: number): DivideResult { ... }
```

## Scope

| File | Action |
|---|---|
| `src/mcp/operations.ts` | Create |

## Out of Scope
- `src/mcp/index.ts` — the MCP server entry point is **not** created or modified here.
- `package.json`, `tsconfig.json` — produced by the scaffold task; must not be changed.
- Any test files — no test framework is in scope for this project.
- Any linting configuration — not in scope.
- Goal 1 files at the project root (`operations.ts`, `calc.ts`, etc.) — must not be touched.
- `calculator.html` (Goal 2) — must not be touched.

## Acceptance Criteria

1. `src/mcp/operations.ts` exists in the repository.
2. The file exports a function named `add` that accepts two `number` parameters and returns their sum as a `number` (e.g. `add(3, 4)` → `7`).
3. The file exports a function named `subtract` that accepts two `number` parameters and returns their difference as a `number` (e.g. `subtract(10, 3)` → `7`).
4. The file exports a function named `multiply` that accepts two `number` parameters and returns their product as a `number` (e.g. `multiply(3, 4)` → `12`).
5. The file exports a function named `divide` that accepts two `number` parameters.
6. `divide(10, 2)` returns a value indicating success with the numeric result `5` — not a thrown exception and not an error signal.
7. `divide(10, 0)` returns a value that signals an error (e.g. `{ ok: false, error: 'Division by zero' }`) — it does **not** throw, does not return `Infinity`, and does not return `NaN`.
8. The exported `DivideResult` type (or equivalent discriminated union) is present in the file and used as the return type annotation of `divide`.
9. The file contains no imports from `@modelcontextprotocol/sdk`, `zod`, or any I/O-related module — it is pure logic with no external runtime dependencies.
10. No other existing files in the repository are modified.