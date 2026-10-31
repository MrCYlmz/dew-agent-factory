---
name: vue3
description: Vue 3 with TypeScript, Pinia, Vue Router and Vite conventions.
---

## Versions

Use the versions in `package.json` and follow the official Vue style guide. Much of the Vue code models learned from is Vue 2, so these older forms keep coming back:

- `<script setup lang="ts">` with the Composition API, never the Options API (`data()`, `methods`). Mixing both styles splits the codebase, and Vapor mode supports only the Composition API.
- Pinia for stores, never Vuex.
- None of the APIs Vue 3 removed: filters, `$on`/`$off` event buses, `this.$set`, the `.sync` modifier.
- `defineModel` for two-way binding instead of a prop plus an update event, and `useTemplateRef` for template refs.

## Reactivity

- Prefer `ref`. Destructuring or reassigning a `reactive` object silently drops reactivity.
- Destructured props stay reactive, but pass them to `watch` as a getter: `watch(() => size, …)`.
- Derived values are `computed`; `watch` is only for side effects outside Vue.

## Mapping to atomic design

- A page's view model is a composable, `use<Page>()`. It is the only code that calls the API client or uses Pinia stores. Below pages, components use `defineProps` and `defineEmits` only.
- Never mutate a prop. Emit an event or use `defineModel`, because the parent owns the value.
- Type props and emits with TypeScript generics, so a wrong prop fails the build instead of the page.
- Never put `v-if` on the same element as `v-for`. In Vue 3, `v-if` runs first and cannot see the loop variable.
- Style components with `<style scoped>`, so their styles cannot leak into others.
- Load route pages lazily with `() => import(…)`, so users download only the pages they open.

## Testing

- Vitest with Vue Testing Library, `user-event` and the jest-dom matchers.
- Act through `user-event` and read what is rendered, never `wrapper.vm` or a component's internal state.
- Test view models with `createTestingPinia` and a mocked API client.
- Run mutation testing with Stryker and its Vitest runner.

## Checks

- `vue-tsc` and ESLint with `eslint-plugin-vue` pass.
