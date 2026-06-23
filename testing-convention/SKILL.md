---
name: testing-convention
description: >
  Framework-agnostic testing philosophy and decision matrix. Use when deciding
  which type of test to write (unit vs. integration vs. e2e), when structuring
  test files, or when writing test descriptions. Applies to any testing
  framework (Jest, Vitest, Playwright, Cypress, Flutter Test, etc.).
---

# Testing Convention

Framework-agnostic testing philosophy and decision matrix. These principles dictate **what** to test, **how** to test it, and how to **organize** test suites.

## Core Philosophy: The Comprehensive Testing Matrix

Testing is about confidence. To build confidence efficiently, use a multi-layered approach.

### 1. Universal Unit Coverage

Every single function and component must have at least a unit test.

- **Rule:** If you write a piece of logic or a UI component, it gets a unit test.
- **Why:** Most things are easy to unit test. This establishes a solid baseline of confidence that the smallest pieces work as intended in isolation.

### 2. Integration Tests

Test the boundaries where units connect, but avoid redundancy.

- **Rule:** Use integration tests when the logic connecting parts isn't too complex.
- **Gotcha:** Do not duplicate test cases that are already comprehensively covered by unit tests. Focus only on the integration points and data flow between units.

### 3. Component UI Tests (The 3-Pronged Approach)

UI components require comprehensive testing to ensure both their internal mechanics and their external presentation are correct. Component tests must include three distinct prongs:

- **User POV + Internals:** Test both how the user interacts with the component (clicking, typing, reading text) AND the internal state/methods. (e.g., Use queries by role/text for user POV, but don't shy away from checking internal state if necessary).
- **DOM Snapshot:** Required to catch structural/DOM changes. This acts as a tripwire for unintended structural regressions.
- **Visual Regression:** Required to ensure the actual rendered UI looks visually correct to the user. Snapshots catch HTML changes; visual regression catches CSS and layout changes.

### 4. End-to-End (E2E) Guarantee

E2E tests provide the ultimate confidence that the system works as a whole.

- **Rule:** Every complete user flow *must* have an E2E test.
- **Why:** While unit and integration tests prove the parts work, only E2E tests prove the user can actually accomplish their goal in a real environment.

---

## Test Structure & Documentation

### Separation of Concerns (Describe Blocks)

Component tests must be highly organized. Always separate concerns using distinct `describe` blocks.

**Template:**
```javascript
// Replace with appropriate syntax for your testing framework
describe('ComponentName', () => {

  // Group by specific scenario, variant, or state
  describe('Case: [Scenario Name]', () => {
    
    describe('Visuals', () => {
      // DOM Snapshots
      // Visual Regression Tests
    });

    describe('Lifecycle', () => {
      // Internal state behavior
      // Mount / Unmount logic
      // Internal methods and data fetching
    });

    describe('Interactions', () => {
      // User POV interactions
      // Clicks, typing, form submissions
      // Accessibility checks
    });

  });

  describe('Case: [Another Scenario]', () => {
    // ... Repeat the 3 prongs for this scenario
  });

});
```

### Tests as Documentation

A test suite is the living documentation of your codebase.

- **Rule:** Every test description (the `it` or `test` block) must be written in human descriptive language.
- **Why:** A non-developer (PM, designer, new team member) should be able to read the test file and understand exactly what the code is supposed to do.

**Bad:**
```javascript
it('handles state correctly', () => { ... })
it('calls API', () => { ... })
```

**Good:**
```javascript
it('disables the submit button when the email input is invalid', () => { ... })
it('fetches and displays the user profile after successful login', () => { ... })
```
