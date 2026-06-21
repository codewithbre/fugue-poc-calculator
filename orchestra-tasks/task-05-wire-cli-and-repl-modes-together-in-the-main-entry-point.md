# Wire CLI and REPL Modes Together in the Main Entry Point

## Outcome
`calc.ts` (compiled to `calc.js`) serves as the unified entry point for the calculator. It inspects `process.argv` and routes execution to one of three outcomes:
- **Three operand arguments** (`node calc.js <num> <op> <num>`): delegates to single-expression mode and prints the result.
- **No arguments** (`node calc.js`): delegates to interactive REPL mode.
- **Any other argument count**: prints a usage message to stderr and exits with a non-zero exit code.

## Why
Without a wired entry point, the CLI and REPL implementations are isolated modules with no way to invoke them from the command line. This task connects all the pieces so the calculator is usable as a complete tool.

## Context
This project is a TypeScript CLI calculator (`fugue-poc-calculator`). Two prior tasks have produced the following modules (their outputs are assumed to exist in the project root):

1. **Single-expression mode** — a module (e.g., `cliMode.ts` or similar) that accepts three arguments (left operand, operator, right operand), evaluates the expression, prints the result, and handles division-by-zero with a clear error message.
2. **Interactive REPL mode** — a module (e.g., `replMode.ts` or similar) that starts an interactive prompt, reads expressions line by line, evaluates them, and prints results until the user exits.

The main entry point `calc.ts` must import from both of these modules and use `process.argv` to decide which to invoke. `process.argv[0]` is `node`, `process.argv[1]` is the script path, so user-supplied arguments begin at `process.argv[2]`.

The project uses TypeScript and is compiled to JavaScript (the README references `calc.js` as the runnable artifact). Follow the same TypeScript conventions already present in the codebase (imports, types, etc.).

**Argument routing logic:**
- `process.argv.length === 5` → three user args present → single-expression mode with `process.argv[2]`, `process.argv[3]`, `process.argv[4]`
- `process.argv.length === 2` → no user args → REPL mode
- anything else → print usage to stderr, `process.exit(1)`

**Usage message format (example):**
```
Usage:
  node calc.js <number> <operator> <number>   # single expression
  node calc.js                                 # interactive REPL
Operators: +, -, *, /
```

## Scope

| File | Action |
|---|---|
| `calc.ts` | Create |

## Out of Scope
- Implementing single-expression evaluation logic — that is provided by the prior task "Implement single-expression mode (CLI args)" as an importable module.
- Implementing the REPL loop logic — that is provided by the prior task "Implement interactive REPL mode" as an importable module.
- Modifying the CLI or REPL modules themselves.
- Any web calculator or MCP server files mentioned in the README.
- Build configuration, `tsconfig.json`, or `package.json` changes (assume they already exist and compile `calc.ts` to `calc.js`).

## Acceptance Criteria

1. `calc.ts` exists in the project root and contains a main entry point that reads `process.argv`.
2. When `process.argv` contains exactly three user-supplied arguments (e.g., `10 + 5`), `calc.ts` imports and calls the single-expression mode module, passing those three arguments.
3. When `process.argv` contains no user-supplied arguments, `calc.ts` imports and calls the REPL mode module.
4. When `process.argv` contains any other number of user-supplied arguments (e.g., only one argument like `10`), `calc.ts` prints a usage message to stderr and calls `process.exit` with a non-zero code (e.g., `1`).
5. The usage message printed in criterion 4 references both valid invocation forms: the three-argument form and the no-argument REPL form.
6. `calc.ts` does not contain any arithmetic evaluation logic itself — all computation is delegated to the imported modules.
7. Running `node calc.js 10 + 5` (after compilation) prints `15` and exits with code `0`.
8. Running `node calc.js` (after compilation) enters the REPL prompt without error.
9. Running `node calc.js 10` (after compilation) prints a usage message and exits with a non-zero code.