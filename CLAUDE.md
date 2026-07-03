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
[ADR 0007](docs/decisions/0007-consolidate-agent-config.md)):

- **gvt-dev workflow contract** — `commands.validate` (`npm run lint && npm run
  build`), `repo.host` (`github`) and `repo.default_branch` (`main`), read by the
  gvt-dev skills/agents (validator, create-pr, rebase, …). Validate with
  `/gvt-dev:audit-conventions`. See
  [ADR 0006](docs/decisions/0006-gvt-dev-contract-migration.md).
- **gvt-construct3 C3 marker** — `features.c3` and `paths.c3project` (see below).

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
