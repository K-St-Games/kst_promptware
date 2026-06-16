# Changelog

> **Artifact class:** Changelog (repo meta)
> **Written by:** Builder tier at closeout of identity-changing work orders
> **Amended by:** Append per release; entries are never rewritten
> **Read by:** Humans and agents orienting across framework versions
> **Validated by:** Gate lint (governance header present)

Framework versions of this repository. Earlier versions live in [`/archive`](archive/README.md).

## v4.0.0 — 2026-06-12 (codename: Boresight)

Specification-first work-order system bootstrapped via WO-0001; the repo's identity becomes the spec factory, with the v3 session framework retained as a companion layer.

- Added north-star artifacts: `charter.md`, `constitution.md`.
- Added work-order machinery: `templates/work-order.md` (Template v0.2.0), `schemas/work-order.schema.json`, `/work-orders/`, `capability-map.md`, `ledger.md`.
- Added bundle directories: `/features`, `/decisions`, `/playbooks`, `/commands`.
- Added `LICENSE` (MIT, Damien O'Donnell) and this changelog.
- Reconciled `AGENTS.md` and `README.md` to the v4 identity (pointers only).
- v3 session templates (`CONTEXT`/`PROGRESS`/`HANDOFF`/`WORKFLOW`) marked as companion layer (Ledger L-008).

## v3.0.0 — 2026-05-25

Promoted to the single canonical framework (`templates/`), consolidating the prior version sets.

- PM → IC → QA three-perspective loop (QA added as explicit verification step).
- Strict size budgets on state files; trigger-based compaction (not per-session ritual).
- `HANDOFF.md` as overwrite, not append.
- Lightweight Track — the three-hat loop became optional by task size.

## v2.0.0 — 2026-01 era

Two-role model (PM_Agent / IC_Agent) with ContextChain and rolling logs. Archived at `archive/templates-v2-framework/` and `archive/templates-v02/`.

## v1.0.0 — 2025-11-02

First-pass context docs for an AI developer agent; generalized from game-specific to application development. Abandoned approach — archived at `archive/v01/`.
