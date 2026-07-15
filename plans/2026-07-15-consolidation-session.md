# Session Plan: 2026-07-15 — P0 Harness Fixes + Toolkit Consolidation

**Purpose:** Fix the harness-facing install defects from `references/repo-review-070326.md` §1 (currently corrupting every downstream install), and consolidate the planning-docs toolkit that landed in PRs #8–#10 into single canonical homes.

**Status:** active · **Serves:** repo-review-070326.md §6 punch list (P0) + post-merge feedback of 2026-07-15
**Source of truth:** `AGENTS.md` · `references/repo-review-070326.md` §1 · this plan
**Branch:** `claude/repo-review-feedback-zifrlr` (already reset to main tip — work here; do not branch from it)
**Size legend:** S ≈ one hour · M ≈ half a day · L = multi-session

**Entry gate (whole session):** read `AGENTS.md`, then `references/repo-review-070326.md` §1, then this plan fully. All decisions below are settled — execute, don't re-litigate.

---

## Decided — do not reopen

| # | Decision | Resolution |
|---|---|---|
| D1 | Canonical toolkit doc | `references/planning-doc-toolkit-v0.2.md`. The chimera file is restored to proposal history (D3). |
| D2 | Canonical templates home | `templates/planning-docs/` (operational home). `references/planning-docs-toolkit/` is removed after merging unique content in. |
| D3 | `planning-cascade-proposal-070326.md` disposition | Restore to its v2.1 content, then banner it: superseded by the toolkit, kept as design history. Do not delete. |
| D4 | Toolkit Appendix A ("Boresight isomorphism") | Delete. It redefines the repo's v4 codename incorrectly. Replace with one line: "Heavy specs convert nearly 1:1 to Boresight work orders (this repo's v4 system) if a project adopts the Full profile." |
| D5 | `code-review` skill name collision | Rename to `structured-code-review` (directory + `name:` field + every reference). Shadowing Claude Code's bundled `/code-review` silently is worse than the rename ripple. |
| D6 | Skills catalog/profile contradiction | `multi-agent-design-exploration` and `project-philosophy-document` become `profile: full` (frontmatter + catalog). Standard stays at 4 skills. |
| D7 | Model guidance | Prose recommends family aliases only (`sonnet`, `opus`); no concrete model IDs anywhere in prose. Schema enums unchanged. |
| D8 | Workspace-specific examples in toolkit docs | Genericize (Crowbot / agent-os / HTF / `C:\Lonnie\...` → neutral placeholders). Meaning kept, private context removed. |

## Already done — do not re-do

- pm-export v0.2 (skill, schema, export, review absorption) — do not touch its logic or schema.
- `playbooks/validation-checklist.md`, WO `clarifications` field, work-order template v0.3.0 — landed and correct.
- Toolkit v0.2 *content* and cascade v2.1 *content* — the design debate is settled; this session only consolidates files.

---

## Track A: P0 Harness Fixes  — **Size M · 🔴 do first**

**What:** The four defects that make documented installs silently fail on Claude Code, plus the small rot items from the same review.

**Writes:** `CLAUDE.md` (new) · `skills/README.md` · `profiles/{minimal,standard,full}.md` · `skills/code-review/` → `skills/structured-code-review/` · `agents/README.md` · `AGENTS.md` · `references/cursor-recommendations.md` · `guides/microservice-workflow-guide.md`

1. **CLAUDE.md shim.** Create root `CLAUDE.md` containing exactly `@AGENTS.md` (one line). Add a "create `CLAUDE.md` → `@AGENTS.md`" row to each profile's manifest/checklist (Claude Code does not read AGENTS.md natively).
2. **Skill install paths.** Everywhere skills installation is described (`skills/README.md` §How to Install, all three profile manifests — grep `.claude/skills/`): flat file → directory form `.claude/skills/<skill-name>/SKILL.md`. Also fix `skills/README.md`'s "name must match the filename stem" → matches the *directory* name.
3. **Rename per D5.** `git mv skills/code-review skills/structured-code-review`; update `name:` frontmatter, catalog row, `profiles/standard.md` + `full.md` rows, `AGENTS.md` quick-reference row. Add one sentence to `skills/README.md`: project skills sharing a bundled-skill name (`code-review`, `security-review`, `verify`, `run`, `debug`) replace the bundled one — name new skills to avoid accidental shadowing.
4. **Model guidance per D7.** `agents/README.md`: delete the "Current model IDs" sentence and the broken `../skills/claude-api/` link (point to the official model docs instead). `profiles/standard.md`: pin advice becomes `model: sonnet`.
5. **Catalog contradiction per D6.** Update the two SKILL.md frontmatters and the `skills/README.md` catalog column.
6. **Remaining broken links.** `references/cursor-recommendations.md`: 3 root-relative links → correct `../` relative paths. `guides/microservice-workflow-guide.md`: remove the missing-screenshot reference.
7. **AGENTS.md tree:** add `research/` and `plans/` lines to the repository-structure block.

**Verification gates:**
- [ ] `CLAUDE.md` exists; content is exactly `@AGENTS.md`
- [ ] `grep -rn '\.claude/skills/[a-z-]*\.md' --include='*.md' .` → zero matches (no flat-file install instructions remain)
- [ ] `grep -rn 'code-review' skills/ profiles/ AGENTS.md` shows only `structured-code-review` (plus the shadowing warning sentence)
- [ ] Track C link check passes over `agents/`, `references/`, `guides/`

