<!-- Superseded by 0006 (gvt-dev rename + GitHub-only remote). Retroactive record. Decision date from commit e026c5f (2026-06-19). -->

# 0005. Adopt the genvid-dev contract; treat GitHub as the canonical host

- **Status:** superseded by [0006](0006-gvt-dev-contract-migration.md)
- **Date:** 2026-06-19
- **Issue:** — (no tracked issue; established in commit `e026c5f`)

## Context

Adopting the `genvid-dev` convention contract requires the workflow skills
(`create-pr`, rebase, issue triage, validator) to know the project's validate
command and its host. This repo has **two** git remotes: `origin` →
Bitbucket (`genvidtech`) and `github` → GitHub (`genvid-holdings`). Host
inference from `origin` would pick Bitbucket, but the upstream bugs in
[0003](0003-genvid-c3-nested-project.md) were filed on GitHub and the project is
moving to GitHub (consistent with dropping Jira in
[0004](0004-github-style-commits.md)).

## Decision

Satisfy the `genvid-dev` contract and declare GitHub as canonical:

- `.genvid-agent.json` `commands.validate` = `npm run lint && npm run build`
  (no test suite exists), reusing the toolchain from
  [0001](0001-typescript-eslint-toolchain.md).
- `.genvid-agent.json` `repo.host` = `github`, `repo.default_branch` = `main` —
  explicitly **overriding** the Bitbucket `origin` inference.
- Add `docs/TOC.md` as the documentation index required by the contract.

## Compromise

Setting `repo.host: github` while `origin` is still Bitbucket leaves a standing
(non-fatal) audit warning about the mismatch. Accepted deliberately: the
`repo.host` override exists for exactly this mirrored-remote case. Renaming the
remotes (making GitHub `origin`) was left to the maintainer rather than rewritten
automatically. `git push` still defaults to Bitbucket; pushing to GitHub needs
`git push github main`.

## Consequences

- `create-pr`, rebase, and issue skills target GitHub (`gh`) and use `main`.
- The validator runs lint + build as the project's full check.
- The remote mismatch warning persists until the remotes are reconciled; flip
  this decision (or the remote) if Bitbucket ever becomes canonical again.
