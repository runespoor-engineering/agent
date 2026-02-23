---
name: react-testing-library
description: Write and structure React component/integration tests with React Testing Library. Use when writing or reviewing React tests, RTL, component tests, integration tests, or when the user mentions Testing Library, user-event, or MSW for API mocking.
---

# React Testing Library

Follow these rules when writing or updating React component/integration tests.

## Philosophy

- **Write tests. Not too many. Mostly integration.** Prefer integration over unit; avoid testing implementation details. [Ref](https://kentcdodds.com/blog/write-tests)
- **AHA (Avoid Hasty Abstraction).** Duplicate in tests until a real need to abstract appears. [Ref](https://kentcdodds.com/blog/aha-testing)
- **One case — one test.** Separate test cases; no one test doing multiple behaviors.
- **Avoid mutable variables** in tests (e.g. `let` that gets reassigned). [Ref](https://x.com/kentcdodds/status/1154468901121482753)
- **Avoid nesting** in tests. Prefer flat `describe`/`it`; no nested `describe` blocks. [Ref](https://kentcdodds.com/blog/avoid-nesting-when-youre-testing)
- **Keep tests up to date.** When adding or changing component/method/class behavior, update existing tests and add tests for new behavior.
- **Bad tests are worse than no tests.** Do not add low-value or unclear tests; skip or document gaps instead of pushing weak coverage.

## Integration / Component Tests

- Use [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/) (RTL).
- Follow [RTL query priority](https://testing-library.com/docs/queries/about#priority): prefer `getByRole`, then `getByLabelText`, then `getByPlaceholderText`, then `getByText`, then `getByDisplayValue`, then `getByAltText`, then `getByTitle`, then `getByTestId` only when necessary.
- Use correct [ARIA roles](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles) for `...ByRole()` (e.g. `button`, `textbox`, `listbox`).
- Use `@testing-library/user-event` for [user interactions](https://testing-library.com/docs/user-event/intro/) (clicks, typing, etc.), not fireEvent when user-event supports the action.
- Avoid [common RTL mistakes](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library): don’t use `container`/querySelector for DOM, don’t assert implementation details, use async utilities correctly.
- Use a **custom `render`** (and `renderHook` if needed) that wrap components with all shared providers. Add test-specific providers only in that test; if a provider becomes shared across tests, move it into the custom render.
- Do **not** test implementation details in integration tests. [Ref](https://kentcdodds.com/blog/testing-implementation-details)
- Do **not** use shallow rendering. [Ref](https://kentcdodds.com/blog/why-i-never-use-shallow-rendering)
- **Isolate each test:** each test renders the component from scratch; no relying on shared mutable state or previous test output.

## Mocking

- **Resemblance = confidence.** The more tests behave like real usage, the more they inspire confidence. Mocking trades speed for confidence; use deliberately. [Ref](https://kentcdodds.com/blog/the-merits-of-mocking)
- **API calls:** Mock REST with [MSW](https://kentcdodds.com/blog/stop-mocking-fetch). Follow [RTL + MSW example](https://testing-library.com/docs/react-testing-library/example-intro/#full-example).
- **TanStack Query:** [Turn off query retries](https://tanstack.com/query/latest/docs/framework/react/guides/testing#turn-off-retries) in test setup so failures are deterministic.
- **Timezone:** Mock system timezone in tests that depend on dates to avoid environment-dependent behavior (e.g. [Vitest fake date/timezone](https://github.com/vitest-dev/vitest/issues/1575#issuecomment-1439286286)).
- **Organization:** Keep mocks in a consistent place and style (e.g. handlers, fixtures) like the rest of the codebase.

## Test Desiderata

Tests should be: **Isolated** (order-independent), **Composable**, **Deterministic**, **Inspiring** (confidence), **Predictive** (pass ⇒ production-ready), **Automated**, **Writable**, **Readable** (clear intent), **Fast**, **Behavioral** (fail when behavior changes), **Specific** (failure points to cause), **Structure-insensitive** (not tied to internal structure).

## Style Guide

- **Location:** Test file next to the subject: same folder as the component/method/class; same base name with `.test` suffix (e.g. `Button.test.tsx`, `useEntitiesMap.test.ts`).
- **`describe`:**
  - No nested `describe`.
  - Message = name of component/method/class. Example: `describe('useEntitiesMap', () => {})`.
  - Optional second `describe` for corner cases: `describe('useEntitiesMap (corner cases)', () => {})`.
- **`it`:**
  - Message starts with `should`, present tense.
  - Use backticks for technical names (props, hooks, handlers). Example: ``it('should skip the entity if it does not have an object by the provided `entityDataPath`', () => {})``
- **Mocks:** Name with `mock` prefix (e.g. `mockOnSubmit`, `mockFetch`), not `mocking` or `mocked`.
