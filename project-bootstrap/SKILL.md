---
name: project-bootstrap
description: >
  Mandatory day-zero setup requirements for any new project. Covers linting,
  formatting, security scanning, test infrastructure, config management, and
  version control. Use when initializing a new codebase before writing any features.
---

> **⚠️ COMPATIBILITY FIRST:**
> If you are working in an **existing project**, you MUST prioritize and mimic the project's existing structure, naming conventions, and patterns over the rules in this document. Only apply these `f-skill` rules strictly when starting a **new project**, creating a completely **new module**, or when the user explicitly asks you to follow `f-skill`.

# Project Bootstrap Requirements

When creating a new project or repository, the AI Agent must never start writing feature code or UI components until these baseline requirements are met. The foundation must be secure, strictly typed, and formatted.

## 1. Linting & Formatting Foundation

- **Requirement:** A code linter and formatter MUST be configured immediately.
- **Enforcement:** A pre-commit hook (e.g., Husky + lint-staged, or Lefthook) MUST be set up to prevent unformatted or unlinted code from being committed.
- *Framework-Agnostic note:* This applies whether the project uses Biome, ESLint/Prettier (JS/TS), or the Dart Analyzer (Flutter).

## 2. Security Guardrails

The following security measures must be put in place:
- **Secret Scanning:** A tool like Gitleaks must be installed (via pre-commit or CI pipeline) to prevent accidental committing of API keys, tokens, or secrets.
- **Dependency Scanning:** CI pipelines must include a step to audit for vulnerable dependencies.
- **Environment Separation:** Strict separation between DEV, STG, and PROD environments must be enforced. No hardcoded production URLs in the codebase.
- **Client Pentest Rules:** Establish and document rules that UI state must follow (e.g., no sensitive user data or authentication tokens stored in plain `localStorage`).

## 3. Testing Infrastructure

- **Requirement:** A Unit Testing framework (e.g., Vitest, Jest, Flutter Test) MUST be configured from day one.
- *Note:* E2E testing frameworks (like Playwright/Cypress) and Visual Regression tools are highly recommended, but can be deferred until the feature set matures to speed up initial bootstrap.

## 4. Config Handling & Environment Variables

- **Centralized Typed Config:** Environment variables (e.g., `process.env`, `.env` files) MUST NOT be accessed directly inside feature components or scripts.
- All environment variables must be mapped to a strongly typed configuration object at the root of the project (e.g., `src/config/env.ts` or `lib/core/config/env.dart`).
- **Fail Fast:** If a required environment variable is missing, the application must throw a loud error during the startup/bootstrap phase.

## 5. Version Control & Changelogs

- **Requirement:** An automated versioning and changelog generation tool MUST be configured.
- **Single-Repo Architecture:** Must use a tool that relies on **Conventional Commits** (e.g., `feat:`, `fix:`) to automatically generate `CHANGELOG.md` and bump versions (e.g., `standard-version`, `semantic-release`, `release-please`).
- **Mono-Repo Architecture:** Must use a package-aware versioning tool (like `changesets` or `rush`) to manage independent package versions and their respective changelogs.

---

## 🤖 AI Instructions

1. When asked to "start a new project", "initialize a repo", or similar, **stop** and ensure all 5 pillars above are set up first.
2. Provide the user with a checklist of the tools you will install to satisfy these 5 pillars before writing code.
