# CLAUDE.md — fugue-poc-calculator

## Project Overview

A progressive POC project for the Fugue AI orchestration system. Three goals
demonstrating Fugue across different output types. Currently contains planning
artifacts (Orchestra task docs); source implementations are pending.

---

## Stack

**Goal 1 — CLI calculator (planned):**
- TypeScript, CommonJS (`"module": "commonjs"`) — not ESM
- npm (not pnpm), compiled with `tsc`
- Files at root — no `src/` directory
- Entry point: `calc.ts` → compiled to `calc.js`
- No test framework, no linting configured

**Goal 2 — Web calculator (planned):**
- Single file: `calculator.html` at root
- Vanilla JS and CSS inline — no framework, no build step
- No external dependencies

**Goal 3 — MCP server (not yet planned):**
- TypeScript
- `@modelcontextprotocol/sdk`
- `zod` for input schemas
- stdio transport (required for Claude Desktop)

---

## Directory Structure

```
fugue-poc-calculator/
├── README.md
├── CLAUDE.md
├── orchestra-tasks/        ← Fugue task docs (Goals 1 and 2, flat — naming collision)
│   ├── task-01-scaffold-the-typescript-project-*.md
│   ├── task-02-implement-the-four-arithmetic-*.md   (Goal 1)
│   ├── task-01-define-the-html-skeleton-*.md        (Goal 2, same prefix)
│   └── ...
└── .claude/tasks/          ← Task docs from early failed Fugue runs (stale)
```

**Note:** `orchestra-tasks/` has a flat structure where both Goal 1 and Goal 2
task docs use `task-01`, `task-02` prefixes without differentiation. Future runs
use `orchestra/<feature-slug>/` to avoid this collision.

---

## Commands

**Goal 1 (once implemented):**
```bash
npm install
npm run build        # tsc
node calc.js 10 + 5  # single expression
node calc.js         # interactive REPL
```

**Goal 2 (once implemented):**
```bash
open calculator.html  # no build step — open directly in browser
```

**Goal 3 (to be defined in task docs):**
- Entry: `src/mcp/index.ts` (planned)

---

## Conventions

From Goal 1 task docs:
- TypeScript files at project root — not in `src/`
- CommonJS modules for Node.js compatibility
- `tsconfig.json` with `"strict": true`, `"module": "commonjs"`, target ES2020+
- Compiled output at root — `calc.ts` → `calc.js`
- Separate modules per concern: `operations.ts`, `cliMode.ts`, `replMode.ts`, `calc.ts`
- No barrel exports — each module imported directly by path

From Goal 2 task docs:
- Everything in one `calculator.html` file
- Data attributes for JS targeting: `data-value="7"`, `data-action="clear"`
- IDs for structural elements: `#calculator`, `#display`, `#button-grid`
- Standard `<button>` elements — no custom elements

---

## Patterns to Follow

**CLI module structure (Goal 1):**
- `operations.ts` — pure arithmetic functions, no I/O
- `cliMode.ts` — single-expression parser and runner
- `replMode.ts` — interactive REPL loop
- `calc.ts` — entry point, routes via `process.argv.length`

**HTML data attribute convention (Goal 2):**
- Buttons that produce input: `data-value="<char>"`
- Action buttons: `data-action="clear"`, `data-action="backspace"`, `data-action="equals"`
- DOM order: `C ⌫ / *` → `7 8 9 -` → `4 5 6 +` → `1 2 3 =` → `0 .`

**MCP tool pattern (Goal 3):**
- Each operation is a separate `server.tool()` registration
- Zod schemas for typed inputs
- Returns `{ content: [{ type: 'text', text: String(result) }] }`
- Division by zero returns `{ content: [...], isError: true }` — not a thrown exception

---

## Do Not

- Do not use ESM (`"type": "module"`) for Goal 1 — task docs specify CommonJS
- Do not put Goal 1 files in `src/` — they live at the project root
- Do not use a build step for Goal 2 — `calculator.html` is self-contained
- Do not use pnpm for Goals 1 and 2 — task docs specify npm
- Do not add test files or linting — not in scope for any goal
- Do not merge Goal 1 and Goal 2 into one entry point — they are separate files

---

## Goal 3 — MCP server context

Entry point: `src/mcp/index.ts` (the only subdirectory in this project).

```typescript
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';
import { z } from 'zod';

const server = new McpServer({ name: 'calculator', version: '1.0.0' });

server.tool('add', 'Add two numbers',
  { a: z.number(), b: z.number() },
  async ({ a, b }) => ({ content: [{ type: 'text', text: String(a + b) }] }),
);

// repeat for subtract, multiply, divide

const transport = new StdioServerTransport();
await server.connect(transport);
```

**Claude Desktop config:**
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

---

## Notes

- No source code exists yet. Patterns are inferred from task docs. Once Goals 1
  and 2 are implemented, regenerate this file via `/onboard-project`.
- `orchestra-tasks/` has a naming collision between Goal 1 and Goal 2 task docs.
  Future runs land in `orchestra/<feature-slug>/` to avoid this.
- Goal 3 stack is based on SDK documentation, not observed code.
