# Register the multiply and divide MCP tools

## Outcome
`src/mcp/index.ts` registers two additional MCP tools — `multiply` and `divide` — alongside the already-registered `add` and `subtract` tools. The `divide` tool returns `{ content: [{ type: 'text', text: 'Division by zero' }], isError: true }` when the denominator is zero instead of throwing. The file compiles without TypeScript errors.

## Why
The calculator MCP server is incomplete without multiplication and division. Exposing all four arithmetic operations makes the server useful as a Claude Desktop tool. Handling division by zero gracefully (via `isError`) keeps the MCP protocol contract intact — a thrown exception would surface as an unhandled server error rather than a structured, inspectable response.

## Context

### Stack
- TypeScript, `@modelcontextprotocol/sdk`, `zod`
- Entry point: `src/mcp/index.ts`
- stdio transport (required for Claude Desktop compatibility)

### Current state of `src/mcp/index.ts`
The prior task ("Register the add and subtract MCP tools") produced this file. It already contains:
- An `McpServer` instance named `calculator` at version `1.0.0`
- `add` and `subtract` tool registrations using `server.tool()`
- A `StdioServerTransport` connected via `await server.connect(transport)`

The file follows this pattern for each tool:

```typescript
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';
import { z } from 'zod';

const server = new McpServer({ name: 'calculator', version: '1.0.0' });

server.tool(
  'add',
  'Add two numbers',
  { a: z.number(), b: z.number() },
  async ({ a, b }) => ({ content: [{ type: 'text', text: String(a + b) }] }),
);

server.tool(
  'subtract',
  'Subtract two numbers',
  { a: z.number(), b: z.number() },
  async ({ a, b }) => ({ content: [{ type: 'text', text: String(a - b) }] }),
);

const transport = new StdioServerTransport();
await server.connect(transport);
```

### MCP tool registration pattern
Each tool is registered with `server.tool(name, description, zodSchema, asyncHandler)`. The handler returns `{ content: [{ type: 'text', text: string }] }`. For error cases, the handler returns `{ content: [...], isError: true }` — it does **not** throw.

### Division by zero handling
Per project convention (CLAUDE.md): division by zero must return a structured error response, not a thrown exception:
```typescript
{ content: [{ type: 'text', text: 'Division by zero' }], isError: true }
```

## Scope

| File | Action |
|---|---|
| `src/mcp/index.ts` | Modify |

## Out of Scope
- The `add` and `subtract` tool registrations must not be changed.
- The `McpServer` instantiation, imports, and transport setup must not be changed.
- No new files are created.
- No test files, linting configuration, or build scripts are added.
- This task does not implement the CLI calculator (`calc.ts`) or the web calculator (`calculator.html`).

**Dependency:** This task builds on the output of "Register the add and subtract MCP tools," which produces `src/mcp/index.ts` containing the `add` and `subtract` registrations, the server setup, and the transport connection. That file must already exist before this task begins.

## Acceptance Criteria

1. `src/mcp/index.ts` contains a `server.tool('multiply', ...)` registration that returns `{ content: [{ type: 'text', text: String(a * b) }] }` for inputs `a` and `b`.
2. `src/mcp/index.ts` contains a `server.tool('divide', ...)` registration that, when `b` is non-zero, returns `{ content: [{ type: 'text', text: String(a / b) }] }`.
3. The `divide` handler, when `b === 0`, returns exactly `{ content: [{ type: 'text', text: 'Division by zero' }], isError: true }` without throwing an exception.
4. The `add` and `subtract` tool registrations remain present and unchanged.
5. The `McpServer` instantiation (`{ name: 'calculator', version: '1.0.0' }`), all imports, and the `StdioServerTransport` connection are unchanged.
6. The file contains no TypeScript compilation errors (i.e., `tsc --noEmit` or equivalent passes cleanly).
7. The `multiply` and `divide` tool schemas each declare `{ a: z.number(), b: z.number() }` as their input shape.