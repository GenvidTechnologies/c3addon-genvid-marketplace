---
type: decision-context
title: 'Repository layout: addon in `src/`, sample C3 project in `sample/`'
description: 'Repository layout: addon in `src/`, sample C3 project in `sample/`'
tags: [decisions]
status: stable
---
<!-- Retroactive record. Decision date from commit 3496de7 (2025-06-02). -->

# 0002. Repository layout: addon in `src/`, sample C3 project in `sample/`

- **Status:** accepted
- **Date:** 2025-06-02
- **Issue:** — (no tracked issue; established in commit `3496de7`)

## Context

The repository must hold two distinct things: the **shippable addon** and a
**Construct 3 project** to exercise it in the editor. They have different
shapes — the addon is TypeScript source that builds to a `.c3addon`; the C3
project is editor-managed JSON (event sheets, layouts, object types). Mixing
them at the repo root would make the build, the `.gitignore`, and tooling
ambiguous.

## Decision

Lay the repo out as:

- `src/` — the addon (`addon.json`, `aces.json`, `plugin.ts`, `c3runtime/`,
  `lang/`, `icon.svg`); builds to `dist/`, zipped as `Genvid_Marketplace.c3addon`.
- `sample/` — a **stub C3 project** (`project.c3proj` + `eventSheets/`,
  `layouts/`, `objectTypes/`, …) used to load and test the addon.
- `SDK/` — the Construct 3 addon SDK as a git submodule.

The C3 project is deliberately a **nested** subdirectory, not the repo root.

## Compromise

Nesting the C3 project under `sample/` means any tool that assumes
"repo root == C3 project root" needs to be pointed at the subdirectory — a
cost paid later (see [0003](0003-genvid-c3-nested-project.md)). The
alternative, putting the C3 project at the repo root, was rejected: it would
bury the addon source the repo actually ships and conflate two unrelated
artifact lifecycles.

## Consequences

- Clean separation: building/zipping the addon never touches `sample/`, and
  editing the sample never affects the shipped artifact.
- C3-aware tooling must target `sample/` explicitly; this directly motivated
  the project-root auto-discovery work in [0003](0003-genvid-c3-nested-project.md).
