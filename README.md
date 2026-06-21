# fugue-poc-calculator

Test project for the Fugue runtime POC (orchestra coding agent). Three progressively complex goals, each run as a separate Fugue session.

---

## Goal 1 — CLI calculator

**Issue title to use:**
```
Build a command-line calculator in TypeScript. Two modes: single expression via args (node calc.js 10 + 5) and interactive REPL. Operations: add, subtract, multiply, divide. Handle division by zero with a clear error.
```

**What this tests:** Basic breakdown and write-task quality on a well-understood problem. Expected 4–5 tasks with clear scope boundaries.

---

## Goal 2 — Web app calculator

**Issue title to use:**
```
Build a web calculator as a single self-contained HTML file. No framework, no build step — vanilla JS and CSS only. Standard calculator layout with button input and keyboard support.
```

**What this tests:** UI decomposition, constraint handling (no framework), whether breakdown separates layout/logic/interaction cleanly.

---

## Goal 3 — Calculator MCP server

**Issue title to use:**
```
Build a calculator MCP server in TypeScript using the @modelcontextprotocol/sdk. Expose add, subtract, multiply, and divide as MCP tools with typed inputs and outputs. Compatible with Claude Desktop.
```

**What this tests:** Breakdown quality on a newer protocol. MCP tool schema shape is the hard part — breakdown needs to get the input/output types right.

---

## How to trigger Fugue

Fugue is triggered from the CLI for now (GitHub Actions requires the runtime to be published — see note below):

```bash
node --env-file=packages/fugue/runtime/.env \
  packages/fugue/runtime/src/index.ts \
  --task "<issue title>" \
  --repo "codewithbre/fugue-poc-calculator" \
  --issue <issue-number>
```

**Gate 1:** Fugue posts the task breakdown as a comment on the issue. Reply `approved` to continue.
**Gate 2:** Fugue opens a draft PR per task. Implement and merge each PR. Fugue advances to the next task automatically.
**Final step:** Fugue posts a `/reprise` retrospective issue automatically after all tasks merge.

### Why not GitHub Actions yet

The Fugue runtime source lives in `packages/fugue/runtime/src/` which is gitignored (source is private). GitHub Actions can't check out files that aren't in the repo. The Actions trigger becomes available once the runtime is either published to npm or the source is made public.
