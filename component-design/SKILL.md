---
name: component-design
description: >
  Design and structure UI components following a 7-tier responsibility system
  with strict naming conventions and callback patterns. Use when creating,
  refactoring, reviewing, or splitting components in any component-based
  framework (React, Flutter, or similar). Activate when the user asks about
  component structure, naming, responsibility, splitting, props design,
  composition, or when building new UI features that require component
  architecture decisions.
---

> **⚠️ COMPATIBILITY FIRST:**
> If you are working in an **existing project**, you MUST prioritize and mimic the project's existing structure, naming conventions, and patterns over the rules in this document. Only apply these `f-skill` rules strictly when starting a **new project**, creating a completely **new module**, or when the user explicitly asks you to follow `f-skill`.

# Component Design Principles

Framework-agnostic principles for designing components in any component-based UI framework (React, Flutter, Vue, etc.). These principles define **what** a component should be, **when** to split it, **how** to name it, and **how** to design its interface.

## Core Philosophy

> A component is a unit of **single responsibility**. It exists for exactly one reason. If you can describe what a component does and the description contains "and", it should be two components.

## Design System Strictness

- **NEVER** build a custom UI element if a Design System component already exists.
- If a Figma/UI design requires a component or variant that does not exist in the Design System, you **MUST** pause and ask the user/designer: *"This is not in the Design System. Can we use the standard component, or can you add this to the Design System?"*
- **NEVER** use inline styles or ad-hoc CSS for colors, spacing, or typography. Always use the project's design tokens.

---

## The 7-Tier Component System

Every component falls into exactly one of these tiers. A component that belongs to more than one tier must be split.

### Tier 1 — Pure UI

Stateless visual elements. No logic, no data awareness. Receives only styling/layout props.

- **Renders UI**: Yes
- **Has logic**: No
- **Fetches data**: No
- **Naming**: `<Domain><UIType>`
- **Examples**: `ProductImage`, `OrderBadge`, `UserAvatar`

### Tier 2 — Pure UI + Data

Receives domain data as a single object prop and displays it. No interaction logic, no side effects.

- **Renders UI**: Yes
- **Has logic**: No
- **Fetches data**: No (receives via props)
- **Naming**: `<Domain><Responsibility><UIType>`
- **Examples**: `ProductDetailCard`, `OrderSummaryBanner`, `UserProfileHeader`

### Tier 3 — Pure UI + Logic

Has interaction logic (toggles, expand/collapse, selections, animations) but does not fetch data.

- **Renders UI**: Yes
- **Has logic**: Yes
- **Fetches data**: No
- **Naming**: `<Domain><Responsibility><UIType>`
- **Examples**: `ProductQuantitySelector`, `OrderFilterDropdown`, `UserSettingsToggle`

### Tier 4 — Data Source + UI

Manages its own data (fetching, caching, mutations) and renders the result. This is the highest-responsibility UI component.

- **Renders UI**: Yes
- **Has logic**: Yes
- **Fetches data**: Yes
- **Naming**: `<Domain><Responsibility><UIType>`
- **Examples**: `ProductDetailSection`, `OrderHistoryPanel`, `UserFeedContainer`

### Tier 5 — List Composition

Composes multiple items into a list or collection layout. Handles list-level concerns (empty states, separators, scroll behavior) but delegates item rendering to Tier 1–3 components.

- **Renders UI**: Yes
- **Has logic**: No (list-level only)
- **Fetches data**: No
- **Naming**: `<Domain><UIType>List` or with `Item` suffix for children
- **Examples**: `ProductCardList`, `OrderHistoryList`, `UserAvatarList`
- **Children**: `ProductCardListItem`, `OrderHistoryListItem`

### Tier 6 — Auto Behavior (Headless)

Performs automatic side effects without rendering UI. Triggered by lifecycle events (mount, focus, timer). No visible output.

- **Renders UI**: No
- **Has logic**: Yes
- **Fetches data**: Maybe
- **Naming**: `Auto<WhatItDoes>`
- **Examples**: `AutoFetchProducts`, `AutoRefreshToken`, `AutoSyncCart`, `AutoTrackScreenView`

### Tier 7 — Domain Behavior (Headless)

Performs domain-specific logic or side effects without rendering UI. Unlike Auto (Tier 6), these are explicitly triggered or scoped to a domain action.

- **Renders UI**: No
- **Has logic**: Yes
- **Fetches data**: Maybe
- **Naming**: `<Domain><WhatItDoes>` (no UI-type suffix)
- **Examples**: `ProductFetch`, `OrderSubmit`, `UserLogout`, `CartSync`

---

## Naming Convention

All components follow this pattern:

```
<Domain><Responsibility><UIType><List|Item?>
```

