# Migration note — Planning Docs Toolkit v0.2

**Date:** 2026-07-03  
**Applies to:** Any repo that wants to adopt patterns from `planning-cascade-proposal-070326.md` (v0.2)

---

## Adoption is optional and per-repo

This toolkit is a **proposal**, not workspace-wide governance. Each repo decides independently whether to use any part of it.

- No requirement to rename directories, add `STATE.md`, or restructure existing docs.
- No requirement to adopt Boresight terminology or parent-chain validators.
- Existing files (`CURRENT_STATE.md`, `docs/work-orders/`, `AGENTS.md`, etc.) remain authoritative for repos that already have them.

---

## If you want to try it

**Minimal adoption (recommended start):**

1. Use the session plan template for non-trivial agent or human work blocks.
2. When scoping a PR, use the medium spec template if your repo does not already have an equivalent.
3. Add `Do not start` and `Already done / do not redo` sections to existing work orders as you touch them — no bulk rewrite.

**Phase-level adoption (optional):**

1. Add or refresh a phase README (table of work orders + merge gates) if the repo has multiple parallel streams.
2. Link new work orders to the phase README with an optional `Serves:` line.

**Vision-level adoption (optional):**

1. Only for repos that lack a north-star doc and actively need one.
2. Keep vision docs short; link to architecture and decision logs for detail.

---

## Mapping from v0.1 concepts (if you used the earlier draft)

| v0.1 idea | v0.2 disposition |
|---|---|
| Six planning levels | Reduced to 4 levels on the menu; use 3–4 in practice |
| Required `STATE.md` | Optional / experimental; use repo's existing current-state doc |
| Fixed `Serves:` parent chain | Loose one-line convention |
| Line/token budgets | Guidelines only |
| Prescriptive directory taxonomy | Descriptive examples; do not rename existing dirs |
| Boresight isomorphism | Appendix only; plain names preferred |
| Compaction recovery claims | Removed unless backed by repo-specific examples |
| Framework / cascade framing | Reframed as toolkit |

---

## What not to do

- Do not edit other repos as part of "migration" unless that repo's owner opts in.
- Do not treat this proposal as canonical in `AGENTS.md` or agent-os until explicitly promoted.
- Do not impose repo-wide standards from a `references/` proposal file.

---

## Suggested review before v0.3

- One real Crowbot session closed out using the session plan template
- One existing work order compared against medium template — note gaps only, no forced rewrite
- Owner decision: workspace vision note yes/no
