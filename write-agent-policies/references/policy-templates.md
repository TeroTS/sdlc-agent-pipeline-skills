# Policy Templates

Use these templates as the canonical source for generated `AGENTS.md` blocks.
Keep policy intent stable. Adapt only the placeholders and the few lines marked
as conditional.

## Placeholder Rules

- `<SOURCE_DOC>`: Use the repo's explicit product or requirements source of
  truth. Prefer a concrete filename when it exists. Otherwise use `the repo's
  accepted product specification or equivalent source-of-truth document`.
- `<CHECKPOINT_SECTIONS>`: Use 3-6 concrete `AGENTS.md` section names that
  should be acknowledged before edits, such as `Where New Code Goes`,
  `Endpoint, Migration, and Test Rules`, `Commenting Convention`,
  `Backend Logging Rule`, `Naming Convention`, or `Testing Boundary Rule`. If
  the repo does not already have clear section names, generalize to the repo's
  implementation-governing rules instead of inventing placeholders. Include
  every generated section that materially governs implementation.
- `<AUTHORED_SCOPE>`: Use concrete authored source directories when they are
  obvious, such as ``frontend/``, ``backend/``, or ``infra/``. Otherwise use
  `new authored application or infrastructure source files`.
- `<TYPESCRIPT_AUTHORED_SCOPE>`: Use concrete authored TypeScript source
  directories when they are obvious, such as ``apps/`` and ``packages/`` or
  ``src/``. Otherwise use `authored TypeScript source files`.
- `<INFO_CALL>`, `<WARN_CALL>`, `<ERROR_CALL>`: Use the structured logging API
  already present in the repo. Prefer observed method names. If the logger shape
  is unknown but object logging is clearly expected, fall back to
  `logger.info`, `logger.warn`, and `logger.error`.
- `<ENTITY_ID_EXAMPLES>`: Use 2-4 representative IDs from the target domain,
  such as `orderId`, `accountId`, `workspaceId`, or `jobId`. If the domain is
  unclear, use neutral examples such as `resourceId` and `recordId`.
- `<ACTOR_FIELDS>`: Include `actorRole` and `actorId` only when the domain has
  clear actors such as users, admins, operators, or service principals.
- `<LOG_EXAMPLE>`: Keep the example idiomatic for the detected language. Prefer
  a JavaScript or TypeScript object-logging example only when the repo actually
  uses that style. If no confident code example fits, omit the code block and
  keep the field list only.

## Template Rules

- Section headings must match the actual subject matter of the section.
- Do not emit hybrid headings unless the repo already uses them intentionally
  and the section fully covers both topics.
- Always emit `Commenting Convention`.
- When both naming and commenting policies exist, prefer separate
  `Naming Convention` and `Commenting Convention` sections.
- Emit `TypeScript Return Types` when the repo has authored TypeScript unless
  the user narrowed the requested scope away from TypeScript policy.
- Naming guidance must be discovery-first and repo-specific. Do not force a
  generic architecture or filename pattern over established framework or repo
  conventions.

## Required Start-of-Work Protocol

```md
## Required Start-of-Work Protocol

This workflow policy applies before agents edit repo-tracked files.

- Before the first repo-tracked edit in a task, reread `AGENTS.md`.
- Identify the sections that govern the task, such as
  `<CHECKPOINT_SECTIONS>`.
- Ensure the list includes every generated implementation-governing section
  that applies, including `TypeScript Return Types`, `Naming Convention`, and
  `Commenting Convention` when present.
- In the last progress update before the first edit, explicitly name the
  applicable sections and how they will affect the implementation.
- If any repo rule appears to conflict with a higher-priority instruction,
  state the conflict explicitly before editing instead of silently choosing.
- Apply this checkpoint to repo-tracked code, config, docs, and test edits.

Exemptions:

- read-only work such as repo exploration, review, or planning with no file
  edits

Example acknowledgment:

`Applicable AGENTS sections for this task: <CHECKPOINT_SECTIONS>. I will follow
them while implementing this change.`
```

