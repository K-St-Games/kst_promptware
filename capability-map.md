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

## Capability: Promptware Library Layer (v0.1)

Selectable rules, skills, agents, and profiles that downstream projects copy and install.

> **Note:** F-008 through F-013 were delivered outside the work-order system (no WO ids exist). The status `delivered` is a map-level convenience marker only — not a work-order state. See Ledger L-011 for the coverage-gap record.

| Feature | Description | Work Order | Status |
|---|---|---|---|
| F-008 Common rules | 6 always-apply rule files in `rules/common/` | — | `delivered` |
| F-009 Language rule stubs | TypeScript, Python, Go stubs in `rules/<lang>/` | — | `delivered` |
| F-010 Development agents | 5 subagent definitions in `agents/`: architect, code-reviewer, security-reviewer, spec-miner, silent-failure-hunter | — | `delivered` |
| F-011 Governance agents | 3 subagent definitions in `agents/`: planner, builder, gatekeeper | — | `delivered` |
| F-012 Skills | 7 invokable skills in `skills/`: ADR, multi-agent exploration, project philosophy, work-order intake, deep research, code review, session handoff | — | `delivered` |
| F-013 Installation profiles | 3 manifests in `profiles/`: Minimal, Standard, Full | — | `delivered` |
| F-014 Agent + skill schemas | `schemas/agent.schema.json`, `schemas/skill.schema.json` | — | `delivered` |
| F-015 Validation playbook + WO clarifications | `playbooks/validation-checklist.md` closeout checklist + `clarifications` frontmatter field on `templates/work-order.md` (v0.3.0) | — | `delivered` |
| F-016 PM export | `pm-export` skill + `schemas/pm-export.schema.json` + derived `pm/pm-export.json` — tool-agnostic projection of work orders, features, and ledger for external trackers | — | `delivered` |
| F-017 Repository conformance and consolidation | Dependency-free active-doc audit, current drift repair, and bounded follow-up sequence for governance, session/profile, and PM adapter proof | WO-0008 | `draft` |

## Companion Layer (not bundle-governed)

The v3 session framework (`/templates/CONTEXT.md`, `PROGRESS.md`, `HANDOFF.md`, `WORKFLOW.md`) operates alongside the bundle for session execution. Integration deferred — Ledger L-008.
