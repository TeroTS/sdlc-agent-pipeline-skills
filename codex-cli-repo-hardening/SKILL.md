---
name: codex-cli-repo-hardening
description: >
  Use this skill when the user asks to make a code repository “AI friendly”
  or to improve agent reliability, determinism, and workflow ergonomics for AI-assisted development.
---

# Codex-CLI Repository Hardening

## Goal

Make the repository as Codex-CLI friendly as possible by minimizing ambiguity, maximizing determinism, and providing a single, reliable “golden path” for setup and verification.

## Inputs

- Repository workspace and its current baseline.
- Requested hardening scope, when supplied.

## Outputs

Return:

```json
{"status":"success","outputs":{"artifactPaths":["AGENTS.md","scripts/setup","scripts/verify"],"workspaceStatus":"changed"},"validation":{"checks":[{"name":"setup","passed":true},{"name":"verify","passed":true}]},"error":null}
```

List only artifacts actually created or updated. Workspace changes are side effects.

## Validation

Run applicable setup, verification, and documented single-test commands.

## Failure Conditions

Return `{"status":"failure","outputs":{},"validation":{"checks":[]},"error":{"code":"HARDENING_FAILED","message":"..."}}` without claiming success when repository tooling or required commands cannot be established or pass.

## Non-Negotiable Principles

- Prefer **one-command setup** and **one-command verification**.
- Keep workflows **non-interactive** and **deterministic** (pinned versions, lockfiles, reproducible installs).
- Provide **explicit instructions** in files Codex reads.
- Optimize for **fast feedback**: minimal, reliable test targets and clear failure output.
- Avoid agent guessing: document “where things go” and “how to run”.
- Establish repo-local code comment expectations in `AGENTS.md` only when repo
  evidence or explicit user direction indicates a standing comment convention.
- Establish repo-local naming expectations in `AGENTS.md` when the repo wants
  domain code to mirror accepted spec terminology.
- Treat real local dependency-backed testing as the default for persistence and external-service boundaries; coded mocks and fakes are fallback tools, not the primary test strategy.

## Required Outputs (Repo Artifacts)

### A) `AGENTS.md` (repo root) — REQUIRED
This is the authoritative “how to work here” doc for agents.

It MUST include:
- **Project overview** (1–3 sentences)
- **Repo layout** (backend/frontend/packages, key directories)
- **Golden path commands** (copy/pastable):
  - install dependencies
  - build
  - test
  - lint
  - format / format-check
  - typecheck (if applicable)
- **Verification**: one “verify” command that runs the same checks as CI
- **Rules**:
  - where new code belongs
  - how to add endpoints, tests, migrations
  - generated code policy (if any)
  - how to run a single test / subset
- **Commenting convention** when the repo expects commented code or the user
  explicitly asks to add that policy:
  - header comments for new or modified non-exempt authored files
  - explanatory comments around non-obvious logic
  - explicit exemptions for generated files, trivial barrels, or machine-owned stubs
- **Naming convention** when the repo is spec-driven:
  - domain-facing code should use accepted terms from specs and canonical data models
  - alternate business synonyms should not be invented at module boundaries
  - technical helper names remain acceptable for infrastructure and private implementation details
- **Common pitfalls** (env vars, required services, ports)
- **Testing boundary rule** when the repo owns persistence or external-service integrations:
  - pure deterministic domain logic may stay unit-only
  - persistence-boundary and external-service-boundary tests should run against real local runtime dependencies, preferably local or containerized services
  - coded doubles are allowed only when no runnable local dependency exists, and that exception must be documented

### B) `scripts/setup` and `scripts/verify` — STRONGLY RECOMMENDED
Provide stable entrypoints so Codex can run predictable commands.

- `scripts/setup` MUST:
  - be non-interactive
  - install deps deterministically
  - fail fast with clear errors
  - document or invoke the local dependency bootstrap needed for real boundary testing when the repo owns such dependencies

