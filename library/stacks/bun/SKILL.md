---
name: bun
description: Bun runtime conventions for TypeScript servers and tools, tested with bun:test.
---

## Language

- TypeScript in strict mode. Bun runs server code directly, and `tsc --noEmit` only checks types. Browser code, if any, has its own `tsconfig.json` with the DOM lib and no Bun types, so server APIs fail its typecheck.
- ES modules only. Import local files with their `.ts` extension, as `allowImportingTsExtensions` expects; no `require`, no `__dirname` (use `import.meta.url` or `import.meta.dir`).
- Keep types exact: no `any`, and no casts or non-null assertions to silence the compiler. Data from outside (JSON files, process output, request bodies) arrives as `unknown` and is narrowed before use.

## Runtime APIs

- Use Bun's built-ins before adding a package: `Bun.serve` for HTTP, `Bun.file` and `Bun.write` for files, `Bun.spawn` for processes, global `fetch`, and the `.env` loading Bun does by itself. Packages such as `express`, `node-fetch`, `dotenv`, `execa` or `ts-node` duplicate the runtime.
- Start processes with `Bun.spawn([cmd, ...args])` and an argument array, read output from `proc.stdout`, and await `proc.exited` for the exit code. Never build a shell string from data.
- `node:` modules work and are fine where Bun has no own API, such as `node:path` or `fs/promises` `rename`.

## Testing

- `bun:test` only: `import { describe, expect, test, mock, spyOn, setSystemTime } from "bun:test"`. Never import from `vitest` or `@jest/globals`.
- Fix the clock by passing `now` into the code under test, or with `setSystemTime`; reset it in `afterEach`.
- Replace a module boundary with `mock.module(...)` only for I/O such as a process or network call, and restore spies with `mockRestore`.
- Run one file with `bun test tests/<file>.test.ts`, or filter with `bun test -t "<name>"`.

## Checks

- `bun run typecheck` and `bun test` pass.
- `bun install --frozen-lockfile` succeeds, so `bun.lock` matches `package.json`.
