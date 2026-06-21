# Document Claude Desktop Configuration and Usage

## Outcome

`README.md` contains a new subsection under the Goal 3 section titled "Claude Desktop Setup" (or equivalent) that includes:

1. The exact `mcpServers` JSON configuration block a user must paste into their Claude Desktop config file, with the correct `command`, `args` array, and a clearly marked absolute-path placeholder.
2. Step-by-step instructions for connecting the server to Claude Desktop.
3. Instructions for running the server manually from the terminal to verify it starts correctly before connecting it to Claude Desktop.

A reader can follow the documentation end-to-end to connect the calculator MCP server to Claude Desktop without consulting any external source.

## Why

The MCP server is only useful if it can be connected to a client. Claude Desktop requires a specific JSON configuration shape in a specific config file. Without documented setup instructions, a developer who has built and installed the server has no clear path to actually use it. This documentation closes the gap between "server exists" and "server is running inside Claude Desktop."

## Context

### Repository layout

The project is `fugue-poc-calculator`. Relevant files:

- **`README.md`** — Top-level readme. Contains three goal sections. Goal 3 section currently reads:
  ```
  ## Goal 3 — Calculator MCP server
  **Issue title to use:**
  ```Build a calculator MCP server in TypeScript…```
  **What this tests:** Breakdown quality on a newer protocol…
  ```
  There is no setup or usage documentation in this section yet.

- **`CLAUDE.md`** — Internal agent context file. Already contains the canonical Claude Desktop config block and the entry point path:
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
  Entry point: `src/mcp/index.ts`

### MCP server implementation (prior task output)

The prior task "Connect the MCP server to the stdio transport" produces `src/mcp/index.ts`. That file:
- Imports `McpServer` from `@modelcontextprotocol/sdk/server/mcp.js`
- Imports `StdioServerTransport` from `@modelcontextprotocol/sdk/server/stdio.js`
- Registers four tools: `add`, `subtract`, `multiply`, `divide`
- Connects via `new StdioServerTransport()` and `await server.connect(transport)`

The server is run directly as a TypeScript file using `tsx` (ESM mode), not compiled first. The `--import tsx/esm` flag is what enables this.

### Stack for Goal 3

- TypeScript, ESM via `tsx`
- `@modelcontextprotocol/sdk`
- `zod`
- stdio transport (required for Claude Desktop)
- Entry point: `src/mcp/index.ts`

### Conventions

- README uses `##` for goal sections and `###` or bold text for subsections.
- Code blocks in README use triple-backtick fences with language tags (`bash`, `json`).
- The project uses `npm` (not pnpm).

## Scope

| File | Action |
|---|---|
| `README.md` | Modify |

## Out of Scope

- No changes to `src/mcp/index.ts` or any other source file.
- No changes to `CLAUDE.md`.
- No new files (e.g., `docs/mcp-setup.md`) — the documentation goes into `README.md`.
- No changes to Goal 1 or Goal 2 sections of `README.md`.
- No `package.json` scripts, no `tsconfig.json` changes.
- This task does not implement the MCP server itself. It depends on the prior task "Connect the MCP server to the stdio transport" having produced `src/mcp/index.ts` with a working stdio transport. The documentation describes how to run that file.

## Acceptance Criteria

1. `README.md` contains a subsection within or immediately after the Goal 3 section that is clearly titled to indicate Claude Desktop setup or MCP server usage.

2. The subsection contains a JSON code block with exactly this shape:
   ```json
   {
     "mcpServers": {
       "calculator": {
         "command": "node",
         "args": ["--import", "tsx/esm", "<absolute-path>/src/mcp/index.ts"]
       }
     }
   }
   ```
   where `<absolute-path>` (or equivalent placeholder text) is clearly marked as something the reader must replace with the actual absolute path to their local clone of the repository.

3. The subsection states where the Claude Desktop config file lives (e.g., `~/Library/Application Support/Claude/claude_desktop_config.json` on macOS) so the reader knows where to paste the JSON block.

4. The subsection includes a `bash` code block showing how to run the server manually for testing, using the form:
   ```bash
   node --import tsx/esm src/mcp/index.ts
   ```
   (run from the project root), so a reader can verify the server starts before connecting it to Claude Desktop.

5. The manual-run instructions note that the server communicates over stdio and will appear to hang (no output) when started successfully — this is expected behavior for an MCP stdio server.

6. The Goal 1 and Goal 2 sections of `README.md` are unchanged.

7. No other files in the repository are modified.