## Integration Fidelity Rule

```md
## Integration Fidelity Rule

This integration policy applies to both production code and tests.

- Do not use mocks, fakes, or in-memory replacements when a real integration is
  already available in the repo or can be run locally with the existing service
  setup.
- Prefer real persistence, real service boundaries, and real local containers
  when they are available for the task.
- Use Docker, Docker Compose, or Testcontainers when they provide the real
  integration path.
- Use mocks only when no practical real integration option exists for the task.
- If a mock is used, explain why the real integration path was not viable.
```

## TypeScript Return Types

Emit this section only when the repo has authored TypeScript.

```md
## TypeScript Return Types

This policy applies to authored TypeScript in `<TYPESCRIPT_AUTHORED_SCOPE>`.

- Add explicit return types to exported functions, exported methods, and public
  module-boundary helpers.
- Add explicit `Promise<...>` return types to async service, repository,
  route-support, and shared-package functions.
- Allow inferred return types for React components, inline callbacks, small
  local helpers, and obvious one-line transformations.
- Do not add noisy annotations where TypeScript inference is clearer and the
  function is not part of a boundary.
- Treat agent-written code as incomplete if exported domain-facing functions
  rely on inferred return types.
```

## Backend Logging Rule

Omit this section when the repo does not have backend or service code.

```md
## Backend Logging Rule

When implementing backend or service code for any user story from `<SOURCE_DOC>`:

- Add structured logs for every state-changing action and external side effect.
- Use object-based logs with `<INFO_CALL>`, `<WARN_CALL>`, or `<ERROR_CALL>`.
- Do not rely on plain string-only logs.
- Include these fields whenever available:
  - `event`
  - `<ACTOR_FIELDS>`
  - relevant entity IDs such as `<ENTITY_ID_EXAMPLES>`
  - `outcome`
  - `reason` or `errorCode` for warnings and errors
```

When `<ACTOR_FIELDS>` does not apply, remove that bullet cleanly instead of
leaving an empty placeholder.

If `<LOG_EXAMPLE>` is appropriate, append an idiomatic example after the bullet
list. Example JavaScript or TypeScript shape:

```ts
this.logger.info({
  event: "resource.updated",
  actorRole: "operator",
  actorId,
  resourceId,
  outcome: "success",
});
```

Replace the event name, actor fields, and entity IDs with repo-specific terms.
Do not emit this example unchanged unless it already matches the target repo.

## Naming Convention

Emit this section only when the repo is spec-driven, already has a domain-term
naming policy, has repeated module structure that needs consistency, or the
user explicitly asks for structure or naming guidance.

```md
## Naming Convention

This naming policy applies to project structure, filenames, and exported
domain-facing code written by agents.

- Follow the repo's existing folder structure, framework conventions, and
  repeated module patterns before introducing a new location or file shape.
- Place recurring concerns such as API handlers, business logic, data access,
  shared utilities, tests, generated code, and config in their established
  canonical locations.
- Use consistent, descriptive filenames that reveal purpose without opening the
  file.
- Apply the same naming pattern across equivalent modules.
- Avoid arbitrary one-off names unless the file has a genuinely unique
  responsibility.
- Use accepted terms from the repo's product spec and canonical data model.
- Do not invent alternate business synonyms at module or API boundaries.
- Technical helper names remain acceptable for adapters, framework glue, and
  private implementation details.
- Do not impose generic names such as `service.ts`, `repository.ts`, or
  `controller.ts` unless they match the repo's established conventions.
- Treat broad renames of existing files as a separate migration, not an
  incidental cleanup during feature work.
- If domain vocabulary is confusing, fix it in the source-of-truth spec rather
  than drifting in code.
```

## Commenting Convention

```md
## Commenting Convention

This policy applies to every new production code file in `<AUTHORED_SCOPE>`.

- Add a file header comment that describes the file's functionality.
- Add one single-line comment above every function and method.
- Generated files are exempt.
- Treat agent-written production code as incomplete when a required header or
  function/method comment is missing.
```
