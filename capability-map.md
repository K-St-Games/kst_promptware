# Capability Map

> **Artifact class:** Capability Map (derived index)
> **Written by:** Planner tier (WO-0001)
> **Amended by:** Planner tier — statuses derive from work-order frontmatter; on conflict, frontmatter wins and this map is repaired (Constitution §3)
> **Read by:** All tiers, to find active and queued work
> **Validated by:** Gate lint (every feature has a status; every queued order has an id)

Status vocabulary: work-order states per Constitution §8. `queued` is a **map-level marker** for orders not yet cut — it is not a work-order state.

## Capability: Self-Hosting Bundle (v0.1)

The bundle can specify, execute, verify, and reconcile its own work.

| Feature | Description | Work Order | Status |
|---|---|---|---|
| F-001 BOOTSTRAP | Repo foundation: north-star artifacts + work-order machinery (brownfield v4) | WO-0001 | `review` |
| F-002 Artifact templates | Charter, constitution, domain-model, contracts, capability-map, feature, ADR, ledger templates — each with governance header + authoring instructions | WO-0002 | `queued` |
| F-003 Schemas | Feature and capability-map frontmatter schemas | WO-0003 | `queued` |
| F-004 Playbooks & commands | Intake, specification, implementation, verification/repair, change-order | WO-0004 | `queued` |
| F-005 README & design notes | Full README rewrite + lineage essay (stepwise refinement, AV/construction work orders, Ansible, CI/CD) | WO-0005 | `queued` |
| F-006 Worked example | Candidate: promote this bootstrap's own artifacts to `/examples/` | WO-0006 | `queued` |
| F-007 Adapter shims | CLAUDE.md + Copilot instruction templates; pointers only, one screen max | WO-0007 | `queued` |

Queue ordering is provisional — ratified or reordered at WO-0001's `review → complete` gate.

## Companion Layer (not bundle-governed)

The v3 session framework (`/templates/CONTEXT.md`, `PROGRESS.md`, `HANDOFF.md`, `WORKFLOW.md`) operates alongside the bundle for session execution. Integration deferred — Ledger L-008.
