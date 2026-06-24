---
name: module-architecture
description: >
  Organize project structure using a flat, module-based architecture where
  business features and technical concerns are treated equally as modules.
  Use when creating new folders, deciding where to place shared code, setting
  up a new project structure, or determining module boundaries. Applies to
  any component-based framework (React, Flutter, Next.js, etc.).
---

> **⚠️ COMPATIBILITY FIRST:**
> If you are working in an **existing project**, you MUST prioritize and mimic the project's existing structure, naming conventions, and patterns over the rules in this document. Only apply these `f-skill` rules strictly when starting a **new project**, creating a completely **new module**, or when the user explicitly asks you to follow `f-skill`.

# Module-Based Architecture

Framework-agnostic principles for organizing project structure. These principles define **where** code lives, **how** modules are organized internally, and **how** modules interact with each other.

## Core Philosophy

> There is no distinction between a "feature" and a "shared utility." Everything is a module. A `cart` module and a `ui` module are peers — they live at the same level, follow the same internal structure, and have equal standing in the codebase.

---

## Principle 1 — Everything is a Module

The root source directory contains a flat list of modules. There is no special `shared/`, `common/`, or `core/` folder.

### The rule

- Business domains (`cart`, `checkout`, `auth`, `product`) are modules.
- Technical concerns (`ui`, `network`, `logger`, `storage`, `analytics`) are also modules.
- They all live side-by-side at the same directory level.

### Why

- Prevents the "shared folder dumping ground" anti-pattern where `shared/` becomes an unorganized mess.
- Every piece of code has a clear, functional home. A reusable button belongs in `ui`, not in `shared`.
- New developers can browse the module list and immediately understand the full scope of the project.

### Example structure

```
src/
├── auth/
├── cart/
├── checkout/
├── product/
├── ui/
├── network/
├── analytics/
└── logger/
```

---

## Principle 2 — Flat Internals by Type

Inside each module, organize code flatly by technical type.

### The rule

- Use folders like `components/`, `api/`, `store/`, `utils/` inside a module.
- Do not nest sub-modules inside modules.
- Do not over-engineer strict layered architecture (e.g., `data/repositories/datasources/`) unless absolutely necessary. Keep it flat and accessible.

### Example module internals

```
cart/
├── components/
├── api/
├── store/
└── utils/
```

### When to create a new internal folder

- When you have 3+ files of the same technical type within a module.
- Before that threshold, keeping files flat at the module root is acceptable.

---

## Principle 3 — Free-For-All Boundaries

Modules are allowed to import deep paths directly from other modules.

### The rule

- Direct imports across modules are permitted and expected.
- There is no requirement for barrel files (`index.ts`) or public API surfaces at the module root.
- Import exactly what you need from wherever it lives.

### Why

- Strict barrel files add unnecessary maintenance overhead in fast-moving UI projects.
- Direct imports keep development fast and tree-shaking efficient.
- Avoids circular dependency issues that often plague barrel-file architectures.

---

## Principle 4 — Origin-Based Ownership

When code in one module starts being used by another module, apply this decision:

### The refactoring rule

| Situation | Action |
|-----------|--------|
| The code is **generic** (a button, a fetch wrapper, a date formatter) | Move it to its functional module (e.g., `ui`, `network`, `utils`) |
| The code is **domain-specific** to its origin module | Leave it where it is. Let other modules import it directly. |

### Why

- Minimizes file-movement churn and noisy diffs.
- Keeps domain-specific knowledge close to its source.
- Only truly generic, multi-purpose code gets promoted to a functional module.

---

## Gotchas

- **Don't create `shared/` or `common/` folders.** If you feel the need for a "shared" folder, you actually need a new module named after its function (e.g., `ui`, `network`, `validation`).
- **Don't nest modules inside modules.** If a module grows large, split it into two sibling modules rather than creating sub-modules. For example, split `product/` into `product-catalog/` and `product-detail/` as siblings.
- **Don't over-organize small modules.** A module with 3 files doesn't need `components/`, `api/`, and `utils/` folders. Keep files flat until the module grows.
- **Module names should be nouns.** Use `auth` not `authenticate`, `cart` not `add-to-cart`. The module represents a domain, not an action.
