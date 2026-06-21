# Scaffold the MCP Server TypeScript Project

## Outcome
The repository contains a valid `package.json` with all required MCP server dependencies, a `tsconfig.json` configured for strict TypeScript with ESM-compatible settings, a `src/mcp/index.ts` stub file, and an updated `.gitignore`. Running `npm install` completes without errors and the project is ready for MCP tool implementation.

## Why
Goal 3 of this POC requires a TypeScript MCP server that integrates with Claude Desktop. Before any tool logic can be written, the project scaffolding must exist: dependency declarations, TypeScript compiler configuration, and the entry point directory structure. Without this foundation, subsequent tasks that implement the arithmetic tools have nowhere to land.

## Context

### Repository Overview
This is `fugue-poc-calculator`, a multi-goal POC project. Goals 1 and 2 are already implemented (CLI calculator and web calculator). Goal 3 is the MCP server, which has not yet been implemented. No `src/` directory currently exists in the repository.

### Existing Files
- `README.md` — project overview, no source code
- `CLAUDE.md` — conventions and patterns for all three goals
- `orchestra-tasks/` — Fugue task planning documents (do not modify)
- `.claude/tasks/` — stale task docs from earlier runs (do not modify)

There is currently no `package.json`, `tsconfig.json`, `src/` directory, or `.gitignore` in the repository (or if a `package.json` exists from Goal 1, it targets CommonJS and must be updated/replaced to support the MCP server's ESM requirements — see the Goal 1 note below).

### Goal 1 vs Goal 3 Stack Difference
Goal 1 uses `"module": "commonjs"` and files at the project root. Goal 3 uses ESM-compatible settings and files under `src/mcp/`. The `tsconfig.json` for Goal 3 must use `"module": "NodeNext"` or `"module": "ESNext"` (not `"commonjs"`) because the MCP SDK imports use `.js` extensions and the `tsx` runner expects ESM. The Claude Desktop config in `CLAUDE.md` confirms this: it uses `--import tsx/esm`.

### MCP Server Entry Point Pattern
From `CLAUDE.md`, the intended `src/mcp/index.ts` content is:

```typescript
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';
import { z } from 'zod';

const server = new McpServer({ name: 'calculator', version: '1.0.0' });

// Tools will be registered here in a subsequent task

const transport = new StdioServerTransport();
await server.connect(transport);
```

The stub created in this task should be a minimal placeholder that imports compile cleanly — the actual tool registrations are out of scope.

### Required Dependencies
From `CLAUDE.md` and the task acceptance criteria:
- `@modelcontextprotocol/sdk` — MCP server SDK
- `zod` — input schema validation
- `tsx` — TypeScript execution for Claude Desktop integration (used as `--import tsx/esm`)

### Claude Desktop Integration
The server is run directly from TypeScript source via `tsx` (no compile step at runtime):
```json
{
  "mcpServers": {
    "calculator": {
      "command": "node",
      "args": ["--import", "tsx/esm", "/absolute/path/to/src/mcp/index.ts"]
    }
  }
}
```

### TypeScript Configuration Requirements
- `strict: true`
- ESM-compatible module settings (`"module": "NodeNext"` and `"moduleResolution": "NodeNext"`, or `"module": "ESNext"` with `"moduleResolution": "Bundler"`)
- `target` of `ES2020` or later (to support top-level `await`)
- `include` should cover `src/**/*`

### Conventions to Follow
- Use `npm` (not pnpm or yarn)
- No test framework, no linting
- No barrel exports
- The `src/mcp/` directory is the only subdirectory in this project

## Scope

| File | Action |
|---|---|
| `package.json` | Create / Modify |
| `tsconfig.json` | Create / Modify |
| `src/mcp/index.ts` | Create |
| `.gitignore` | Create / Modify |

## Out of Scope
- Implementing any MCP tools (`add`, `subtract`, `multiply`, `divide`) — those belong in a subsequent task
- Modifying `README.md`, `CLAUDE.md`, or any file under `orchestra-tasks/` or `.claude/tasks/`
- Adding a build script that compiles TypeScript to JavaScript — the server runs via `tsx` directly
- Adding test files or linting configuration
- Goal 1 (`calc.ts`, `operations.ts`, `cliMode.ts`, `replMode.ts`) and Goal 2 (`calculator.html`) files — do not touch these if they exist
- Claude Desktop config file — that is user-local configuration, not part of the repository

## Acceptance Criteria

1. Running `npm install` in the repository root completes without errors and produces a `node_modules/` directory.
2. `package.json` exists at the repository root and lists `@modelcontextprotocol/sdk`, `zod`, and `tsx` as dependencies (under `"dependencies"` or `"devDependencies"` — `tsx` may be a devDependency).
3. `tsconfig.json` exists at the repository root and contains `"strict": true`.
4. `tsconfig.json` contains ESM-compatible module settings: `"module"` is set to `"NodeNext"`, `"ESNext"`, or equivalent (not `"commonjs"`).
5. `tsconfig.json` sets `"target"` to `"ES2020"` or later.
6. The directory `src/mcp/` exists in the repository.
7. `src/mcp/index.ts` exists and contains at minimum an import of `McpServer` from `@modelcontextprotocol/sdk/server/mcp.js` and an import of `StdioServerTransport` from `@modelcontextprotocol/sdk/server/stdio.js`.
8. `src/mcp/index.ts` does not contain any `server.tool()` registrations (those are for the next task).
9. `.gitignore` exists and includes `node_modules/` so that installed packages are not tracked.
10. No files from Goal 1 (e.g., `calc.ts`, `operations.ts`) or Goal 2 (`calculator.html`) are deleted or modified.