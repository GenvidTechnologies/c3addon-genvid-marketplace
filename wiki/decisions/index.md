# Decision Records

This directory contains Architecture Decision Records (ADRs) in **MADR-lite** format.

**Template:** the MADR-lite template is bundled in the `gvt-dev` plugin at `plugin/docs/decision-record.template.md`. `gvt-dev:tech-writer` fills it.

**To add or insert a record:** run `/gvt-dev:create-adr`. It handles numbering, chronological insertion and renumbering. Do not hand-number files.

**Retroactive records:** a record can be backfilled. Its number follows the order of the decisions, not the order in which the files were written. Records 0001 to 0005 are retroactive: a comment at the top of each gives the commit that the decision date comes from.

**Location:** the records are wiki pages (`type: decision-context`) in `wiki/decisions/`. `.gvt-agent.json` points `paths['docs/decisions/']` at this folder, so `/gvt-dev:create-adr` writes new records here. Each record opens with frontmatter. Its body heading `# NNNN. Title` keeps the number. Until GenvidTechnologies/c3addon-genvid-marketplace#6 the records were in `docs/decisions/`, with the same numbers and file names.

## Records

This list is the index of the records. The [wiki index](../index.md) links it.

- [0001](0001-typescript-eslint-toolchain.md) - TypeScript + flat ESLint toolchain for the addon
- [0002](0002-repository-layout.md) - Repository layout: addon in `src/`, sample C3 project in `sample/`
- [0003](0003-genvid-c3-nested-project.md) - Adopt genvid-c3 with the C3 project nested under `sample/`; fix tooling upstream
- [0004](0004-github-style-commits.md) - GitHub-style commit subjects; drop the Jira `BUR-XXXX` prefix
- [0005](0005-genvid-dev-github-host.md) - Adopt the genvid-dev contract; treat GitHub as the canonical host (superseded by 0006)
- [0006](0006-gvt-dev-contract-migration.md) - Migrate to the gvt-dev contract (`.gvt-agent.json`); remotes reconciled to GitHub-only (two-file split superseded by 0007)
- [0007](0007-consolidate-agent-config.md) - Consolidate agent config into a single `.gvt-agent.json`
