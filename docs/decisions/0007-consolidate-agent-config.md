# 0007. Consolidate agent config into a single `.gvt-agent.json`

- **Status:** accepted
- **Date:** 2026-07-03
- **Issue:** [#4](https://github.com/GenvidTechnologies/c3addon-genvid-marketplace/issues/4)

## Context

[0006](0006-gvt-dev-contract-migration.md) left the repo with **two** contract
files, each owned by one plugin: `.gvt-agent.json` for the `gvt-dev` workflow
skills (`commands`, `repo`) and `.genvid-agent.json` for the C3 plugin's marker
(`features.c3`, `paths.c3project`). The split was deliberate — one owner per file,
no duplicated keys to drift.

Since then the C3 plugin was **renamed `genvid-c3` → `gvt-construct3`**, and its
`audit-c3-conventions` now reads the C3 marker from **`.gvt-agent.json`
preferentially**, treating `.genvid-agent.json` as a deprecated fallback that
emits an info-severity deprecation finding when it is the only file present. With
both plugins now keyed off the same filename, the two-file split no longer buys
separation — it just leaves a legacy file behind and a standing deprecation
warning.

Issue #4 proposed `git mv .genvid-agent.json .gvt-agent.json`, but that literal
rename would **clobber** the gvt-dev workflow contract this repo already carries
in `.gvt-agent.json`. The correct operation is a **merge**, not a rename.

## Decision

- **Consolidate** the C3 marker into `.gvt-agent.json`: populate its
  previously-empty `features`/`paths` blocks with `features.c3: true` and
  `paths.c3project: "sample/project.c3proj"`. The gvt-dev `commands`/`repo`
  blocks are unchanged.
- **Delete `.genvid-agent.json`.** A single file now serves both plugins:
  `gvt-dev` reads `commands`/`repo`; `gvt-construct3` reads `features`/`paths`.
- Update `.claude/settings.json` to enable the renamed plugin as
  `gvt-construct3@gvt-plugins` (the marketplace was also renamed
  `genvid-plugins` → `gvt-plugins`).

This **supersedes the two-file arrangement recorded in
[0006](0006-gvt-dev-contract-migration.md)** (0006's remote-reconciliation
decisions still stand).

## Consequences

- `/gvt-construct3:audit-c3-conventions` reads the marker from `.gvt-agent.json`
  and no longer emits the `.genvid-agent.json` deprecation finding.
- `/gvt-dev:audit-conventions` is unaffected — its keys stayed put.
- Distinct keys, one file: `commands`/`repo` (gvt-dev) and `features`/`paths`
  (gvt-construct3) don't overlap, so the drift risk 0006 avoided by splitting is
  now avoided by non-overlapping ownership within one file.
- `CLAUDE.md` is updated to describe the single-file convention and the
  `genvid-c3 → gvt-construct3` rename.
