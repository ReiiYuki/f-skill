---
name: f-skill-workflow
description: The Meta-Skill and Universal Entrypoint for the f-skill ecosystem. Use this when asked to build or refactor a feature using f-skill.
---

> ⚠️ **COMPATIBILITY FIRST:** Before executing this skill, check if you are working in an existing codebase. If the project already has established conventions for state management, component design, or folder structure, you **MUST** mimic their conventions first, even if they contradict `f-skill`. 
> 
> Only enforce `f-skill` rules in completely new projects, new isolated modules, or if the user explicitly tells you to ignore legacy patterns.

# The `f-skill` Meta Workflow

You have been asked to execute the `f-skill` workflow to build or refactor a feature. 

**DO NOT WRITE ANY CODE IMMEDIATELY.**

Your only job in this step is to invoke the `feature-breakdown` orchestrator skill. 

## Instructions

1. **Acknowledge the Request:** Briefly acknowledge the user's request.
2. **Delegate to Orchestrator:** Pass the user's entire requirement into the `feature-breakdown` skill. 
3. **Wait:** Do not proceed to generate files until you have completed the Discovery Phase and breakdown plan outlined in `feature-breakdown`, and the user has approved the plan.

This meta-skill acts as a router. The true work is orchestrated by `feature-breakdown`, which will then cascade down into `module-architecture`, `state-management`, `script-creation`, `component-design`, and `testing-convention`.
