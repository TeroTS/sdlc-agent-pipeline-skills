---
name: lean-story-delivery
description: Guide incremental product delivery from SPEC.md and generated contracts, one working user story at a time without heavy upfront specs or a full future-proof data model.
---

# Lean Story Delivery

## Overview

Use this skill to keep delivery incremental without coding blindly. Plan only
one small related slice at a time, use `SPEC.md` as the backlog, treat generated
contracts as implementation constraints when they exist, and implement one story
at a time with real verification.

This skill also standardizes how slices are tracked. Do not invent a repo-local
slice-note convention while using this skill.

## Core Stance

- Keep `SPEC.md` simple and user-facing when it exists.
- Treat the explicit user stories in `SPEC.md` as the authoritative story
  backlog when they exist.
- Treat generated contract artifacts as the primary interface constraints when
  they exist.
- Treat `DECISIONS.md` as a fallback reference only when the current slice needs
  traceability to a locked decision that is not clear enough in `SPEC.md` or
  the contract artifacts.
- Look ahead only 2-3 related stories, not the whole product.
- Implement one story at a time inside that small slice.
- Model only the minimum shared authoritative state needed for the current
  slice.
- Prefer refactoring later over speculative architecture now.
- Require real integration tests for completed story behavior when runnable
  local dependencies exist.
- Require at least one explicit real end-to-end test per completed user-visible
  story when the repo supports a runnable end-to-end path for that story.
- Use one canonical slice note path and lifecycle unless the user explicitly
  asks for a different convention.
- Stop implementation when all explicit user stories in `SPEC.md` are complete
  unless the user explicitly asks for additional work.

## Use This Skill

Use it when:

- the repo is an early-stage app or internal product
- the user wants to ship working behavior quickly
- the user explicitly wants to avoid a heavy spec framework
- the next story might affect 1-2 nearby stories through shared data or rules
- the main decision is how much to design before coding

Do not use it when:

- the system is safety-critical, regulated, or contract-heavy
- a stable external API or protocol must be defined up front
- many teams depend on formal interface contracts before implementation

## Required Reads

Read these before starting or resuming a slice:

- `SPEC.md` if it exists
- `docs/slices/current-slice.md` if it exists
- `openapi.yaml` if it exists
- `docs/contracts.md` if it exists
- `DECISIONS.md` only when the slice note points to it or `SPEC.md` plus the
  contract artifacts still leave a material locked decision ambiguous

## Slice Note Contract

When this skill is used to start, continue, or finish a real implementation
slice, the canonical slice note is:

- `docs/slices/current-slice.md`

Rules:

- Create `docs/slices/` if it does not exist.
- Do not invent per-slice filenames such as `signup-slice.md` or
  `cancellation-slice.md`.
- Do not vary the location from repo to repo unless the user explicitly asks for
  a different convention.
- Legacy slice notes may be read for context, but all new updates go through
  `docs/slices/current-slice.md`.
- If the interaction is only a lightweight recommendation and no slice is being
  started yet, the note may be omitted.
- `docs/slices/current-slice.md` is the source of truth for the current slice's
  implementation state, current target story, immediate next-step context, and
  the contract artifacts constraining the current slice.
- If the note is stale relative to the code or conversation, reconcile the note
  before implementation instead of inferring state from scattered context.

The note is a single active working brief. Slice history lives in git history,
not in a growing archive of note files.

## Slice Note Template

Use this exact template when creating or replacing `docs/slices/current-slice.md`:

```md
# Current Slice

Status: planned

## Slice Goal
- One short sentence describing the user-visible goal of this slice.

## Stories In Scope
- `US-XX Story name`

## Stories Completed In This Slice
- Empty while the slice is `planned`

## Stories Remaining In This Slice
- `US-XX Story name`

## Contract Inputs
- `openapi.yaml` and/or `docs/contracts.md` entries that constrain this slice, or `- None.`

## Decision Inputs
- `DECISIONS.md` entries needed for fallback decision traceability, or `- None.`

## Shared State Required Now
- Minimum authoritative entities, flags, tokens, or invariants required for this slice.

## Operations / Endpoints / Surfaces In This Slice
- Public operations, routes, pages, jobs, or components being added or changed now.

## Tests Required
- Real checks that must pass before the current story is considered done.

## Not Now
- Adjacent stories, infrastructure, or broader modeling explicitly deferred.

## Done When
- Concrete user-visible acceptance criteria for this slice.

## Completion Summary
- Fill only when the slice is done.

## Next Slice Recommendation
- Fill only when the slice is done.
```

Template rules:

- `Stories In Scope` must list exactly one story by default.
- More than one in-scope story is an exception, allowed only when the user
  explicitly asks for it or a hard implementation dependency makes a
  single-story slice impossible.
- If more than one story is in scope, the note must state that exception
  explicitly and name the dependency forcing it.
- Every in-scope story must appear in exactly one of:
  - `Stories Completed In This Slice`
  - `Stories Remaining In This Slice`
- Do not list the same story in both sections.
- `Contract Inputs` must list the contract artifacts constraining the current
  slice, or `- None.` when no contract artifacts apply.
- `Decision Inputs` must list `DECISIONS.md` references only when fallback
  decision traceability is needed. Otherwise write `- None.`.
- The first story in `Stories Remaining In This Slice` is the current
  implementation target for `planned` or `active` slices.
