---
name: state-management
description: >
  Decision matrix for choosing the correct state management pattern (local,
  subtree, server cache, global). Use when deciding where state should live,
  avoiding prop drilling, or managing API data. Framework-agnostic.
---

# State Management Decision Matrix

A framework-agnostic mental model for choosing the right state management approach. State should live as close to where it is used as possible, and different types of state require different architectural solutions.

## The 4-Tier Decision Matrix

```
What kind of state do you need?
├── Component-local state (shallow)
│   └── Tier 1: Local Component State
├── Shared across specific deep component tree
│   └── Tier 2: Subtree / Dependency State
├── Async / Promise / API data
│   └── Tier 3: Server State / Async Cache
└── Shared across unrelated pages / whole app
    └── Tier 4: Global / App-Wide State
```

---

## Tier 1: Local Component State

State that is confined to a single UI component or passed down at most 1–2 levels.

**When to use:**
- UI visibility (is dropdown open, is modal visible)
- Form inputs and local validation
- Toggles, hover states, active tabs

**When NOT to use:**
- State needs to be accessed by deeply nested components
- State needs to persist across page navigation
- State comes from async operations (missing caching/loading states)

*Example tools: `useState` (React), `setState` (Flutter), `ref` (Vue).*

---

## Tier 2: Subtree / Dependency State

State shared across a specific branch of the component tree, used to avoid prop drilling.

**When to use:**
- Feature-level configuration
- Theming for a specific section
- Avoiding passing props through 3+ levels of intermediate components that don't use the data

**When NOT to use:**
- State needs to persist across hard navigations/different pages
- State changes frequently and triggers heavy re-renders
- State is async/API data

*Example tools: `Context API` (React), `InheritedWidget` (Flutter), `provide/inject` (Vue).*

---

## Tier 3: Server State / Async Cache

State derived from external systems, APIs, native bridges, or any async promise.

**When to use:**
- Data fetched from a backend API
- Results from native mobile bridges (e.g., Golden Bridge)
- Any operation that requires caching, refetching, and `isLoading`/`isError` states

**When NOT to use:**
- Synchronous client-only state (UI state)
- State that doesn't involve external async operations

*Example tools: `React Query` / `SWR` (React), `Riverpod` / `Bloc` async patterns (Flutter).*

---

## Tier 4: Global / App-Wide State

State shared across completely unrelated pages or the entire application lifecycle.

**When to use:**
- User preferences (dark mode, language)
- Auth session state
- Shopping cart data that must persist across the app
- State accessed by completely unrelated components outside the same tree

**When NOT to use:**
- State only needed within a single component (use Local State)
- State only needed within a single page (use Subtree State)
- Async/API data (use Server State)

*Example tools: `Zustand` / `Jotai` / `Redux` (React), `Riverpod` / `Provider` (Flutter), `Pinia` (Vue).*

---

## Quick Decision Guide

Ask these questions in order:

| Question | Yes → Use | No → Next Question |
|----------|-----------|-------------------|
| Is it async/promise-based? | **Server State Cache** | ↓ |
| Needed across unrelated pages? | **Global State** | ↓ |
| Needed by deep children? | **Subtree State** | ↓ |
| Otherwise | **Local State** | - |

## Anti-Patterns

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| Local State for async data | No caching, manual loading states | Use Server State Cache |
| Subtree State for cross-page | Lost on navigation | Use Global State |
| Global State for local UI | Overkill, unnecessary complexity | Use Local State |
| Prop drilling 3+ levels | Hard to maintain and refactor | Use Subtree State |
