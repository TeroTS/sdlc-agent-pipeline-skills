# Implement-Story Skill Decisions

Status: Draft v1

## Problem Summary

Provide a skill that selects and implements exactly one dependency-ready story from the authoritative YAML backlog.

## Goals and Success Criteria

- Select the correct next story from `backlog.yaml`.
- Implement and verify exactly one story per invocation.
- Keep backlog status accurate after success or failure.

## Primary Roles / Actors

- Developer invoking `$implement-story`.
- Implementer executing the selected backlog story.

## Non-Goals

- Generating or modifying backlog stories beyond status changes.
- Implementing multiple stories in one invocation.
- Automatically committing changes.
- Generating contracts.

## Locked Decisions

### Product / Scope

- Create the `$implement-story` skill.
- Implement exactly one story per invocation.

### UX / Workflow

- Resume the sole `active` story when present.
- Otherwise select the first dependency-ready `pending` story.
- A user-named ready story overrides automatic selection.
- Change status `pending` to `active` before implementation and `active` to `done` only after all checks pass.
- On failure, leave the story `active`, stop, and report failed checks.

### Data / State / Ownership

- `backlog.yaml` is the authoritative story source and status record.
- A story is dependency-ready only when every `dependsOn` story is `done`.
- The skill may change only the selected story's status.
- `$decisions-to-stories` may replace `backlog.yaml` only when every existing
  story is `done`.

### Interfaces / Contracts

- Read the selected story's `contractRefs` and enforce applicable contracts during implementation.
- Read referenced contract artifacts before coding.

### Operations / Deployment

- Run the selected story's `requiredTests` and applicable project verification before marking it done.
- Do not commit or push automatically.

## Core Business Rules

- Reject an absent, invalid, or ambiguous backlog.
- Reject a named story that is not dependency-ready.
- Reject multiple `active` stories.
- Do not continue to another story after completion or failure.
- Do not mark a story done unless its acceptance criteria, required tests, and definition of done are satisfied.

## UX / Workflow Rules

- Read the selected story completely before implementation.
- Make the minimum changes needed for the selected story.
- Report the selected story, completed checks, and remaining active status when applicable.

## Data / State / Ownership Decisions

- Valid statuses are `pending`, `active`, and `done`.
- Status transitions are `pending → active → done`; a failure remains `active`.

## Interface / Contract Expectations

- Contract references constrain implementation when present.
- An empty `contractRefs` list requires no contract artifact.

## Operational / Deployment Constraints

- No automatic commit or push.
- Stop immediately after one story outcome.

## Assumptions

- `backlog.yaml` conforms to the decisions-to-stories backlog schema.
- Project-local tests and verification commands are discoverable from the story and repository.
- A completed backlog is safe to replace before generating a new backlog.

## Open Questions

- None currently.

## Ready for Spec Generation

- Status: yes
- Reason: `$implement-story` can proceed without inventing policy.
