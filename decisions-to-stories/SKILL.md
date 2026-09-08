---
name: decisions-to-stories
description: >
  Turn a ready repo-root DECISIONS.md into a validated repo-root backlog.yaml of
  dependency-ordered, independently implementable stories. Use after decisions
  are locked and before an explicitly requested implementation.
---

# Decisions to Stories

## Overview

Use this skill to create the authoritative story backlog directly from locked
product decisions. It plans only; it never selects or implements a story.

## Output

- `backlog.yaml`

Follow [references/backlog-template.yaml](./references/backlog-template.yaml)
and validate against
[references/backlog.schema.json](./references/backlog.schema.json) before
writing.

## Preconditions

- Repo-root `DECISIONS.md` exists and is read completely.
- `DECISIONS.md` is ready for generation.
- `DECISIONS.md` has no material open questions.
- Repo-root `backlog.yaml` does not exist.

If a precondition fails, report the error and make no changes.

## Workflow

### 1. Ground in existing artifacts

- Read `DECISIONS.md` completely.
- Read `openapi.yaml` and `docs/contracts.md` when present.
- If `backlog.yaml` exists, stop with an error; do not update or replace it.

### 2. Derive stories

- Create only stories explicit in locked decisions.
- Use sequential IDs beginning at `S001` and initial status `pending`.
- Order stories by implementation dependency.
- Use `dependsOn` only for earlier story IDs. Do not create cycles.
- Keep every story independently implementable.
- Include applicable references into existing contracts. Otherwise use an empty
  `contractRefs` list.
- Derive scope, acceptance criteria, required tests, and definition of done
  from the decisions and applicable contracts. Do not invent policy.

### 3. Validate and write

- Validate YAML syntax with an available YAML parser.
- Validate the parsed document against
  `references/backlog.schema.json` using available project tooling.
- Confirm IDs are unique, dependencies exist and precede their consumers, and
  contract references point to existing artifacts when non-empty.
- Write repo-root `backlog.yaml` only after all validation succeeds.

## Rules

- Do not create `SPEC.md`, slice notes, contracts, code, or clients.
- Do not modify an existing `backlog.yaml`.
- Do not add stories from assumptions, future work, or unresolved questions.
- Require every story field defined by the schema, including empty lists where
  applicable.
- Stop after writing the backlog and name `backlog.yaml` as the implementation
  input.
