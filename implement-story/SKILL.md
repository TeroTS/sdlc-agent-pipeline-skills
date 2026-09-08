---
name: implement-story
description: >
  Implement exactly one selected backlog story. Use `implement <story-id>` for
  initial implementation or `validation-failure <story-id> <validation-results>`
  to correct the sole active story after deterministic validation fails.
---

# Implement Story

## Overview

Use this skill to implement one explicitly selected backlog story. It never
creates stories, selects another story, commits, pushes, reviews, or marks a
story done.

## Inputs

- `implement <story-id>`: implement a dependency-ready `pending` story.
- `validation-failure <story-id> <validation-results>`: correct the sole
  `active` story using the pipeline's structured validation result.

No review-failure input is supported.

## Required reads

- Repo-root `backlog.yaml`
- The selected story's `contractRefs` and referenced artifacts
- Source, tests, and project verification instructions for the story area
- `validation-results` for `validation-failure`

## Validate the invocation

1. Validate `backlog.yaml`: IDs are unique, statuses are `pending`, `active`, or
   `done`, and dependencies name existing stories.
2. For `implement`, require no `active` story and require `<story-id>` to name a
   dependency-ready `pending` story.
3. For `validation-failure`, require exactly one `active` story and require its
   ID to equal `<story-id>`.
4. Reject every other mode, missing input, invalid validation result, or failed
   precondition without making changes.

A story is dependency-ready only when every ID in `dependsOn` is `done`.

## Implement

1. For `implement`, change the selected story from `pending` to `active` before
   editing code.
2. For `validation-failure`, read the failed checks, correct only their cause in
   the selected story's scope, and preserve its `active` status.
3. Read the selected story's goal, scope, acceptance criteria, required tests,
   definition of done, and contract references completely.
4. Implement only included scope. Do not implement excluded or adjacent stories.
5. Run the story's required tests and applicable repository verification.
6. If a check fails, leave the story `active`, stop, and report the failed
   checks.
7. After every required check passes, leave the story `active` for pipeline
   validation, review, and commit.

## Rules

- Make no automatic commit or push.
- Do not change story fields other than the selected story's `status`.
- Do not proceed to another story after success or failure.
- Do not mark a story `done`; the pipeline does so only after validation,
  review, and commit.
- Preserve valid YAML when updating `backlog.yaml`.
- Final output names the invocation mode, story ID, and checks run, or the
  blocking error.
