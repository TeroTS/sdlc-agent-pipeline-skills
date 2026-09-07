---
name: write-agent-policies
description: >
  Generate ready-to-paste `AGENTS.md` policy sections for a repository by
  adapting reusable agent-governance rules to the local codebase. Use when
  Codex needs to create or revise policy blocks such as
  `Required Start-of-Work Protocol`, `Simplicity Rule`,
  `Integration Fidelity Rule`, `Backend Logging Rule`,
  `TypeScript Return Types`, or `Commenting Convention`; when migrating
  repo-specific instructions into a reusable form; or when bootstrapping an
  `AGENTS.md` for a new project.
---

# Write Agent Policies

## Overview

Generate `AGENTS.md` policy blocks that preserve stable policy intent while
adapting repo-specific nouns, paths, source-of-truth docs, and backend logging
examples to the current project.

Return Markdown sections only unless the user explicitly asks to edit files.
Prefer concrete repo evidence over guessed placeholders.

## Output Contract

- Emit ready-to-paste Markdown sections.
- Default to these sections unless the user narrows scope:
  - `Required Start-of-Work Protocol`
  - `Simplicity Rule`
  - `Integration Fidelity Rule`
  - `Backend Logging Rule`
  - `TypeScript Return Types` when the repo has authored TypeScript
  - `Naming Convention` when the repo is spec-driven, already documents
    domain-term naming, has repeated module structure that needs consistency,
    or the user asks for structure or naming guidance
  - `Commenting Convention`
- Preserve stable policy intent. Adapt only repo-specific details.
- Omit `Backend Logging Rule` when the repo has no backend or service layer, or
  when the rule would be misleading.
- Omit `TypeScript Return Types` when the repo has no authored TypeScript.
- Omit `Commenting Convention` unless the repo already has a standing comment
  convention or the user explicitly asks to add one.
- Leave no unresolved placeholders.

## Workflow

### 1. Inspect the repo before writing

- Read the current `AGENTS.md` when present so the new blocks match local tone
  and neighboring conventions.
- Search for the product or requirements source of truth. Prefer explicit files
  such as `SPEC.md`, `PRD.md`, `docs/product.md`, `docs/requirements.md`, or
  an accepted architecture or specification document.
- Identify the existing `AGENTS.md` sections that should be surfaced in a
  pre-edit checkpoint. Prefer concrete local section names such as code
  placement, tests, comments, logging, naming, or testing boundaries.
- Identify which generated sections will materially govern implementation so
  they can all be named in the generated start-of-work checkpoint.
- Identify the top-level authored code locations that matter for comments or
  examples. Prefer concrete directories over guessed names.
- Detect whether the repo has authored TypeScript and identify its top-level
  authored code locations, such as `apps/`, `packages/`, `src/`, or `lib/`.
- Identify repeated module, folder, and file naming patterns that matter for
  code placement. Prefer observed framework and repo conventions over generic
  names.
- Detect whether the repo has backend or service code.
- Detect the logging API already used in backend code. Prefer observed calls
  such as `logger.info`, `logger.warn`, and `logger.error`. If the repo uses a
  different structured logger shape, adapt to it instead of forcing a new
  library.
- Collect 2-4 representative entity ID names from the domain when a backend
  logging example is needed. Prefer existing nouns from routes, models,
  schemas, or service methods.

### 2. Choose the right level of adaptation

- Keep `Simplicity Rule` and `Integration Fidelity Rule` nearly constant across
  repos.
- Adapt `Required Start-of-Work Protocol` to the repo's actual workflow
  boundaries:
  - name the local `AGENTS.md` sections that most often govern implementation
  - include every generated implementation-governing section, such as
    `TypeScript Return Types`, `Naming Convention`, or `Commenting Convention`,
    when those sections are present
  - keep the checkpoint short and mandatory before repo-tracked edits
  - include a read-only exemption when it fits the repo's workflow
- Adapt section headings to the repo's actual policy surface:
  - do not use hybrid headings unless both topics are intentionally merged and
    both are substantively covered
  - prefer `Naming Convention` and `Commenting Convention` as separate sections
    when both exist
- Adapt `Backend Logging Rule` to the repo's actual backend conventions:
  - source-of-truth document name
  - logger method names
  - example entity IDs
  - actor fields only when actor concepts make sense in the domain
