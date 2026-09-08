---
name: decision-to-contracts
description: >
  Turn a repo-root DECISIONS.md into explicit contract artifacts, defaulting to
  openapi.yaml for HTTP interfaces and docs/contracts.md for non-HTTP
  contracts. Use after decisions are ready for contract generation and before
  slice implementation when interfaces need to be locked.
---

# Decision to Contracts

## Overview

Use this skill to formalize external and boundary-facing contracts before
implementation. It is OpenAPI-first for HTTP surfaces and uses `docs/contracts.md`
for non-HTTP or mixed contract sets.

## Inputs

- Repo-root `DECISIONS.md`.
- Existing contract artifacts, when present.

## Outputs

Return:

```json
{"status":"success","outputs":{"artifactPaths":["openapi.yaml","docs/contracts.md"],"workspaceStatus":"changed"},"validation":{"checks":[{"name":"contract-validation","passed":true}]},"error":null}
```

List only contract artifacts actually created or updated. Workspace changes are side effects.

## Validation

Validate each generated contract with available repository tooling and confirm every artifact path in `outputs` exists.

## Failure Conditions

Return `{"status":"failure","outputs":{},"validation":{"checks":[]},"error":{"code":"CONTRACT_GENERATION_FAILED","message":"..."}}` without writing artifacts for missing, ambiguous, or materially unresolved decisions, or failed validation.

## Default outputs

- `openapi.yaml` when `DECISIONS.md` defines HTTP-facing surfaces
- `docs/contracts.md` for non-HTTP contracts, or when no HTTP surfaces exist

Prefer an already-established repo contract path when one clearly exists.
Otherwise default to the root `openapi.yaml` and `docs/contracts.md`.

## Workflow

### 1. Read the source decisions

- Read repo-root `DECISIONS.md` completely.
- Read any existing contract artifacts before replacing them.

### 2. Identify contract-bearing surfaces

- Determine which parts of `DECISIONS.md` imply stable HTTP interfaces.
- Determine which parts imply non-HTTP contracts such as events, file formats,
  job payloads, or cross-component schemas.
- Do not invent interfaces that are not explicit in the decisions.

### 3. Generate or update the contract artifacts

- If HTTP surfaces exist, create or update `openapi.yaml`.
- If non-HTTP contracts exist, create or update `docs/contracts.md` using
  [references/contracts-doc-template.md](./references/contracts-doc-template.md)
  as the structure guide.
- If no HTTP surfaces exist, do not create fake OpenAPI. Write only
  `docs/contracts.md`.

### 4. Handoff to story generation

- Stop after the contract artifacts are written.
- In the final handoff, name the contract files `$decisions-to-stories` must
  read.
- The next step is `$decisions-to-stories`.

## Rules

- Be OpenAPI-first, not HTTP-only.
- Define shared error responses and security schemes when `DECISIONS.md` requires
  them.
- Do not implement code or generate clients.
- Do not create empty or fake `openapi.yaml` files for decisions that have no
  HTTP surfaces.
