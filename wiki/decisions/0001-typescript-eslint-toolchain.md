---
type: decision-context
title: 'TypeScript + flat ESLint toolchain for the addon'
description: 'TypeScript + flat ESLint toolchain for the addon'
tags: [decisions]
status: stable
---
<!-- Retroactive record. Decision date from commit 8939ef6 (2025-05-13). -->

# 0001. TypeScript + flat ESLint toolchain for the addon

- **Status:** accepted
- **Date:** 2025-05-13
- **Issue:** — (no tracked issue; established in commit `8939ef6`)

## Context

The Construct 3 addon source needs an authoring language and a lint gate. C3
loads plain JavaScript at runtime, but hand-writing untyped JS for the editor
plugin (`plugin.ts`), ACEs, and runtime is error-prone, and there was no
consistent style enforcement.

## Decision

Author the addon in **TypeScript** under `src/`, compiled with `tsc -p src`
into `dist/`, and lint with a **flat ESLint config** (`eslint.config.mjs`,
`typescript-eslint`). The package pipeline (`npm run all:{windows|posix}`)
runs lint → build → copy assets → zip `dist/` into the `.c3addon`. This keeps
the shipped artifact (JS in `dist/`) separate from the typed source.

## Compromise

A build step is now mandatory before packaging — `src/` can't be zipped
directly. Plain-JS authoring (no build, edit-and-load) was rejected: it loses
type safety across the editor/runtime boundary and the ACE definitions, which
is where most C3 addon bugs hide.

## Consequences

- Type checking and lint catch errors before they reach the C3 editor.
- Contributors must `npm install` and `npm run build` before producing a
  `.c3addon`; the dev server still serves `src/` for fast iteration.
- The toolchain underpins the later `commands.lint` / `commands.build` /
  `commands.validate` wiring (see [0005](0005-genvid-dev-github-host.md)).