- For a newly started `planned` or `active` slice, `Stories In Scope` and
  `Stories Remaining In This Slice` should each contain exactly that one current
  target story unless an explicit exception is recorded.
- A `done` slice must leave `Stories Remaining In This Slice` empty or mark it
  explicitly as `- None.`.
- `Not Now` must explicitly name nearby deferred work.
- `Completion Summary` stays empty until the slice is done.
- `Next Slice Recommendation` stays empty until the slice is done.
- `Next Slice Recommendation` may either:
  - name the next explicit story from `SPEC.md`, or
  - state explicitly that no explicit next story remains in `SPEC.md`.
- Do not invent new story IDs such as `US-10` from non-story requirements,
  goals, or future work mentioned elsewhere in `SPEC.md`.

## Slice Transition Rules

Use this state machine:

- `planned`
  - The slice has been defined and written to `docs/slices/current-slice.md`
  - Implementation has not started yet
- `active`
  - Implementation is underway for the current slice
- `done`
  - The slice acceptance checks have been met and the note has been updated with
    completion details and the recommended next slice

Transition process:

1. Start a new slice
   - Determine the next story using the precedence rules below.
   - Write `docs/slices/current-slice.md` with `Status: planned`.
   - If the previous note is `done`, replace it with a fresh note before any
     implementation work starts. Do not code against the old completed note.
   - Put exactly one story in `Stories In Scope` and that same one story in
     `Stories Remaining In This Slice` by default.
   - List the constraining contract artifacts under `Contract Inputs` when they
     exist.
   - List `DECISIONS.md` references under `Decision Inputs` only when fallback
     traceability is needed.
   - Keep adjacent stories visible only as planning context in `Not Now` and,
     when relevant, `Next Slice Recommendation`.
   - Leave `Stories Completed In This Slice` empty.
   - Once implementation begins, update the note to `Status: active`.

2. Move within the same slice
   - Keep the same file.
   - Move each finished story from `Stories Remaining In This Slice` to
     `Stories Completed In This Slice`.
   - The first remaining story becomes the current implementation target.
   - Clarify `Contract Inputs`, `Decision Inputs`, or `Not Now` only when
     needed.
   - Do not create a new slice note for every story inside the same slice.

3. Finish the slice
   - Set `Status: done`.
   - Move all in-scope stories into `Stories Completed In This Slice`.
   - Leave `Stories Remaining In This Slice` empty or mark it as `- None.`.
   - Fill `Completion Summary` with shipped behavior, not internal refactors.
   - If explicit stories still remain in `SPEC.md`, fill `Next Slice
     Recommendation` with the next explicit story and the 1-2 nearby stories
     that should stay in view.
   - If no explicit stories remain in `SPEC.md`, fill `Next Slice
     Recommendation` by stating that no explicit next story remains in
     `SPEC.md`.

4. Begin the next slice
   - Reuse `docs/slices/current-slice.md`.
   - Replace the previous slice contents with a fresh note built from the
     template above.
   - Do not carry completed-slice scope forward into the new note.
   - Do not keep multiple active slice files.

## How to Choose the Next Slice

Determine the next slice in this order:

1. If `docs/slices/current-slice.md` exists with `Status: active`, continue that
   slice unless the user explicitly redirects. Resume from the first story in
   `Stories Remaining In This Slice`.
2. Read the explicit user stories in `SPEC.md` when they exist and determine
   whether any remain unimplemented.
3. If all explicit user stories in `SPEC.md` are complete, stop implementation
   and report that no explicit next story remains unless the user explicitly
   asks for more work.
4. If `docs/slices/current-slice.md` exists with `Status: done` and has a filled
   `Next Slice Recommendation`, use that recommendation only if it names a
   story that exists explicitly in `SPEC.md`.
5. Otherwise, use `SPEC.md` story order and story groups.
6. If the user explicitly asks for a different story, follow the user.

Do not guess a new slice if an active slice note already exists.
Do not continue into an inferred new story once the explicit `SPEC.md` story
list is exhausted.

## Workflow

### 1. Ground in the current story area

- Read `SPEC.md` if it exists.
- Read `docs/slices/current-slice.md` if it exists.
- Read `openapi.yaml` if it exists.
- Read `docs/contracts.md` if it exists.
- Read `DECISIONS.md` only when the current slice or an ambiguity points back to
  a locked decision.
- Read the currently implemented story area, data model, and tests.
- Identify the next requested story and the nearest stories that obviously share
  state or invariants with it.

### 2. Decide the planning horizon

- Treat the next story as the implementation target.
- Expand the planning horizon only to the next 1-2 related stories when shared
  authoritative state, workflow transitions, or business rules make blind
  story-by-story coding risky.
- Keep those adjacent stories as planning context only unless an explicit
  single-slice exception is recorded.
- Keep the default slice small:
  - 1 story for mostly isolated UI or behavior
  - 2-3 stories for shared-state workflows

### 3. Define only the current slice

Before implementation, answer only these questions:

- What is the next story?
- Are there any explicit user stories left in `SPEC.md` to implement?
- What contract artifacts constrain this slice?
- Do any locked decisions need explicit fallback traceability to `DECISIONS.md`?
- What are the next 1-2 related stories that could force near-term rework?
- What shared state must exist now because of those stories?
- What can safely wait?
- What tests will prove the story works, including the explicit end-to-end test
  required for each completed user-visible story when applicable?

Do not define later-slice state unless the current slice genuinely depends on
it.
