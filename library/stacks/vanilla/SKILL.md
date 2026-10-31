---
name: vanilla
description: Framework-free frontend conventions: TypeScript, ES modules and custom elements.
---

## Language

- Write TypeScript ES modules, one per component. No globals, no `var`, no jQuery, and no polyfills for features every target browser in `playbook.md` supports.
- Use the browser's own elements before building one: `<dialog>`, `<details>`, the `popover` attribute, built-in form validation. They come with keyboard and screen reader support.

## Mapping to atomic design

- Each atom, molecule, organism and template is a custom element, defined once with `customElements.define` in its own module and named with the project prefix from `playbook.md`.
- Data goes in through properties and comes out as events: `dispatchEvent(new CustomEvent('name', { detail, bubbles: true, composed: true }))`. Attributes carry only strings, so objects and arrays always go through properties.
- Add each element to `HTMLElementTagNameMap` and type its events, so `querySelector` and listeners are typed without casts.
- A page's view model is a plain class or module that holds the page state, calls the API client and notifies the page through an `EventTarget`.

## Custom elements

- Set up in `connectedCallback` and clean up in `disconnectedCallback`, because an element is disconnected and reconnected whenever it moves. One `AbortController` per connection, passed as `signal` to every listener, makes the cleanup one call.
- Keep `connectedCallback` synchronous. Awaited work can finish after the element has left the page and leave listeners behind.
- Give every observed attribute a matching property, and guard attribute-property reflection so it cannot loop.
- Pick up properties that were set before the element was defined, so values from early scripts are not lost.
- Set a `display` value on `:host` and keep `[hidden]` working; custom elements are inline by default.
- Form-control atoms use `ElementInternals` with `formAssociated`. Inputs inside a shadow root do not join the outer form, and `id` references such as `aria-labelledby` do not cross shadow roots.

## Rendering

- Build DOM by cloning a `<template>` and set text with `textContent`. Filling `innerHTML` from template strings is how data turns into script injection.
- Update only the nodes that changed. Replacing a whole subtree loses focus, scroll position and listeners.

## Testing

- Vitest in a DOM environment with DOM Testing Library, `user-event` and the jest-dom matchers.
- Wait for `customElements.whenDefined` and the element's update before asserting.
- Check that removing an element releases its listeners and timers.
- Run mutation testing with Stryker and its Vitest runner.

## Checks

- `tsc --noEmit` and ESLint pass.
