---
type: convention
title: Issue Triage Conventions
description: Labels, priorities, required fields and the gh recipes that /gvt-dev:triage-issues uses for this repository's issues.
---

# Issue Triage Conventions

> Project conventions consumed by `/gvt-dev:triage-issues`. The skill copies this
> file to the contract path `.gvt-agent.json` resolves for `docs/issue-triage.md`; edit it for your tracker and taxonomy. The companion
> **access mechanics** (fetch queries, label names) live in the `bugTracker`
> block of `.gvt-agent.json` — see the skill's SKILL.md for that block.
>
> This is the **structured-taxonomy variant** (`type:*`/`priority/*`/`area:*`).
> In this repository `.gvt-agent.json` sets `paths['docs/issue-triage.md']` to
> `wiki/issue-triage.md`.
>
> The commands below use **GitHub Issues via the `gh` CLI**, and every command
> names the repository with `--repo GenvidTechnologies/c3addon-genvid-marketplace`,
> so it works from any folder. The section headings must stay the same: the skill
> and the analyst locate guidance by heading.

## Types

- `bug` — incorrect behavior in shipped functionality (default).
- `crash` — hard failure, exception, or hang.
- `regression` — worked in a prior build, broken now.

Set the type via the matching `type:*` label; exactly one per issue.

**Non-defect work** (a feature request or a docs change) is *not* a
`type:*` — `type:*` is reserved for defects. Classify it by its category label
instead (`enhancement` or `documentation`) and leave `type:*` unset. It
still gets a `priority/*` and at least one `area:*`.

## Priorities

- `P0` — blocks release or breaks the build; fix now.
- `P1` — major feature broken, no workaround; fix this sprint.
- `P2` — broken with a workaround, or a minor feature; schedule.
- `P3` — cosmetic or nice-to-have; backlog.

Decision rule: pick by the worst **observable** impact, not the suspected cause.

## Labels

- `type:*` — exactly one for a defect (mutually exclusive): `type:bug`, `type:crash`, `type:regression`.
- category — `enhancement` or `documentation` for non-defect work.
- `area:*` — one or more subsystem tags:
  - `area:addon` — the addon source in `src/`.
  - `area:sample` — the `sample/` Construct 3 project.
  - `area:ci` — the GitHub Actions workflows and the release.
  - `area:docs` — documentation: `docs/` or `wiki/`, `README.md`, `CLAUDE.md`.
- `priority/*` — exactly one: `priority/P0` … `priority/P3`.
- `needs-info` — set when required fields are missing; cleared when supplied.
- `duplicate` — set on non-canonical members of a duplicate cluster.
- `triaged` — set **last**, by the skill, when triage is complete.

The triager sets `type:*`, `area:*`, and `priority/*`. Reporters may set `area:*`.

## Required fields

Every triaged **bug** must have: a reproduction (steps or a failing case),
expected vs. actual behavior, the build/version, and at least one `area:*` label.
Missing any of these → add `needs-info` and comment exactly what is missing.

A **non-defect** issue (enhancement, docs) has no repro or build/version;
it instead needs a clear **proposed change** (or acceptance criteria, as `- [ ]` rows under an `## Acceptance Criteria` heading) and at least
one `area:*` label. Never `needs-info` an enhancement for lacking a repro — flag
it only when the proposed change itself is too vague to act on.

## Splitting

Split when one issue bundles unrelated defects, or when a single defect spans
subsystems that ship independently. Prefer **sub-issues** (a task-list of
checkboxes referencing new issues) when the parent is a tracking umbrella;
prefer **separate issues** when the parts share no parent. Keep the original as
the canonical/umbrella and move each split-out defect's repro into its own issue.

## Duplicates

Policy: **link, do not auto-close.** For a duplicate cluster, choose the
canonical (usually the oldest with the best repro), add `duplicate` to the
others, and comment `Duplicate of #<canonical>` on each. Close a duplicate only
with explicit per-item approval.

## Dependencies

Express a dependency with a comment on the blocked issue: `Blocked by #<id>`
(optionally `Blocks #<id>` on the other). For umbrellas, list dependencies as a
GitHub task-list under a `Depends on` heading.

## Mutation recipes

The exact commands the triage skill runs to apply **approved** changes. `{id}`,
`{type}`, `{otherTypes}`, `{p}`, `{otherPriorities}`, `{a}`, `{canonical}`, `{other}`, `{title}`,
`{tmpfile}`, `{triagedLabel}`, `{needsInfoLabel}`, and `{closeReason}` are substituted
by the skill. `$R` stands for `GenvidTechnologies/c3addon-genvid-marketplace`.

When a recipe's set would be empty, omit its --remove-label flag.

{otherTypes} is the set of type labels, written out in full, with the label being added removed; {otherPriorities} is the set of priority labels, written out in full, with the label being added removed.

- Set type: `gh issue edit {id} --repo $R --add-label "type:{type}" --remove-label "{otherTypes}"`
- Set priority: `gh issue edit {id} --repo $R --add-label "priority/{p}" --remove-label "{otherPriorities}"`
- Add area: `gh issue edit {id} --repo $R --add-label "area:{a}"`
- Remove area: `gh issue edit {id} --repo $R --remove-label "area:{a}"`
- Edit body (language fix / fill missing info): `gh issue edit {id} --repo $R --body-file {tmpfile}` — the skill writes the approved new body to `{tmpfile}` first
- Comment: `gh issue comment {id} --repo $R --body-file {tmpfile}` — the skill writes the comment text to `{tmpfile}` first
- Flag missing info: `gh issue edit {id} --repo $R --add-label {needsInfoLabel}` (pair with a Comment saying what's missing)
- Mark duplicate: `gh issue edit {id} --repo $R --add-label duplicate` then `gh issue comment {id} --repo $R --body "Duplicate of #{canonical}"`
- Close duplicate (only with approval): `gh issue close {id} --repo $R --reason "not planned" --comment "Duplicate of #{canonical}"`
- Close as completed or not planned (only with approval): `gh issue close {id} --repo $R --reason "{closeReason}"` — post the reason first through the Comment recipe; `{closeReason}` is `completed` or `not planned`
- Create split issue: `gh issue create --repo $R --title "{title}" --body-file {tmpfile} --label "type:{type},area:{a}"` — the skill writes the drafted split body to `{tmpfile}` first; for a **non-defect** split, replace `type:{type}` with the category label (`enhancement`/`documentation`)
- Link dependency: `gh issue comment {id} --repo $R --body "Blocked by #{other}"`
- Stamp triaged: `gh issue edit {id} --repo $R --add-label {triagedLabel}`

Read the labels back after this edit: confirm the issue now carries exactly the intended labels before it is stamped triaged (use the tracker's read-one query, or the native issue view).
