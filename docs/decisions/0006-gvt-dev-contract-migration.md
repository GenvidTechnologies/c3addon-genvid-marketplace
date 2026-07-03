# 0006. Migrate to the gvt-dev contract (`.gvt-agent.json`); remotes reconciled to GitHub-only

- **Status:** accepted _(the two-file split is superseded by [0007](0007-consolidate-agent-config.md); the remote-reconciliation decisions still stand)_
- **Date:** 2026-07-02
- **Issue:** — (no tracked issue; established during a convention audit)

## Context

Two things changed since [0005](0005-genvid-dev-github-host.md):

1. The workflow-convention plugin was **renamed** `genvid-dev` → `gvt-dev`, and
   with it the contract file `.genvid-agent.json` → `.gvt-agent.json`. The
   `gvt-dev` audit (`/gvt-dev:audit-conventions`) reads `.gvt-agent.json` and no
   longer looks at `.genvid-agent.json`. Because this repo had adopted the *old*
   file name, the audit classified it as **greenfield** and its `--fix`
   scaffolded a fresh, empty `.gvt-agent.json` rather than porting the existing
   `commands`/`repo` values from `.genvid-agent.json`.
2. The dual-remote setup 0005 was written against no longer exists. `origin` is
   now a single GitHub remote (`GenvidTechnologies/c3addon-genvid-marketplace`);
   the Bitbucket remote is gone. The mirrored-remote mismatch warning 0005
   accepted as a standing compromise is therefore cleared.

Separately, `.genvid-agent.json` still carried `commands`/`repo` blocks — now
vestigial, since only the **genvid-c3 / gvt-construct3** C3 plugin reads that
file (for `features.c3` / `paths.c3project`), and it declares no expectation for
`commands` or `repo`.

## Decision

- Adopt `.gvt-agent.json` as the gvt-dev workflow contract, carrying
  `commands.validate` = `npm run lint && npm run build`, `repo.host` = `github`,
  `repo.default_branch` = `main` — the same values 0005 established, in their new
  home (reusing the toolchain from [0001](0001-typescript-eslint-toolchain.md);
  no test suite exists).
- Copy the plugin's canonical `CONVENTIONS.md` to the repo root (part of the
  gvt-dev contract) and index it from [`../TOC.md`](../TOC.md).
- Strip the vestigial `commands`/`repo` blocks from `.genvid-agent.json`, leaving
  it a pure C3 marker (`features.c3`, `paths.c3project`). Each file now has one
  owner — no duplicated keys to drift.
- Record GitHub as the sole remote; drop 0005's Bitbucket-mirror compromise.

This **supersedes [0005](0005-genvid-dev-github-host.md)**.

## Consequences

- `/gvt-dev:audit-conventions` passes with all required expectations satisfied.
- `.gvt-agent.json` = gvt-dev (workflow); `.genvid-agent.json` = genvid-c3 (C3
  marker). `CLAUDE.md` documents the split so the two aren't confused again.
- `git push` targets GitHub by default; the old `git push github main` dance is
  gone.
- Any other genvid-dev-era repo will hit the same greenfield-misclassification on
  upgrade (the audit's legacy detector only knows the `burbank-claude-config`
  submodule era, not the `.genvid-agent.json` → `.gvt-agent.json` rename); a
  plugin fix to port the old file forward was proposed upstream.