- `scripts/verify` MUST:
  - run checks in a stable order (format-check → lint → typecheck → tests → build)
  - exit non-zero on any failure
  - print enough context to diagnose issues
  - include the repo's real local dependency-backed boundary tests when the repo owns persistence or external-service integrations, or document a separate canonical command when those cannot run inside the default verify path

### C) `.codex/` repo config (optional, if used in your environment)
If your org uses repo-local Codex rules, create:
- `.codex/instructions.md` (repo-specific constraints / gotchas / local workflows)
- `.codex/README.md` (optional, explain what is repo-local vs global)

> Note: Global constraints belong in `~/.codex/instructions.md` (user-wide), not in the repo.

## Determinism & Tooling Requirements

### 1) Pin runtime versions
At least one MUST exist (choose what fits your stack):
- Node: `.nvmrc` OR `.tool-versions` OR `mise.toml`
- Java: `.tool-versions` or Maven/Gradle toolchain config
- Python: `.python-version` or `pyproject.toml` + lock tooling

### 2) Deterministic dependency install
- Node:
  - Prefer `npm ci` when `package-lock.json` exists
  - Or `pnpm install --frozen-lockfile`
  - Or `yarn install --immutable`
- Python:
  - Prefer locked deps (uv/poetry/pip-tools) and a single install command
- Java:
  - Prefer wrapper usage (`mvnw`, `gradlew`) and pinned plugin versions

### 3) Remove interactive prompts
Setup and verify MUST run without:
- prompts
- TTY-required flows
- browser-based auth steps
- manual approvals

If a step is inherently interactive (rare), document it explicitly in `AGENTS.md` under “Manual steps”.

## “Golden Path” Command Contract

The repo MUST define and document these commands:

- **Setup**: `./scripts/setup`
- **Verify**: `./scripts/verify`
- **Single test**: documented examples for:
  - one file
  - one package/module
  - one test case (if supported)

If a mono-repo:
- `./scripts/verify` MUST support running at root
- Optionally support `./scripts/verify <package>` or `--scope <pkg>`

## CI Alignment

- CI MUST call the same commands as local verify (or be explicitly documented if different).
- If CI uses different environment variables or services, document:
  - required env vars
  - required containers/services
  - how to start dependencies locally

## Test Hygiene Requirements

- Pure deterministic domain logic MAY use unit-only tests without external dependencies.
- Persistence-boundary and external-service-boundary behavior MUST be tested locally against real runtime dependencies when those dependencies can be run locally.
- Prefer local or containerized services over coded mocks or fakes for those boundary tests.
- Coded doubles are allowed only when no runnable local dependency exists, and the exception MUST be documented in `AGENTS.md`.
- Integration or boundary tests MUST be clearly labeled and separable (for example: `test:unit` vs `test:integration`) when the repo needs both fast and dependency-backed paths.
- Provide minimal smoke tests to validate the build pipeline quickly, but do not treat smoke tests as a substitute for real dependency-backed boundary coverage.

## Repository Layout Documentation Rules

In `AGENTS.md`, document:
- Where feature code goes
- Where tests go
- Where shared utilities go
- Where config lives
- Where generated output lives (and how to regenerate)

If using OpenAPI / codegen:
- Identify the contract file location
- Identify generated output directories
- Provide regenerate command(s)
- State that generated code MUST NOT be edited by hand

If the repo wants commented code as a standing standard, `AGENTS.md` should also document:
- when file header comments are required
- what kinds of logic deserve explanatory comments
- what files are exempt because comments would be boilerplate

When both naming and commenting rules exist, prefer separate `Naming Convention`
and `Commenting Convention` sections unless the repo already uses an intentional
combined section that fully covers both topics.

If the repo wants spec-term naming as a standing standard, `AGENTS.md` should also document:
- which code surfaces must follow accepted spec terminology
- where technical/helper naming is still acceptable
- that awkward domain terms should be fixed in the spec rather than forked in code

