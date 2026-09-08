---
name: spec-to-contracts
description: >
  Turn a repo-root SPEC.md into explicit contract artifacts, defaulting to
  openapi.yaml for HTTP interfaces and docs/contracts.md for non-HTTP
  contracts. Use after SPEC.md is accepted and before slice implementation when
  interfaces need to be locked.
---

# Spec to Contracts

## Overview

Use this skill to formalize external and boundary-facing contracts before
implementation. It is OpenAPI-first for HTTP surfaces and uses `docs/contracts.md`
for non-HTTP or mixed contract sets.

## Default outputs

- `openapi.yaml` when `SPEC.md` defines HTTP-facing surfaces
- `docs/contracts.md` for non-HTTP contracts, or when no HTTP surfaces exist

Prefer an already-established repo contract path when one clearly exists.
Otherwise default to the root `openapi.yaml` and `docs/contracts.md`.

## Workflow

### 1. Read the source spec

- Read repo-root `SPEC.md` completely.
- Read any existing contract artifacts before replacing them.
- If `SPEC.md` is ambiguous about a contract surface and `DECISIONS.md` exists,
  use `DECISIONS.md` as a fallback reference before asking the user.

### 2. Identify contract-bearing surfaces

- Determine which parts of `SPEC.md` imply stable HTTP interfaces.
- Determine which parts imply non-HTTP contracts such as events, file formats,
  job payloads, or cross-component schemas.
- Do not invent interfaces that are not explicit in the spec.

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
- Define shared error responses and security schemes when `SPEC.md` requires
  them.
- Do not implement code or generate clients.
- Do not create empty or fake `openapi.yaml` files for specs that have no HTTP
  surfaces.
