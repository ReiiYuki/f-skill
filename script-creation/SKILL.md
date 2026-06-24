---
name: script-creation
description: >
  Conventions for writing functions, scripts, utilities, and helpers. Use when
  creating new functions, refactoring complex logic, reviewing code for single
  responsibility, designing function parameters, or writing documentation.
  Applies to any language and framework (TypeScript, Dart, JavaScript, etc.).
---

> **⚠️ COMPATIBILITY FIRST:**
> If you are working in an **existing project**, you MUST prioritize and mimic the project's existing structure, naming conventions, and patterns over the rules in this document. Only apply these `f-skill` rules strictly when starting a **new project**, creating a completely **new module**, or when the user explicitly asks you to follow `f-skill`.

# Script Creation and Maintenance

Framework-agnostic conventions for writing functions and scripts. These principles define **how** to structure a function, **how** to design its interface, **how** to document it, and **when** to split it.

## Core Philosophy

> A function is a unit of single responsibility. It exists in its own file, takes a single well-typed parameter object, is fully documented, and has co-located tests. If you can't describe what a function does in one sentence without "and", it should be two functions.

## Existing Code First (DRY)

- Before writing any new utility function, formatting script, or API call, you **MUST** search the codebase to ensure it doesn't already exist.
- Reusing existing code is **always** prioritized over creating new scripts.
- If a similar script exists but doesn't quite fit the new requirement, refactor the existing script to be more generic rather than duplicating it.

---

## Principle 1 — Single Function per File

Each file should export one primary function as its default export.

### The rule

- One default exported function per file entry point.
- The file name or directory name matches the function's purpose.
- Supporting types and constants may live in the same file or in a sibling `types` file.

### Why

- Enables tree-shaking — unused functions are never bundled.
- Keeps imports clean and explicit.
- Makes each file a clear, self-contained unit of logic.

---

## Principle 2 — Single Responsibility

Each function does exactly one thing.

### The rule

- A function should have one reason to change.
- If the function description contains "and", it should be split into two functions.
- This is the component-design skill's philosophy applied at the function level.

---

## Principle 3 — Documentation Requirements

Every exported function must have documentation.

### The rule

- Use the documentation format appropriate for the language (JSDoc for TypeScript/JavaScript, dartdoc for Dart, docstrings for Python, etc.).
- Required elements in every doc block:
  - **Description** of what the function does
  - **Parameters** with types and descriptions
  - **Return value** with type and description
  - **Usage example** (recommended)

### Why

- Functions become self-documenting — no need to read the implementation to understand the interface.
- AI agents can use documentation to understand intent without parsing logic.
- Usage examples serve as lightweight specification.

---

## Principle 4 — Object Parameter Pattern

If a function accepts more than one parameter, wrap them in a single object.

### The rule

- **0–1 parameters**: Use a direct parameter.
- **2+ parameters**: Use a single named parameter object with a dedicated type/interface.
- Apply default values via destructuring defaults within the parameter.
- If all parameters are optional, default the entire object to empty.

### Why

- Parameter order becomes irrelevant — no more guessing which argument goes where.
- Adding a new parameter never changes the function signature.
- Self-documenting at the call site — named fields are clearer than positional arguments.

---

## Principle 5 — Field Ordering in Types

When defining types, interfaces, or data classes, order fields consistently.

### The rule

- **Required fields first**, then **optional fields**.
- Within each group, order logically by importance or domain relevance.

### Why

- Makes types scannable — you immediately see what's mandatory.
- Consistent ordering across the codebase reduces cognitive load.

---

## Principle 6 — Mapper Pattern over Conditionals

Replace chains of `if/else` or `switch/case` with lookup maps.

### The rule

- When a function maps an input value to an output value across multiple cases, use a dictionary/map/record instead of conditional branches.
- Provide a fallback default for unmatched keys.

### Why

- More declarative and easier to read.
- Adding a new case is a single line, not a new branch.
- Eliminates the risk of missing a branch or forgetting a `break`.
- Easier to test — the map itself is a data structure that can be validated.

---

## Principle 7 — Logic Splitting Heuristics

Split a function when it grows too complex.

### When to split

| Signal | Action |
|--------|--------|
| Function exceeds ~30–50 lines | Extract sub-functions |
| Too many conditional branches | Use mapper pattern or extract branch logic |
| Each condition branch has significant logic | Extract each branch into its own function |
| Function does multiple sequential steps | Extract each step into a named function |

### Split strategies

- **Sub-utilities**: Extract logic into helper functions in a sibling file or `utils` directory.
- **Mapper pattern**: Replace condition-based transformations with a lookup map.
- **Pipeline**: Break sequential operations into named steps that compose together.

---

## Principle 8 — Co-located Tests

Every function must have a test file alongside it.

### The rule

- Tests live next to the function they test, not in a separate `__tests__` root directory.
- File structure: the function file and its test file are siblings in the same directory.
- Test coverage must include:
  - All possible condition branches
  - Edge cases (null, undefined, empty values, boundary values)
  - Error scenarios
- Use parameterized/table-driven tests for multiple similar test cases.

### Why

- Co-location means you never lose track of which tests belong to which function.
- Deleting a function means deleting its directory — tests go with it automatically.
- Encourages writing tests immediately, not as an afterthought.

---

## Gotchas

- **Don't over-split**: A 10-line function doesn't need to be broken into 3 functions. Split for clarity, not for arbitrary size targets.
- **Don't abuse the mapper pattern**: If cases require genuinely different logic (not just different values), separate functions are better than a map of functions.
- **Documentation is not optional**: Even "obvious" functions benefit from doc blocks. The agent and future developers rely on them.
- **Object params are for functions, not components**: Component props follow the component-design skill. This skill is for utility functions, helpers, and scripts.
