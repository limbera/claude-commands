---
description: Wrap up work — simplify, test, document, commit, and open a PR
allowed-tools: Task, Read, Write, Edit, Glob, Grep, Bash
---

Run all of the following steps sequentially. Do not skip steps. If a step fails, fix the issue before moving on.

## Step 1 — Simplify code

Launch the `code-simplifier:code-simplifier` subagent via the Task tool targeting recently modified code. Let it clean up clarity, consistency, and maintainability issues while preserving all functionality.

## Step 2 — Review instrumentation

- **Detect instrumentation tools** — Scan `package.json`, `requirements.txt`, imports, or similar dependency files for recognized tools (PostHog, Sentry, structured logging libraries like winston/pino/structlog, etc.)
- **If none are found, skip this step entirely**
- **Analyze recent changes** — Run `git diff` and flag opportunities:
  - New user-facing features or flows → analytics events (e.g. PostHog)
  - New error-prone paths, API calls, or async operations → error tracking (e.g. Sentry)
  - New server-side operations, state changes, or admin actions → structured logging
- **Present recommendations** to the user and ask which (if any) to implement
- **Implement the approved instrumentation** before moving on, so subsequent test-writing covers it

## Step 3 — Run existing tests

- Detect the project's test framework and run command (e.g. `npm test`, `pytest`, `cargo test`, etc.)
- Run the existing test suite to catch any breakage from recent changes
- If tests fail, fix the issues before proceeding

## Step 4 — Write / update tests

- Run `git diff` to identify what changed
- Categorize the changes and write the appropriate test types:
  - **Utilities / helpers** — unit tests for pure functions, transformations
  - **Hooks** — tests using `renderHook`, `act`, state/effect assertions
  - **Components** — render tests, user interaction, prop/state assertions
  - **Integration tests** — multi-piece flows, API interactions
- Follow the existing test file naming conventions, locations, and patterns already in the project
- Only write tests relevant to what actually changed — don't pad coverage for unrelated code

## Step 5 — Run full test suite

- Run all tests (existing + newly written) to confirm everything is green
- If new tests fail, iterate and fix until they pass

## Step 6 — Review the full diff

- Run `git diff` and review the entire changeset end to end
- Flag and fix any of the following:
  - Leftover `console.log` / debug statements
  - TODO / FIXME comments that should be resolved
  - Accidental file inclusions (`.env`, lock files, build artifacts)
  - Style inconsistencies with the rest of the codebase

## Step 7 — Update documentation

- Review `README.md` and `CLAUDE.md` for anything that needs updating based on the changes
- Only update documentation if genuinely relevant — do not force changes

## Step 8 — Commit

- Stage and commit in as many or few commits as makes sense for the changeset
- Use clear, conventional commit messages (e.g. `feat:`, `fix:`, `test:`, `docs:`)

## Step 9 — Push and create PR

- Push the branch to GitHub
- Create a PR using `gh pr create` with:
  - A concise title
  - A summary of changes
  - A test plan checklist
