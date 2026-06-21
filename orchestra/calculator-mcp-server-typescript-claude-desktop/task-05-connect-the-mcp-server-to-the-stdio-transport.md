# Connect the MCP Server to the stdio Transport

## Outcome
`src/mcp/index.ts` instantiates a `StdioServerTransport` and calls `server.connect(transport)` at the end of the file. The MCP server process starts without errors when invoked with `node --import tsx/esm src/mcp/index.ts`, remains alive waiting on stdin, and does not exit immediately or produce unhandled promise rejections.

## Why
Without connecting the server to a transport, the MCP server is never actually reachable — it cannot receive or respond to any messages. The stdio transport is the mechanism Claude Desktop uses to communicate with MCP servers. Wiring the transport is the final step that makes the calculator MCP server fully operational and compatible with Claude Desktop's stdio-based MCP protocol.

## Context

### Entry point file
The file being modified is `src/mcp/index.ts`. This is the sole source file for Goal 3 of this project. It already imports `McpServer` from `@modelcontextprotocol/sdk/server/mcp.js` and registers four arithmetic tools (`add`, `subtract`, `multiply`, `divide`) using `server.tool()` with Zod schemas.

The file does **not yet** import `StdioServerTransport` or call `server.connect(transport)`. Those two things are what this task adds.

### Expected final shape of the file (from CLAUDE.md)
```typescript
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';
import { z } from 'zod';

const server = new McpServer({ name: 'calculator', version: '1.0.0' });

server.tool('add', 'Add two numbers',
  { a: z.number(), b: z.number() },
  async ({ a, b }) => ({ content: [{ type: 'text', text: String(a + b) }] }),
);

// subtract, multiply, divide registered the same way

const transport = new StdioServerTransport();
await server.connect(transport);
```

### Transport import path
`StdioServerTransport` is imported from `@modelcontextprotocol/sdk/server/stdio.js` (note the `.js` extension — required for ESM resolution with `tsx/esm`).

### Runtime invocation
The server is run with:
```bash
node --import tsx/esm src/mcp/index.ts
```
This uses `tsx` to handle TypeScript ESM files directly without a separate compile step.

### Tool registration pattern (produced by the prior task)
Each tool follows this pattern:
```typescript
server.tool(
  '<name>',
  '<description>',
  { a: z.number(), b: z.number() },
  async ({ a, b }) => ({ content: [{ type: 'text', text: String(<result>) }] }),
);
```
Division by zero returns `{ content: [...], isError: true }` rather than throwing.

### Package and module conventions for Goal 3
- TypeScript with ESM (this is the exception to the CommonJS convention used in Goals 1 and 2)
- `tsx/esm` is used as the runtime loader — no `tsc` compile step
- `@modelcontextprotocol/sdk` and `zod` are dependencies
- `src/mcp/index.ts` is the only file in the MCP server

## Scope

| File | Action |
|---|---|
| `src/mcp/index.ts` | Modify |

## Out of Scope
- This task does **not** register any new tools. The `add`, `subtract`, `multiply`, and `divide` tools are registered by the prior task ("Register the multiply and divide MCP tools"), which produces a `src/mcp/index.ts` that already contains all four `server.tool()` calls.
- This task does **not** modify `package.json`, `tsconfig.json`, or any other configuration file.
- This task does **not** add Claude Desktop configuration files.
- This task does **not** add tests or linting.
- This task does **not** change the tool handler logic or Zod schemas.

## Acceptance Criteria

1. `src/mcp/index.ts` contains an import of `StdioServerTransport` from `@modelcontextprotocol/sdk/server/stdio.js`.
2. `src/mcp/index.ts` contains the lines `const transport = new StdioServerTransport();` and `await server.connect(transport);` at the bottom of the file, after all `server.tool()` registrations.
3. Running `node --import tsx/esm src/mcp/index.ts` starts the process and the process does not exit immediately — it remains alive, blocking on stdin.
4. No error output (stderr) is produced when the process starts under the command above.
5. No unhandled promise rejection messages appear in the output when the process starts.
6. All four tool registrations (`add`, `subtract`, `multiply`, `divide`) remain present and unmodified in the file.