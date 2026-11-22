# CLAUDE.md

Project-specific guidance for the Genvid Construct 3 marketplace addon.

## What this repo is

A **Construct 3 addon (plugin) SDK** — not a C3 game project.

- `src/` — the addon source: `addon.json`, `aces.json`, `plugin.ts`, `c3runtime/`, `lang/`, `icon.svg`. This is what ships as the `.c3addon`.
- `sample/` — a small **C3 project stub** (one event sheet, one layout, three object types) used to exercise the addon in the editor. This is the only Construct 3 project in the repo.
- `SDK/` — the Construct 3 addon SDK (git submodule).

## Commands

```bash
npm run lint            # eslint src
npm run build           # tsc -p src --outDir dist (+ postbuild copies json/svg/lang)
npm run all:windows     # lint + build + zip -> Genvid_Marketplace.c3addon (7z)
npm run all:posix       # lint + build + zip -> Genvid_Marketplace.c3addon (zip)
npx http-server src --cors   # dev: load http://localhost:8080/addon.json in C3 (not 127.0.0.1, CSP)
```

Run `npm ci` once in a new worktree before the first commit. The gvt-dev
pre-commit hook runs `npm run lint`, and without `node_modules` it fails with
`'eslint' is not recognized`, which looks like a broken toolchain.

## CI / Release (GitHub Actions)

CI lives in `.github/workflows/` (the repo migrated off CircleCI):

- `ci.yml` — runs `npm ci && npm run lint && npm run build` on pushes to `main`
  and on pull requests. Validation only; produces no artifact.
- `release.yml` — on pushing a version tag (`[0-9]+.*`, e.g. `1.0.0.0`), lints,
  builds, zips `Genvid_Marketplace.c3addon`, and publishes it as an asset on a
  GitHub Release for that tag.

To cut a release, bump `version` in `src/addon.json`, commit, then push a matching
tag:

```bash
git tag 1.0.0.1 && git push origin 1.0.0.1
```

The legacy Azure-blob / 1Password upload was dropped — GitHub Releases is now the
only distribution target.

## Commit convention

Plain GitHub-style **imperative subject lines**. Do **not** use the `BUR-XXXX:`
(or `chore - BUR-0000:`) prefix seen in older history — those are legacy Jira
tickets that no longer map to anything on GitHub.

## Agent contract file

A single `.gvt-agent.json` at the repo root serves **both** the `gvt-dev`
workflow plugin and the `gvt-construct3` C3 plugin — there is no longer a
separate `.genvid-agent.json` (see
[ADR 0007](wiki/decisions/0007-consolidate-agent-config.md)):

- **gvt-dev workflow contract** — `commands.validate` (`npm run lint && npm run
  build`), `repo.host` (`github`) and `repo.default_branch` (`main`), read by the
  gvt-dev skills/agents (validator, create-pr, rebase, …). Validate with
  `/gvt-dev:audit-conventions`. See
  [ADR 0006](wiki/decisions/0006-gvt-dev-contract-migration.md).
- **gvt-construct3 C3 marker** — `features.c3` and `paths.c3project` (see below).
- **Issue triage** — the `bugTracker` block and `paths['docs/issue-triage.md']`,
  read by `/gvt-dev:triage-issues`. The triage conventions (labels, priorities,
  `gh` recipes) live in [`wiki/issue-triage.md`](wiki/issue-triage.md).
- **Documentation** — `wiki.wikiDir` (`wiki`) and the `paths` overrides for
  `docs/TOC.md` (`wiki/index.md`) and `docs/decisions/` (`wiki/decisions/`).
  gvt-dev 6.0.0 and later derive these same locations from `wiki.wikiDir`
  (GenvidTechnologies/claude-code-plugin-gvt-dev#938). An older gvt-dev falls
  back to `docs/TOC.md` and `docs/decisions/` when the overrides are unset, so
  keep them.

## Documentation

The wiki is the only documentation surface: there is no `docs/` folder. It
follows the `maintain-wiki` format (gvt-wiki plugin): every page opens with OKF
frontmatter, [`wiki/index.md`](wiki/index.md) lists the pages,
[`wiki/log.md`](wiki/log.md) records each change to them (newest first), and
[`wiki/schema.md`](wiki/schema.md) holds the rules. A new or updated page gets
an index entry and a log entry. Decision records live in
[`wiki/decisions/`](wiki/decisions/index.md); add one with
`/gvt-dev:create-adr`. Check the wiki with `/gvt-wiki:maintain-wiki lint`.

The lint script (`wiki-lint.mjs` in the gvt-wiki plugin) checks dead links,
out-of-bundle links and orphaned pages only. It does not read frontmatter
(GenvidTechnologies/claude-code-plugin-gvt-wiki#19), so a page with no
frontmatter, an empty `type`, or a `title` that differs from its H1 passes it.
Check those by reading the page. `audit-conventions` does not check `wiki/`
either: its link and orphan scans skip the wiki bundle and say so in their Info
lines.

## gvt-construct3 plugin wiring

This repo adopts the `gvt-construct3` plugin convention (formerly `genvid-c3`),
scoped to the nested `sample/` project:

- `.gvt-agent.json` (repo root) — the C3 marker; `paths.c3project` points at `sample/project.c3proj`.
- `sample/domain-config.json`, `sample/construct3-chef.config.json` — live at the **C3 project root** (`sample/`), not the repo root.
- The bundled `construct3-chef` (≥0.10.2) and `c3-domain-manager` (≥0.5.0) MCP
  servers **auto-discover** the project root (an immediate child containing
  `project.c3proj`), so they operate on `sample/` with no `--project-dir` flag.
  Override with the `C3_PROJECT_DIR` env var if needed.

Run `/gvt-construct3:audit-c3-conventions` to validate the contract.
