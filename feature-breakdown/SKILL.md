---
name: feature-breakdown
description: >
  Orchestrator skill for the f-skill ecosystem. Use FIRST when given a large
  feature, epic, or ambiguous task to build. Guides the breakdown of the
  feature into manageable subtasks and maps them to specific execution skills
  before any code is written.
---

> **⚠️ COMPATIBILITY FIRST:**
> If you are working in an **existing project**, you MUST prioritize and mimic the project's existing structure, naming conventions, and patterns over the rules in this document. Only apply these `f-skill` rules strictly when starting a **new project**, creating a completely **new module**, or when the user explicitly asks you to follow `f-skill`.

# Feature Breakdown & Orchestration

This is the central orchestration skill. Never start writing code for a large feature immediately. Always break it down into a plan with discrete subtasks, mapping each subtask to the correct `f-skill` convention.

## Complexity Pushback

If a requested feature has excessive edge cases, multiple complex error states, or highly divergent paths (e.g., "Guest Checkout" vs. "Logged-In Checkout"), **do NOT plan a single massive feature.** You MUST push back. Ask the user/designer to split the feature into completely distinct sub-flows before writing the breakdown plan.

---

## The Breakdown Flow

When analyzing a new feature request, break it down in this exact dependency order:

1. **Step 0: Discovery Phase** (Find Existing Code)
   - ALWAYS search the codebase for existing modules, types, utility scripts, or UI components that can be reused *before* creating the plan.
2. **Module & File Setup** (Where does it live?)
   - Create the directory structure.
   - Reference: `module-architecture` skill.
3. **State & Data Flow** (How is data managed?)
   - Determine if state is Local, Subtree, Server Cache, or Global.
   - Reference: `state-management` skill.
4. **Logic & APIs** (Data fetching and helpers)
   - Define types, API calls, and utility scripts.
   - Reference: `script-creation` skill.
5. **Component & Composition** (Visual and structural blocks)
   - Build the component tiers (Presentational, Container, Controller, Page).
   - Reference: `component-design` skill.
6. **Testing** (Verification)
   - Write tests for the components and scripts.
   - Reference: `testing-convention` skill.

---

## Subtask Extraction Format

When presenting your breakdown plan to the user, use this exact template for each subtask:

```markdown
### [Task Type] Subtask Name
**Description:** Why this exists and what it does.
**Discovery:** What existing code/components will be reused for this task.
**Todo:** Technical steps to implement (directories, files, logic).
**Skill Reference:** Which f-skill governs this step.
**Concern:** Any open questions for the user before starting.
```

---

## Example Extraction Plan

**Task from User:** "Build a Shopping Cart feature that fetches from an API and shows a list of items."

**AI Breakdown Plan:**

### [Architecture] Create Cart Module
**Description:** Setup the flat module structure for the new Cart feature.
**Todo:**
- Create `src/cart/` module directory.
- Add flat subdirectories: `components/`, `api/`, `store/`.
**Skill Reference:** `module-architecture`
**Concern:** None.

### [Logic] Create Cart API and Types
**Description:** Define the types and API fetching logic for the cart items.
**Todo:**
- Create `src/cart/api/fetchCartItems.ts`.
- Ensure it takes a single object parameter and exports a single function.
- Include JSDoc with @example.
**Skill Reference:** `script-creation`
**Concern:** What is the exact API endpoint URL?

### [State] Setup Cart Server Cache
**Description:** Manage the async data fetching state.
**Todo:**
- Use the Server State Cache pattern (e.g., React Query or Riverpod).
- Create a hook to wrap `fetchCartItems` handling loading and error states.
**Skill Reference:** `state-management`
**Concern:** None.

### [UI] Create CartItem Component
**Description:** Reusable presentational component for a single cart item.
**Todo:**
- Create `src/cart/components/CartItem.tsx`.
- Follow the 7-tier responsibility system (pure presentational, callback-everything).
**Skill Reference:** `component-design`
**Concern:** Do we need a "Remove Item" button on this card?

### [Testing] Write Cart Component Tests
**Description:** Verify the UI component behaviors.
**Todo:**
- Create `src/cart/components/CartItem.spec.tsx`.
- Structure tests using the Case wrapper: `describe('Case: ...')` → Visuals/Lifecycle/Interactions.
**Skill Reference:** `testing-convention`
**Concern:** None.

---

## 🤖 AI Instructions

1. **Always plan first:** When given a complex task, output a Breakdown Plan using the format above.
2. **Request approval:** Stop and ask the user "Does this plan look correct?" before writing any code.
3. **Read references:** Before executing a specific subtask, read its referenced `f-skill` file to ensure strict adherence to the conventions.
4. **Resolve concerns:** If a subtask has a `Concern`, ask the user for clarification before starting that subtask.
