---
okf_version: "0.2"
---

# Wiki Index

This wiki is the documentation of the Genvid Construct 3 marketplace addon. It lists every page under `wiki/`, grouped under section headings, one line each. Each entry's text is the linked page's frontmatter `description`. The page format and the maintenance rules are in [the schema](schema.md). The change log is [log.md](log.md).

Outside the wiki, `CLAUDE.md` at the repository root gives the project overview: the repository layout (addon SDK in `src/`, C3 stub in `sample/`), the build commands, the commit convention and the gvt-construct3 wiring. `CONVENTIONS.md` is the gvt-dev plugin's convention contract (workflow, branching, documentation and agent expectations), a copy that the plugin manages.

## Decisions

* [Decision Records](decisions/index.md) - The architecture decision records, in MADR-lite format.

## Process

* [Issue Triage Conventions](issue-triage.md) - Labels, priorities, required fields and the gh recipes that /gvt-dev:triage-issues uses for this repository's issues.

## Schema

* [Wiki Maintenance Schema](schema.md) - The maintenance rules for this wiki: page format, links, the decisions location, create-vs-update lifecycle, staleness policy and the verb contract.