| Segment          | Required | Description                                       | Examples                          |
|------------------|:--------:|---------------------------------------------------|-----------------------------------|
| `Domain`         | Yes      | The business domain this component belongs to     | `Product`, `Order`, `User`, `Cart`|
| `Responsibility` | Sometimes| What the component does (omit for Tier 1 pure UI) | `Detail`, `Summary`, `Quantity`   |
| `UIType`         | For UI   | The visual type of the component                  | `Card`, `Button`, `Section`, `Banner`, `Header`, `Panel`, `Dropdown` |
| `List` / `Item`  | Sometimes| Suffix for list compositions and their children   | `List`, `ListItem`, `Item`        |

**Headless components** (Tier 6 & 7) drop the `UIType` segment:
- Tier 6: `Auto<WhatItDoes>` — e.g., `AutoFetchProducts`
- Tier 7: `<Domain><WhatItDoes>` — e.g., `ProductFetch`

---

## Callback-Everything Rule

**Every component that performs an action must expose a callback for that action — even internal ones.**

This principle ensures that any component can be wired to analytics, logging, feedback, or parent coordination at integration time without refactoring.

### How to apply

- Name callbacks with the `on` prefix: `onPressed`, `onToggled`, `onExpanded`, `onFetched`, `onSubmitted`
- Include relevant context in callback parameters (e.g., `onItemSelected(item)` not just `onItemSelected()`)
- Callbacks are always **optional** — the component must work without them
- Even Tier 6/7 headless components expose callbacks for their lifecycle events: `onFetchStarted`, `onFetchCompleted`, `onError`

### Why

- Enables analytics integration without modifying component internals
- Makes components testable — assert behavior through callback invocations
- Supports parent-driven coordination patterns
- Prevents "add a callback later" refactoring churn

---

## Responsibility-Driven Splitting

Do not use line counts or arbitrary size limits to decide when to split. Instead, apply this rule:

> **Split when a component holds responsibilities from more than one tier.**

### Splitting heuristics

| Signal | Action |
|--------|--------|
| Component fetches data AND renders complex UI | Split into Tier 4 (data) + Tier 2 (UI) |
| Component renders a list AND also handles item interactions | Split into Tier 5 (list) + Tier 3 (items) |
| Component has automatic side effects AND renders UI | Extract Tier 6 (auto behavior) from the UI component |
| Component has both domain logic AND presentation | Extract Tier 7 (domain behavior) from the UI component |
| List items are heterogeneous (different shapes) | Each item type becomes its own Tier 1–3 component |
| A callback's handler grows complex | Extract the logic into a Tier 7 domain behavior component or a hook/BLoC |

### When NOT to split

- A component fits cleanly into a single tier
- Splitting would create components with no standalone meaning
- The "pieces" would always be used together and never independently

---

## Data as Domain Object Prop

When passing data to a component, pass it as a **single object prop named after the domain** — never as individual destructured properties.

### The rule

- The prop name matches the domain: `product`, `order`, `user`
- The component receives one data object, not scattered primitives
- Additional non-data props (callbacks, styling, flags) remain as separate props

### Why

- Adding a field to the data model requires zero prop changes
- The component's interface clearly separates "data" from "behavior" and "presentation"
- Easier to type-check and refactor
- Naturally groups related information

---

## Bounded Generics for Multi-Type Components

When a component must support multiple data types, use **generics with a base interface constraint** so the component can safely access shared properties.

### The rule

- Define a base interface with the shared fields the component needs to render
- Constrain the generic: `T extends BaseInterface`
- The component renders using only the fields from the base interface
- Specific types extend the base with their own domain-specific fields

### When to use generics

| Scenario | Use generic? |
|----------|:------------:|
| Component only ever renders one data type | No — use the concrete type |
| Component renders 2+ types that share a visual structure | Yes — extract shared fields into a base interface |
| Component renders completely different layouts per type | No — use separate components per type |

### When NOT to use generics

- If only one data type will ever be used — generics add unnecessary abstraction
- If the types have nothing meaningful in common — forcing a base interface creates a leaky abstraction
- If the component needs to know the exact type to render correctly — use separate components instead

---

## Gotchas

- **Don't over-tier**: Not every tiny visual element needs its own component. A standalone text label inside a card doesn't need to be a Tier 1 component unless it's reused elsewhere.
- **Tier 4 is a code smell at scale**: Too many Tier 4 (Data Source + UI) components in one screen usually means a state management layer is missing. Consider extracting data fetching into Tier 6/7 headless components.
- **Callbacks are optional, not mandatory to pass**: The component must work without any callbacks being provided. They are escape hatches for integration, not required dependencies.
- **Generic doesn't mean "accepts anything"**: Bounded generics (`T extends X`) should require the minimum fields needed. An unbounded generic (`T`) defeats the purpose.
- **Naming is not decoration**: If you can't name a component using the convention, it's a sign the component's responsibility isn't clear. Rethink the component before naming it.
