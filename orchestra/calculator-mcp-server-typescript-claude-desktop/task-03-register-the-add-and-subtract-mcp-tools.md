# Register the add and subtract MCP tools

## Outcome
`src/mcp/index.ts` exists and registers two MCP tools — `add` and `subtract` — using `server.tool()`. Each tool accepts a Zod-typed input schema `{ a: z.number(), b: z.number() }` and returns `{ content: [{ type: 'text', text: String(result) }] }`. The file compiles without TypeScript errors.

## Why
The MCP server needs tool registrations to expose calculator operations to clients such as Claude Desktop. Without these registrations, the server starts but advertises no capabilities. Wiring up `add` and `subtract` as the first two tools establishes the pattern that `multiply` and `divide` will follow and makes the server immediately useful for basic arithmetic.

## Context

### Project layout
The MCP server lives under `src/mcp/`. The entry point is `src/mcp/index.ts`. This is the only subdirectory in the project; all other source files live at the project root.

### Dependency: arithmetic operations module
This task depends on a prior task — **"Implement the arithmetic operations module"** — which produces a module exporting `add` and `subtract` functions. That module is expected at `src/mcp/operations.ts` (or a similar path within `src/mcp/`). The functions have the signature `(a: number, b: number) => number`. Import them by direct path — no barrel exports are used in this project.

### SDK and tooling
- Package: `@modelcontextprotocol/sdk`
- Zod is used for input schemas: `import { z } from 'zod'`
- Server class: `McpServer` from `@modelcontextprotocol/sdk/server/mcp.js`
- Transport: `StdioServerTransport` from `@modelcontextprotocol/sdk/server/stdio.js`
- The file uses top-level `await` (for `server.connect(transport)`)

### MCP tool registration pattern
From `CLAUDE.md`, the canonical shape for a tool registration is:

```typescript
server.tool(
  'add',
  'Add two numbers',
  { a: z.number(), b: z.number() },
  async ({ a, b }) => ({ content: [{ type: 'text', text: String(a + b) }] }),
);
```

Arguments in order: tool name (string), description (string), input schema (Zod object shape), async handler returning the content envelope.

### Return shape
Every tool handler must return:
```typescript
{ content: [{ type: 'text', text: String(result) }] }
```

### Server bootstrap
After all tool registrations, the server connects via stdio:
```typescript
const transport = new StdioServerTransport();
await server.connect(transport);
```

### TypeScript configuration
- `"strict": true`
- Target ES2020 or later
- The project uses `tsx` for execution (no separate compile step at runtime), but the file must still be valid TypeScript

## Scope

| File | Action |
|---|---|
| `src/mcp/index.ts` | Create |

## Out of Scope
- `multiply` and `divide` tool registrations are **not** part of this task
- No changes to `tsconfig.json`, `package.json`, or any other configuration file
- No changes to the arithmetic operations module itself — this task only consumes it
- No test files
- No Claude Desktop configuration changes
- The arithmetic operations module (`src/mcp/operations.ts` or equivalent) is produced by the prior task **"Implement the arithmetic operations module"** and must already exist before this task is complete. This task does not create or modify that file.

## Acceptance Criteria

1. `src/mcp/index.ts` exists in the repository.
2. The file imports `McpServer` from `@modelcontextprotocol/sdk/server/mcp.js` and `StdioServerTransport` from `@modelcontextprotocol/sdk/server/stdio.js`.
3. The file imports `z` from `zod`.
4. The file imports `add` and `subtract` from the arithmetic operations module by direct file path (no barrel import).
5. `server.tool('add', ...)` is called with input schema `{ a: z.number(), b: z.number() }` and its handler returns `{ content: [{ type: 'text', text: String(result) }] }` where `result` is the return value of the imported `add` function.
6. `server.tool('subtract', ...)` is called with input schema `{ a: z.number(), b: z.number() }` and its handler returns `{ content: [{ type: 'text', text: String(result) }] }` where `result` is the return value of the imported `subtract` function.
7. A `StdioServerTransport` instance is created and passed to `server.connect(transport)` via top-level `await` after all tool registrations.
8. No other tool registrations (`multiply`, `divide`, or any other name) appear in the file.
9. Running `tsc --noEmit` (or equivalent TypeScript type-check) against the file produces zero errors.