---
name: node
description: Node.js 24 conventions for TypeScript servers, daemons and CLIs, with Hono, zod 4 and Vitest 5.
---

## Language

- TypeScript strict, ES modules with `module: nodenext`. Relative imports end in `.js`, because Node resolves the built output. No `require` or `__dirname`; use `import.meta.dirname`.
- No `any`, and no casts or non-null assertions to silence the compiler. `String(x)`, `x ?? ''` and `` `${x}` `` on a `T | undefined` are casts too. Data from outside (files, process output, request bodies) is `unknown` until a zod schema parses it.
- Restrict install scripts with `allowScripts` in `package.json` together with `strict-allow-scripts=true` in `.npmrc`; without the `.npmrc` setting, `allowScripts` does nothing.
- Import built-ins with the `node:` prefix, and use them before adding a package: `fetch`, `crypto.randomUUID()`, `AbortSignal.timeout()`, `util.parseArgs`, `node:sqlite`, not `uuid`, `axios` or `yargs`.

## Runtime

- Never leave a promise floating: an unhandled rejection kills the process. Await it or end it with a logging `.catch`; typescript-eslint's `no-floating-promises` checks this.
- Write a file atomically: write a temp file in the same directory, `await handle.sync()`, close it, then `rename` it over the target. A temp file in `os.tmpdir()` fails with `EXDEV` when `/tmp` is a different filesystem.
- Start external programs with `execFile` or `spawn` and an argument array, never `exec` or `shell: true`. Handle the `error` event as well as the exit code, because a missing binary arrives as `ENOENT` on `error`, not as a non-zero exit. Give every call a timeout or an `AbortSignal`.
- `setTimeout` holds at most 2^31−1 ms (about 24.8 days); a longer delay fires at once. Its clock also stops while the machine is suspended, so a timer due during sleep fires late by the sleep time. Code that must act at a wall-clock time compares against `Date.now()` on a short tick, or reschedules on wake.
- Handle `SIGTERM` and `SIGINT`: stop timers, close the server, finish pending writes, then exit. systemd stops services with `SIGTERM`.

## Hono

- Serve with `serve({ fetch: app.fetch, port, hostname })` from `@hono/node-server` 2. Omitting `hostname` makes Node listen on all interfaces.
- Validate with `zValidator('json', schema)` from `@hono/zod-validator` and read `c.req.valid('json')`, never an unchecked `c.req.json()`.
- A value an earlier middleware checked is passed on with `c.set`, not read raw from the request again. Type it on the app or middleware that sets it, `new Hono<{ Variables: ... }>()` or `createMiddleware<Env>`, never by augmenting hono's global `ContextVariableMap`, which types it in every context whether or not it was set.
- SSE uses `streamSSE` from `hono/streaming` and `stream.writeSSE({ event, data })`. Unsubscribe the client in `stream.onAbort`, and catch errors inside the callback: they never reach `app.onError`.

## zod 4

Models still write zod 3. The current forms:

- String formats are top-level: `z.email()`, `z.uuid()`, `z.url()`, `z.iso.datetime()`, `z.iso.date()`, not `z.string().email()`. `z.uuid()` checks the RFC variant bits; use `z.guid()` for any 8-4-4-4-12 hex.
- `z.iso.datetime()` accepts a fraction of any length unless `precision` is set, so store `new Date(x).toISOString()`, not the string as sent.
- Custom messages use `error`, not `message`, `invalid_type_error`, `required_error` or `errorMap`.
- `z.strictObject()` and `z.looseObject()` replace `.strict()` and `.passthrough()`; `.extend()` replaces `.merge()`. `z.record()` takes both key and value schemas.
- `.default()` returns its value without parsing it, so it must already be the output type.
- `z.treeifyError(err)` or `err.issues` replace `.format()`, `.flatten()` and `.errors`.
- Export `type X = z.infer<typeof XSchema>` next to each schema instead of declaring the type a second time.

## Testing

- Vitest 5. `vi.mock`, `vi.hoisted` and `vi.unmock` must be at the top level of the file; nested calls throw. Mocks are cleared before each test by default.
- Prefer passing fakes through the code's own interfaces over `vi.mock` of modules. A module mock couples the test to the import graph.
- Fake time with `vi.useFakeTimers()` and `vi.setSystemTime()`, and advance with `await vi.advanceTimersByTimeAsync(ms)`. The sync `advanceTimersByTime` does not run promise callbacks between timers, so async code under test stalls.
- `test.sequential` is removed; use `test(name, { concurrent: false }, fn)`. `-t` filters match the full name joined with ` > `.
- Test Hono routes with `await app.request(path, init)`, without opening a port.
- File tests use a fresh `fs.mkdtemp` directory, removed in `afterEach`.

## Checks

- `tsc --noEmit`, and ESLint with typescript-eslint's type-checked rules, pass.