## Track B: Toolkit Consolidation  — **Size M · 🔴**

**What:** One toolkit doc, one templates home, restored proposal history, repaired index.

**Reads:** `git show 5ca5e7f:references/planning-cascade-proposal-070326.md` (the v2.1 content to restore — commit is on main's history)
**Writes:** `references/planning-cascade-proposal-070326.md` · `references/planning-doc-toolkit-v0.2.md` · `references/planning-docs-toolkit-migration.md` · `references/planning-docs-toolkit/` (delete) · `templates/planning-docs/*` · `references/README.md`

1. **De-chimera per D3.** Overwrite `planning-cascade-proposal-070326.md` with the `git show` output above, then add to its Status line: "Superseded by [`planning-doc-toolkit-v0.2.md`](planning-doc-toolkit-v0.2.md) — kept as design history (v1 → v2 → v2.1 → toolkit lineage)."
2. **Canonical toolkit per D1 + D8 + D4.** In `planning-doc-toolkit-v0.2.md`: genericize workspace-specific examples (D8); delete Appendix A and add the D4 replacement line; genericize the Crowbot bullet in Appendix B; update its Templates table to `templates/planning-docs/` paths. Apply D8 to the migration note too, and repoint its "Applies to" line at the canonical toolkit file.
3. **Templates single home per D2.** Diff each pair (`references/planning-docs-toolkit/templates/{medium-spec,heavy-spec,session-plan}.md` vs `templates/planning-docs/{medium-spec,heavy-spec,session-weekly-plan}.md`). Merge unique value from the toolkit copies into the `templates/planning-docs/` files (notably the Type/Priority/Status/Serves header block on medium-spec). Then delete `references/planning-docs-toolkit/` entirely and fix every reference to it (grep `planning-docs-toolkit/templates`).
4. **Index repair.** `references/README.md`: re-describe the cascade row as superseded design history; add rows for `planning-doc-toolkit-v0.2.md` (canonical toolkit) and `planning-docs-toolkit-migration.md`; bump Last Updated.

**Verification gates:**
- [ ] `planning-cascade-proposal-070326.md` contains exactly one document (starts `# Proposal: The Planning Cascade`, has the superseded banner)
- [ ] `ls references/planning-docs-toolkit` → does not exist; `grep -rn 'planning-docs-toolkit/templates' .` → zero matches
- [ ] `grep -rniE 'crowbot|agent-os|C:\\\\Lonnie|HTF ' references/planning-doc*` → zero matches
- [ ] Every file in `references/` has an index row (compare `ls references/*.md` against the table)

## Track C: Verify & Close  — **Size S · 🟡**

1. **Link check** (must pass repo-wide except `archive/`):

```bash
python3 - <<'EOF'
import os, re, urllib.parse
broken = []
for root, dirs, files in os.walk('.'):
    dirs[:] = [d for d in dirs if d not in ('.git', 'archive')]
    for fn in files:
        if not fn.endswith('.md'): continue
        p = os.path.join(root, fn)
        for m in re.finditer(r'\[([^\]]*)\]\(([^)\s]+)\)', open(p, encoding='utf-8').read()):
            t = m.group(2)
            if t.startswith(('http://', 'https://', 'mailto:', '#')): continue
            t = urllib.parse.unquote(t.split('#')[0])
            if not t: continue
            r = os.path.join('.', t.lstrip('/')) if t.startswith('/') else os.path.normpath(os.path.join(root, t))
            if not os.path.exists(r): broken.append(f"{p}: {m.group(0)}")
print('\n'.join(broken) or 'CLEAN')
EOF
```

2. Run `playbooks/validation-checklist.md` §3 over every changed file (governance headers intact, HTTPS links, no new nouns).
3. Append a ledger entry (next free L-number): P0 fixes + toolkit consolidation delivered via the lightweight path (L-011 precedent); note the `code-review` → `structured-code-review` rename for downstream consumers.
4. Commit in two logical commits (`fix(library): …` for Track A, `docs(references): …` for Track B), conventional lowercase, specific files staged — never `git add -A`. Push with `git push -u origin claude/repo-review-feedback-zifrlr`.
5. Fill the close-out table below; open a PR only if your operator has authorized it — otherwise report ready-for-PR with the branch name.

## What NOT to Pick Up This Session

- **L-011 change order / any constitution or charter amendment** — human-gated (Damien).
- **WO-0001 ratification (AC-11/AC-12)** — human-gated.
- **pm-export schema or logic changes** — just landed through its own review cycle.
- **Renaming `research/2026-06-21_Promptware_Research_Synthesis.md`** — low value, cross-ref risk.
- **CI automation for the link check** — worthwhile, but a separate proposal (keeps L-007 intact).
- **Any content revision to the toolkit/cascade beyond D1–D8** — the design is settled.

---

## Close-Out (fill at end of session)

| Track | Outcome | Evidence |
|---|---|---|
| A | | |
| B | | |
| C | | |

**Carried forward:** —
**New work surfaced:** —
