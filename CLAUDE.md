# CLAUDE.md — fugue-poc-calculator

## What this is

A progressive POC project for the Fugue orchestration system. Three goals:

- **Goal 1 (done):** CLI calculator — `node calc.js 10 + 5` and interactive REPL
- **Goal 2 (done):** Web calculator — single self-contained HTML file, vanilla JS/CSS
- **Goal 3 (in progress):** Calculator MCP server — exposes operations as MCP tools

---

## Stack

- TypeScript, ESM (`"type": "module"`)
- Node.js 20+
- `tsx` for running TypeScript directly without a compile step

---

## Goal 3 — MCP server context

Goal 3 implements a Model Context Protocol server. An agent implementing this
goal must understand the following conventions.

### SDK and dependencies

```json
{
  "dependencies": {
    "@modelcontextprotocol/sdk": "latest",
    "zod": "latest"
  }
}
```

### MCP tool definition pattern

Each calculator operation (add, subtract, multiply, divide) is a separate MCP tool.

```typescript
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import { z } from 'zod';

const server = new McpServer({ name: 'calculator', version: '1.0.0' });

server.tool(
  'add',
  'Add two numbers',
  {
    a: z.number().describe('First operand'),
    b: z.number().describe('Second operand'),
  },
  async ({ a, b }) => ({
    content: [{ type: 'text', text: String(a + b) }],
  }),
);
```

### Transport — stdio (required for Claude Desktop)

```typescript
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';

const transport = new StdioServerTransport();
await server.connect(transport);
```

### Entry point

```typescript
// src/mcp/index.ts
```

### Claude Desktop config (for verification)

```json
{
  "mcpServers": {
    "calculator": {
      "command": "node",
      "args": ["--import", "tsx/esm", "/path/to/src/mcp/index.ts"]
    }
  }
}
```

### Division by zero

Return an MCP error, not a JavaScript exception:

```typescript
if (b === 0) {
  return {
    content: [{ type: 'text', text: 'Error: division by zero' }],
    isError: true,
  };
}
```

---

## File conventions

```
src/
  cli/          Goal 1 — CLI calculator
  web/          Goal 2 — web calculator (single HTML file)
  mcp/          Goal 3 — MCP server
    index.ts    Entry point — connects server to stdio transport
    server.ts   McpServer instance with all four tool registrations
```

---

## Do not

- Do not add a build step — all goals run directly with `tsx`
- Do not use a web framework for Goal 2 — vanilla JS/CSS only
- Do not add goals beyond 1, 2, 3 without a new task plan
