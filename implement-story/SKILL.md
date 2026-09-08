---
name: implement-story
description: >
  Select and implement exactly one dependency-ready story from repo-root
  backlog.yaml. Use for an explicit implementation request; it updates the
  selected story from pending to active to done only after required checks pass.
---

# Implement Story

## Overview

Use this skill to implement one backlog story. It never creates stories,
implements a second story, commits, or pushes.

## Required reads

- Repo-root `backlog.yaml`
- The selected story's `contractRefs` and referenced artifacts
- Source, tests, and project verification instructions for the story area

## Select one story

1. Validate `backlog.yaml`: IDs are unique, statuses are `pending`, `active`, or
   `done`, and dependencies name existing stories.
2. If more than one story is `active`, stop with an error.
3. If one story is `active`, resume it.
4. Otherwise, if the user named a story, require it to be `pending` and
   dependency-ready; otherwise stop with an error.
5. Otherwise, select the first `pending` story whose every dependency is `done`.
6. If no story is selected, report that no dependency-ready story remains.

A story is dependency-ready only when every ID in `dependsOn` is `done`.

## Implement

1. Change the selected story to `active` before editing code.
2. Read its goal, scope, acceptance criteria, required tests, definition of
   done, and contract references completely.
3. Implement only included scope. Do not implement excluded or adjacent stories.
4. Use the smallest tests that prove the acceptance criteria, including the
   story's required tests and applicable repository verification.
5. If a check fails, leave the story `active`, stop, and report the failed
   checks.
6. Only after every required check passes, change the selected story to `done`.

## Rules

- Make no automatic commit or push.
- Do not change story fields other than the selected story's `status`.
- Do not proceed to another story after success or failure.
- Do not mark a story `done` unless its acceptance criteria, required tests,
  definition of done, and applicable contract requirements are satisfied.
- Preserve valid YAML when updating `backlog.yaml`.
- Final output names the implemented story and checks run, or the blocking error.