- Adapt `Naming Convention` when the repo is spec-driven, already has a
  domain-term naming rule, has repeated module structure that benefits from a
  consistent policy, or the user explicitly asks for structure or naming
  guidance.
  - ground the section in observed folders, filenames, and framework
    conventions
  - define canonical locations for recurring concerns only when the repo makes
    those concerns visible
  - avoid forcing generic names such as `service.ts`, `repository.ts`, or
    `controller.ts` unless they match existing project conventions
  - treat broad renames of existing files as a separate migration, not an
    incidental policy side effect
- Adapt `TypeScript Return Types` when the repo has authored TypeScript:
  - scope the rule to concrete authored TypeScript locations when obvious
  - preserve the distinction between exported/public boundary code and local
    implementation details
  - require explicit `Promise<...>` returns for async service, repository,
    route-support, and shared-package functions when those categories exist
  - avoid noisy annotations for React components, callbacks, small local
    helpers, and obvious one-line transformations
- Adapt `Commenting Convention` to the repo's authored code layout:
  - emit it only when repo evidence or explicit user direction establishes a
    standing comment convention
  - name concrete directories when they are obvious
  - otherwise generalize to new authored application or infrastructure source
    files

### 3. Render from the reference templates

- Use [references/policy-templates.md](./references/policy-templates.md) as the
  canonical template source.
- Replace placeholders with discovered repo facts.
- If a fact cannot be discovered confidently, generalize the wording instead of
  guessing.
- Keep headings and bullets concise so the blocks paste cleanly into
  `AGENTS.md`.

### 4. Sanity-check before returning

- Remove source-repo leakage such as `SPEC.md`, `frontend/`, `backend/`,
  `infra/`, `nestjs-pino`, or domain IDs that do not exist in the target repo.
- Ensure each generated section heading matches the content of that section.
- Ensure the start-of-work checkpoint names real local sections instead of
  generic placeholders.
- Ensure the start-of-work checkpoint includes every generated section that
  materially governs implementation.
- Ensure any generated `TypeScript Return Types` section is omitted for repos
  without authored TypeScript.
- Ensure the start-of-work checkpoint is actionable: reread, identify,
  acknowledge, and surface conflicts before editing.
- Ensure the logging example is valid for the detected language style.
- Ensure the policies do not instruct the agent to use mocks when a real local
  integration path exists.
- Ensure comment policy text is grounded in repo evidence or explicit user
  request, not inferred silently.
- Ensure comment rules do not require noise on generated files, trivial barrels,
  or framework stubs.
- Do not cite unrelated skills as justification or dependency in generated
  policy text.

## Adaptation Rules

- Prefer explicit repo evidence over inference.
- When the repo lacks a clear requirements document, say `the repo's accepted
  product specification or equivalent source-of-truth document` instead of
  inventing a filename.
- When entity IDs are unclear, use neutral examples such as `resourceId`,
  `recordId`, or `jobId`.
- When the repo already has strong implementation-governing sections in
  `AGENTS.md`, surface them in the generated start-of-work checkpoint instead
  of inventing new categories.
- When generating new sections, ensure the heading names the actual topic of
  the section. Do not create hybrid headings such as `Naming And Commenting`
  unless the repo already uses that merged structure and both topics are fully
  covered.
- When the repo has authored TypeScript, include `TypeScript Return Types`
  unless the user narrowed the requested scope away from TypeScript policy.
- When the repo has no backend, omit the logging section instead of forcing a
  generic server rule.
- When the repo is spec-driven, already documents domain-term naming, or has
  repeated module structure that needs consistency, prefer a separate
  `Naming Convention` section instead of folding naming into another section.
- When a `Naming Convention` section covers project structure, keep it
  discovery-first and repo-specific. The section should help agents place new
  code predictably without imposing a generic architecture.
- When the repo already documents a stronger comment policy, align the
  generated block to that convention instead of contradicting it.
- When the repo has no standing comment convention and the user did not ask for
  one, omit `Commenting Convention` instead of inventing policy.
- Do not cite unrelated workflow templates or external guidance in generated
  `AGENTS.md` policy sections unless the user explicitly asked for them.
- Keep the generated Markdown portable. Do not embed local absolute paths,
  personal usernames, or machine-specific commands.
