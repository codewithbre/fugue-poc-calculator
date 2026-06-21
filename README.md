# calculator

A CLI calculator built in TypeScript. Fugue POC test project.

## What to build

A command-line calculator that:
- Supports add, subtract, multiply, divide
- Accepts input interactively (REPL) or as a single expression via args
- Handles division by zero with a clear error message
- Is written in TypeScript, runs with `tsx` or compiled Node.js

## Stack

- TypeScript, ESM
- No external dependencies beyond `tsx` for running

## Example usage

```bash
node calc.js 10 + 5     # → 15
node calc.js 10 / 0     # → Error: division by zero
node calc.js            # → starts REPL mode
```
