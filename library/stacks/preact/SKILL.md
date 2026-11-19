---
name: preact
description: Preact 11 with TypeScript, hooks and Vite 8 conventions.
---

## Versions

Use the versions in `package.json`. Most component code that models learned from is React or Preact 10, so these forms keep coming back:

- Import from `preact` and `preact/hooks`, never from `react`. Add `preact/compat` only when a React-only library needs it, because it switches on React behaviour the rest of the code does not expect.
- Preact 11 forwards refs by default: take `ref` as a normal prop, without `forwardRef`. `useRef` needs an initial value, and a DOM ref is typed `useRef<HTMLInputElement | null>(null)`.
- Core Preact 11 no longer supports `defaultProps` or adds `px` to numeric styles. Use default values in parameter destructuring, and write units: `style={{ height: '500px' }}`.
- The `JSX` namespace holds only what TypeScript needs. Attribute types come from `preact`: `import type { ButtonHTMLAttributes } from 'preact'`, not `JSX.ButtonHTMLAttributes`.
- Function components and hooks only, no class components.
- Use `class`, `for` and `onInput` as in the DOM. `onInput` fires on every keystroke; Preact's `onChange` fires only on blur, unlike React's.

## State

- Hooks for state. Do not mix in `@preact/signals` unless the playbook names it: two state models in one app double what every reader must know.
- Derive values while rendering. `useMemo` only where a measured slowdown exists.
- An effect that subscribes, such as an `EventSource` or a timer, returns a cleanup that closes it. In Preact 11 that cleanup runs after paint on unmount, as in React.

## Mapping to atomic design

- A page's view model is a hook, `use<Page>()`. It is the only code that calls the API client. Below pages, components take props and call callback props such as `onSave`.
- Type each component's props with an interface, and its children with `ComponentChildren` from `preact`.
- Load route pages lazily with `lazy(() => import(…))` behind `Suspense` from `preact/compat`, or with `preact-iso`, only when the bundle size warrants it.

## Vite

- Vite 8 with `@preact/preset-vite`. Vite 8 bundles with Rolldown and transforms with Oxc: config uses `build.rolldownOptions` and `oxc`, not the old `build.rollupOptions` and `esbuild`.
- In dev, forward API calls with `server.proxy` to the backend, so the code always calls relative `/api/…` URLs.

## Testing

- Vitest 5 with the `happy-dom` environment, `@testing-library/preact`, `@testing-library/user-event` and the jest-dom matchers.
- Act through `user-event` and assert on what is rendered, found by role and accessible name. Never reach into hook state.
- Test a view-model hook with `renderHook` and a fake API client passed in, not with a module mock of `fetch`.

## Checks

- `tsc --noEmit` and ESLint with typescript-eslint pass.
