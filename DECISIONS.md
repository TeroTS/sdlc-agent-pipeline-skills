# Decisions-to-Stories Skill Decisions

Status: Draft v1

## Problem Summary

Replace the separate decision-to-spec and lean-story-delivery workflows with one skill that derives a validated YAML story backlog directly from locked decisions.

## Goals and Success Criteria

- Generate a repo-root `backlog.yaml` from `DECISIONS.md`.
- Make each story independently implementable with scope, acceptance criteria, tests, dependencies, and contract references.
- Prevent accidental replacement of an existing backlog.

## Primary Roles / Actors

- Product or technical planner invoking `$decisions-to-stories`.
- Implementer consuming `backlog.yaml` after an explicit implementation request.

## Non-Goals

- Implementing backlog stories.
- Generating or changing contracts.
- Maintaining slice notes or selecting an implementation target.
- Updating an existing backlog.

## Locked Decisions

### Product / Scope

- Replace `decision-to-spec/` and `lean-story-delivery/` with `decisions-to-stories/`.
- The new skill creates a YAML backlog directly from `DECISIONS.md`.

### UX / Workflow

- The skill only generates and validates the backlog; implementation requires an explicit user request.
- If `backlog.yaml` already exists, the skill reports an error and makes no changes.
- `DECISIONS.md` must be ready and have no material open questions.

### Data / State / Ownership

- The generated backlog is repo-root `backlog.yaml`.
- Stories use sequential `S001`-style IDs and initial `pending` status.
- Each story contains the fields shown in the approved YAML example.

### Interfaces / Contracts

- Read existing contract artifacts when present and add applicable `contractRefs`.
- Allow an empty `contractRefs` list when no contract artifact exists.
- Validate generated YAML against a bundled schema or template before writing.
- Update `$decision-to-contracts` and `$spec-to-contracts` handoffs to `$decisions-to-stories`.
- Keep `$spec-to-contracts` as a standalone legacy skill.

### Operations / Deployment

- No deployment or runtime behavior is introduced.

## Core Business Rules

- Do not invent stories or interfaces absent from locked decisions.
- Order stories by dependency and use `dependsOn` for explicit dependencies.
- Every story must include goal, scope, acceptance criteria, required tests, and definition of done.
- Required contract behavior belongs in acceptance criteria and definition of done when a contract reference applies.

## UX / Workflow Rules

- Read `DECISIONS.md` completely before generating the backlog.
- Read existing contract artifacts before deriving references.
- Fail clearly before writing for a missing or unready decisions pack, material open questions, invalid generated YAML, or existing `backlog.yaml`.

## Data / State / Ownership Decisions

- `backlog.yaml` is the authoritative generated story backlog.
- Existing backlog files are never modified by this skill.

## Interface / Contract Expectations

- HTTP and non-HTTP contract references may be included when artifacts exist.
- Empty `contractRefs` is valid when no applicable artifact exists.

## Operational / Deployment Constraints

- Generated backlog syntax and required fields must be validated before it is written.

## Assumptions

- Existing contract artifacts use their established repo paths.
- A schema or template can be bundled without adding dependencies.

## Open Questions

- None currently.

## Ready for Spec Generation

- Status: yes
- Reason: `$decisions-to-stories` can proceed without inventing policy.
