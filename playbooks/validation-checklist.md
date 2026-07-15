# Validation Playbook

> **Artifact class:** Playbook
> **Written by:** Builder tier (Claude, 2026-06-21; authorized by Damien — delivered ahead of WO-0004; see Ledger L-014)
> **Amended by:** PR + human review
> **Read by:** Builder/Gatekeeper, at a work order's `review → complete` transition
> **Validated by:** Human review (no schema; structural validation only, per Constitution §10.4)

Manual checklist run before a work order transitions `review → complete` (Constitution §9.1's three-part exit condition; §10.4's "structural validation... and cross-reference integrity"). Gives Ledger L-007's deferred-gatekeeper decision a tractable, written manual step — it does not introduce automation, and L-007 stays open until work-order volume justifies that.

## 1. Frontmatter Validation

- [ ] `schema` field matches the canonical schema id
- [ ] `status` is a legal state-machine value (Constitution §8)
- [ ] `acceptance_checks` have clear pass/fail criteria, not subjective language ("works well")
- [ ] `writes` paths are relative, not absolute
- [ ] No field contains placeholder text ("TODO", "FIXME", "TBD", or an unresolved `!!TOKEN!!`)
- [ ] Any `clarifications` entries are `resolved` — an `open` entry should not have passed `draft → released`

## 2. Cross-Artifact Consistency

- [ ] Every path in `writes` exists on disk, or is created by one of this order's own steps
- [ ] Every id referenced in `reads` / `discovery_roots` / `writes` resolves to a real file or section
- [ ] Every acceptance check is independently testable — a reviewer could verify it without asking the author
- [ ] The work order's `status` in frontmatter matches its row in `capability-map.md`; on conflict, frontmatter wins and the map is repaired (Constitution §3)
- [ ] No id collisions across work orders, features, or ADRs
- [ ] Agent or skill references used by this order exist under `agents/` or `skills/` respectively
- [ ] Tech-stack or library decisions made during execution are recorded (ADR or evidence block), not left implicit
- [ ] No `open` ledger entry blocks this order's closeout; entries this order resolves are updated to `resolved` (ledger bodies are never rewritten — append status only)

## 3. Markdown & Governance Hygiene

- [ ] Governance header (written by / amended by / read by / validated by) present on every new or modified governed artifact
- [ ] All external links use HTTPS
- [ ] No artifact introduces a noun outside the charter's artifact-class enumeration without a ledger entry (see L-010 for the existing precedent)

## When to Run

Before any `review → complete` transition. For lighter-weight changes delivered outside the work-order system (the L-011 lightweight path), run section 3 at minimum and log the gap in the ledger.

## Provenance

Synthesizes proposals #2 ("Cross-Artifact Consistency Checks") and #5 ("Validation Playbook") from `references/enhancement-proposal-062126.md` into one artifact rather than two near-duplicate checklists — splitting them would itself be the "context bloat / blind references" pattern the source research warns about. One item from the source proposal #5 was dropped: a check for "no bare `Math.random()` patterns in reference code," which doesn't apply to a docs-only repository with no reference code requiring determinism review.
