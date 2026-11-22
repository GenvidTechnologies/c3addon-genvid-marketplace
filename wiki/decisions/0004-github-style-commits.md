---
type: decision-context
title: 'GitHub-style commit subjects; drop the Jira `BUR-XXXX` prefix'
description: 'GitHub-style commit subjects; drop the Jira `BUR-XXXX` prefix'
tags: [decisions]
status: stable
---
<!-- Retroactive record. Decision date from commit a21694a (2026-06-19). -->

# 0004. GitHub-style commit subjects; drop the Jira `BUR-XXXX` prefix

- **Status:** accepted
- **Date:** 2026-06-19
- **Issue:** — (no tracked issue; established in commit `a21694a`)

## Context

Early history uses Jira-style commit subjects (`BUR-6719: …`,
`chore - BUR-0000: …`). Those `BUR-####` tickets live in Jira and no longer map
to anything now that the project's canonical home is GitHub (see
[0005](0005-genvid-dev-github-host.md)). New commits that copied the prefix were
producing dead references.

## Decision

Write **plain, imperative commit subjects** with no ticket prefix
(e.g. "Add CLAUDE.md and project tooling config"). The convention is documented
in `CLAUDE.md` so the commit tooling reads it, and recorded in project memory.
Link work to GitHub issues/PRs in the body or via GitHub's own linking, not via
a Jira ticket token in the subject.

## Compromise

The existing `BUR-####` history is left untouched (rewriting published history
isn't worth it); `git log` therefore shows a mix of old prefixed and new
unprefixed subjects. Conventional Commits (`feat:` / `chore:` …) was not adopted
— no requirement for machine-parseable types or automated changelogs exists yet;
plain imperative subjects are the lighter fit.

## Consequences

- No more dead Jira tokens in new commit subjects.
- Contributors must ignore the prefix pattern visible in older `git log` output.
- If automated release notes are wanted later, revisit and consider Conventional
  Commits as a superseding decision.
