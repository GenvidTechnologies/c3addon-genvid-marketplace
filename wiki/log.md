# Wiki Log

Record of every change to the wiki's page set: what changed, why, and which source drove it, grouped under `## YYYY-MM-DD` date headings (ISO 8601) with the newest date group first. Entries are prose bullets, for example `* **Update**: …`, `* **Creation**: …`, `* **Deprecation**: …`.

Add newest first. Never edit or remove a prior entry. If a past entry needs a correction, add a new entry that says so. The maintenance rules are in [the schema](schema.md).

## 2026-11-22

* **Update**: `wiki/issue-triage.md` (GenvidTechnologies/c3addon-genvid-marketplace#6). Dependencies says that an issue whose proposed change rests on an issue or release in another repository names it in full and says which step depends on it, and that triage and the start of the work read its current state. No `raw/` capture drove this run: the source is the retro of #6, whose step 3 assumed that GenvidTechnologies/claude-code-plugin-gvt-dev#938 had not shipped, while it had closed on 2026-11-21.
* **Creation**: `wiki/index.md`, `wiki/log.md` and `wiki/schema.md` (GenvidTechnologies/c3addon-genvid-marketplace#6). The wiki becomes the only documentation surface. `wiki/index.md` replaces `docs/TOC.md`, and `.gvt-agent.json` sets `wiki.wikiDir` and points `paths['docs/TOC.md']` at it. The schema is the `maintain-wiki` template (gvt-wiki 0.2.0) with this project's paragraph, staleness rule and link policy. No `raw/` capture drove this run: the source is `docs/TOC.md` at a44c943.
* **Creation**: `wiki/decisions/` (GenvidTechnologies/c3addon-genvid-marketplace#6). The seven decision records move from `docs/decisions/` with their numbers and file names unchanged, and each one gains OKF frontmatter (`type: decision-context`; 0005, superseded by 0006, is `status: deprecated`). `wiki/decisions/index.md` lists them, and `.gvt-agent.json` points `paths['docs/decisions/']` at the folder. In 0006 the only body change is the link to the documentation index, which now points at `wiki/index.md`. No `raw/` capture drove this run: the source is `docs/decisions/` at a44c943.
* **Update**: `wiki/issue-triage.md` (GenvidTechnologies/c3addon-genvid-marketplace#6). The page is listed in `wiki/index.md` under Process, and its frontmatter gains `tags` and `status` as the schema's page format gives them. The body is unchanged. No `raw/` capture drove this run.
