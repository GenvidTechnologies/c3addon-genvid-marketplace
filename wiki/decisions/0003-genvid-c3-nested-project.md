---
type: decision-context
title: 'Adopt genvid-c3 with the C3 project nested under `sample/`; fix tooling upstream'
description: 'Adopt genvid-c3 with the C3 project nested under `sample/`; fix tooling upstream'
tags: [decisions]
status: stable
---
<!-- Retroactive record. Decision date from commit 95de59e (2026-06-19). -->

# 0003. Adopt genvid-c3 with the C3 project nested under `sample/`; fix tooling upstream

- **Status:** accepted
- **Date:** 2026-06-19
- **Issue:** genvid-holdings/claude-code-plugin-genvid-c3#26 (and upstream
  genvid-holdings/c3-domain-manager#16, genvid-holdings/construct3-chef#94)

## Context

Adopting the `genvid-c3` plugin contract surfaced a clash with
[0002](0002-repository-layout.md): the plugin and its bundled MCP servers
(`construct3-chef`, `c3-domain-manager`) assumed the C3 project lived at the
**repo root** — they launched at `process.cwd()` with no `--project-dir`, and
`domain-config.json` was checked at the repo root. This repo's only C3 project
is the stub in `sample/`, so the live tools saw an empty root.

## Decision

Keep the C3 project where it belongs ([0002](0002-repository-layout.md)) and
make the tooling support nested projects, rather than relocating the project to
satisfy the tools. Concretely:

- Mark the project with `.genvid-agent.json` `paths.c3project` →
  `sample/project.c3proj` at the repo root.
- Place the C3 configs (`domain-config.json`, `construct3-chef.config.json`) at
  the **project root** (`sample/`), their natural home.
- File and drive upstream fixes so the servers **auto-discover** the project
  root (an immediate child containing `project.c3proj`, plus a `C3_PROJECT_DIR`
  env override). Shipped in `construct3-chef` 0.10.2, `c3-domain-manager` 0.5.0,
  and consumed by `genvid-c3` plugin 1.6.0.

## Compromise

The short-term workaround placed the configs at the repo root to make the audit
pass before the upstream fixes landed; that was reverted once the servers could
auto-discover `sample/`. The rejected alternative — moving the C3 project to the
repo root — would have undone [0002](0002-repository-layout.md) and buried the
addon source for the sake of tooling convenience.

## Consequences

- Plugin-launched MCP servers and the `c3-explorer` / `c3-implementer` agents
  operate on `sample/` with no manual flag.
- Configs live at the project root, so adding more C3 projects later (each its
  own root) is supported without per-tool reconfiguration.
- The repo depends on `construct3-chef` ≥ 0.10.2 and `c3-domain-manager` ≥ 0.5.0
  for the auto-discovery behavior.