## Command Runtime & Timeout Behavior (Agent Guidance)

Because Codex-CLI may not expose a configurable execution timeout, the agent MUST:
- Treat install/build/test commands as potentially long-running on first run
- NOT infer “timeout” solely from elapsed time
- NOT automatically retry with “extended timeout”
- If output is stalled/hanging, report status and ask how to proceed

## Execution Safety (No Guessing)

- If any required command, directory, or tooling is unclear:
  - STOP and ask a clarifying question
  - DO NOT invent commands
- Prefer adding explicit scripts/config over relying on tribal knowledge.

## Operating Procedure (How to Apply This Skill)

### Step 1 — Inventory
Collect:
- languages/stacks (Node/TS, Java, Python, etc.)
- repo structure (mono-repo vs single service)
- current commands (package.json scripts, Makefile, Gradle tasks, etc.)
- CI workflow commands

### Step 2 — Define Golden Path
Decide:
- canonical setup command
- canonical verify command
- single-test commands
- required environment variables and services

### Step 3 — Implement Artifacts
Create/update:
- `AGENTS.md`
- `scripts/setup`
- `scripts/verify`
- runtime pinning files (.nvmrc/.tool-versions/etc.)
- CI alignment or documentation

### Step 4 — Validate
Run:
- setup
- verify
- at least one single-test example

### Step 5 — Document Gotchas
Add concise “Gotchas” to `AGENTS.md`.

## Deliverable Template

When using this skill, output MUST include:
1) A numbered hardening task list (small, verifiable tasks)
2) Proposed content for `AGENTS.md`
3) Proposed `scripts/setup` and `scripts/verify` (or exact edits to existing equivalents)
4) Any required tool pinning changes
5) Verification commands and expected outcomes

## Minimal `AGENTS.md` Skeleton (copy/paste)

```md
# Agent Guide

## Project
<1–3 sentence overview>

## Repo layout
- <dir>: <what>
- <dir>: <what>

## Golden path
```bash
./scripts/setup
./scripts/verify
```

## Common commands

### install
<command>

### build
<command>

### test
<command>

### lint
<command>

### format-check
<command>

### typecheck
<command>

## Run a single test

<example: one test file>

<example: one package>

## Rules
- Where to add code: …
- Where to add tests: …
- Generated code policy: …
- API contracts (if applicable): …

Include `Commenting convention` only when repo evidence or explicit user
direction establishes a standing comment policy.

## Commenting convention
- Add a short file header comment to new or modified authored code files unless exempt
- Comment non-obvious logic, invariants, or boundary-sensitive actions
- Do not narrate obvious code
- Exempt generated files, trivial barrel exports, and machine-owned stubs

Include `Naming convention` only when the repo is spec-driven or already has a
domain-term naming rule. Keep naming and commenting as separate sections unless
the repo already uses an intentional combined section that fully covers both.

## Naming convention
- Use accepted spec and data-model terms for exported domain-facing code
- Do not invent alternate business synonyms at the module boundary
- Allow technical names for adapters, framework glue, and private helpers
- Fix confusing domain vocabulary in the spec instead of drifting in code

## Gotchas
- Required env vars: …
- Required services: …
- Ports: …

## Maintenance Mode (Repo Updates)

This skill MAY also be used when the repository has changed and existing
documentation or scripts may be out of date.

When used in maintenance mode, the agent MUST:
- Review recent repo changes (commands, structure, CI, tooling)
- Identify which documented instructions are now incorrect or incomplete
- Update only the affected artifacts (e.g. `AGENTS.md`, `scripts/setup`, `scripts/verify`)
- Avoid reworking unaffected sections
- Verify that section headings still match their contents and that any
  start-of-work checklist still references all implementation-governing policy
  sections that remain in the document
- Clearly state what changed and why

Maintenance mode MUST NOT:
- Redesign workflows without user approval
- Introduce new tooling unless required by the repo changes
- Modify unrelated sections “for cleanup”
