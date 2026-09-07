---
name: decision-to-spec
description: >
  Turn a repo-root DECISIONS.md into a concise repo-root SPEC.md using the
  standard product spec template. Use after grill-me or any accepted decision
  pack is complete enough for spec generation.
---

# Decision to Spec

## Overview

Use this skill to package locked decisions into a concise `SPEC.md` that later
planning and implementation skills can consume as a story backlog.

## Preconditions

Confirm all of the following before writing `SPEC.md`:

- repo-root `DECISIONS.md` exists
- `DECISIONS.md` marks spec generation as ready, or the user explicitly directs
  you to proceed anyway
- the repo does not have a conflicting established `SPEC.md` convention that
  you cannot safely infer

Stop and ask for clarification if `DECISIONS.md` is still materially ambiguous.

## Workflow

### 1. Read the decisions pack

- Read repo-root `DECISIONS.md` completely.
- Resolve the product summary, goals, actors, boundaries, business rules, and
  remaining open questions from that file.

### 2. Validate readiness

- If `DECISIONS.md` is not ready for spec generation and the user has not
  explicitly overridden that gate, stop and report the blocking ambiguity.
- Do not silently resolve open product decisions during spec generation.

### 3. Write exactly one `SPEC.md`

- Create or replace repo-root `SPEC.md`.
- Follow the template at
  [../brainstorming/references/spec-template.md](../brainstorming/references/spec-template.md)
  exactly.
- Keep `SPEC.md` concise, user-facing, and backlog-oriented.
- Convert locked decisions into explicit `US-xx` user stories.
- Derive story order from dependency order, not arbitrary grouping.
- Carry unresolved items into `## Open Questions` instead of guessing.
- Include a short source note pointing back to `DECISIONS.md`.

### 4. Stop and hand off

- Stop after writing `SPEC.md`.
- If interfaces, APIs, event payloads, or schema contracts need to be locked,
  the next step is `$spec-to-contracts`.
- Otherwise, the next step is `$lean-story-delivery`.

## Rules

- Do not invent new business decisions.
- Do not write additional planning files.
- Do not create contracts.
- If a needed story or boundary remains ambiguous, reflect that in
  `## Open Questions` rather than silently deciding it.
