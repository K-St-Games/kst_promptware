# Constitution

> **Artifact class:** Constitution (north star)
> **Written by:** Planner tier (bootstrap intake, WO-0001)
> **Amended by:** Human approval only, via change order
> **Read by:** All tiers, before any governed work
> **Validated by:** Gate lint (governance header, numbered-constraint structure)

Binding content lives in the numbered constraints. Prose between them is rationale and is non-binding (see §6).

**Lean v0 clause (fixed):** this constitution contains only norms negotiated at intake. Silence is not doctrine — an unaddressed question is an open question for the ledger, not an implied permission or prohibition. Amendments follow the change-order path.

## §1. Format & Toolchain

1. Artifacts are plain markdown + YAML frontmatter + JSON Schema (draft-07).
2. **The canonical artifact bundle has no build step and no runtime dependency.** Optional validators are tooling conveniences, never bundle dependencies. The bundle is runtime-agnostic: any harness that reads markdown learns the rules from the artifacts themselves.
3. **Codename neutrality:** "Boresight" is a production codename. It must not appear in machine-readable surfaces — schema `$id`s, frontmatter field names, canonical directory names. Prose, READMEs, and the changelog may use it freely.

## §2. Governance Headers

1. Every governed artifact carries a four-line governance header: **written by / amended by / read by / validated by.**
2. Exception: `/LICENSE` is verbatim legal text and carries no header.

## §3. Status Canon

1. Work-order frontmatter is canonical. The capability map is a derived index.
2. On conflict, frontmatter wins and the map is repaired — never the reverse.

## §4. Directory Structure Encodes Mutation Rights

| Tier | Surfaces | Amendment path |
|---|---|---|
| **North star** | `/charter.md`, `/constitution.md` | Human approval only |
| **Contracts** | `/domain-model.md`, `/contracts.md` (future) | Architect tier + ADR + ledger entry |
| **Map** | `/capability-map.md` | Planner-owned; status derived from work orders |
| **Spawned** | `/features/`, `/work-orders/` | Planner writes, builder conforms |
| **Memory** | `/decisions/`, `/ledger.md` | Append-only |

1. Where an artifact lives declares who may amend it and how.
2. **Brownfield exception (v4.0):** `/templates` is shared between the bundle's work-order template and the v3 companion-layer files until the integration decision lands (Ledger L-008).
3. The v3 session layer (`/templates/CONTEXT.md`, `PROGRESS.md`, `HANDOFF.md`, `WORKFLOW.md`) is a companion layer: operational, unchanged, and not governed by bundle mutation tiers.

## §5. Noun Budget

1. No framework nouns beyond the charter glossary.
2. A new noun must be (a) replaced with an existing glossary noun, (b) added to the glossary via change order, or (c) recorded in the ledger as a deferred terminology decision.

## §6. Two-Audience Rule

1. Binding content lives only in structured elements — frontmatter, numbered constraints, steps, acceptance checks — using the fixed/discretion modality.
2. Prose carries rationale and is non-binding. **On conflict, structure beats prose.**
3. Load-bearing prose is promoted upward over time: prose → numbered constraint → schema field.

## §7. Closed-World Reads, Discovery & Writes

1. **Reads are exhaustive and normative:** knowledge from outside an order's reads list that would shape a norm or contract requires a ledger entry before use.
2. **Discovery roots are factual:** bounded subtrees a builder may inspect for imports, call sites, types, and existing conventions. Discovery may inform mechanical implementation; it may never introduce a norm, contract, requirement, or scope. Discovery that would change intended behavior or an acceptance criterion → ledger entry or escalation before acting.
3. **Writes are exhaustive:** an unlisted surface is out of bounds. *"If it isn't in writes, this order doesn't create it."*
4. **Transitive writes** — a mechanical repair outside `writes` forced by an edit inside it — are permitted only within these classes:

| Class | Allowed without change order? | Obligation |
|---|---|---|
| `mechanical-reference` — import path fix, callsite rename, generated type reference | Yes | Log in reconciliation block |
| `compile-repair` — type fix required by a declared API change, no behavior change | Yes | Log + evidence |
| `behavioral-adjacent` — caller logic, fallback behavior, new conditionals | No — escalate | — |
| `contract-expanding` — new field, event, route, or invariant | No — change order | — |

## §8. Work-Order State Machine

1. Legal transitions:

```
draft → released → in-progress → review → complete → reconciled
                       ↕
                    blocked ──→ aborted → reconciled
```

2. Recovery arcs from `blocked`: → `in-progress` (block cleared without spec change); → `draft` (change order required; revise, then re-release); → `aborted` (fatal — revert or checkpoint writes, ledger the reason).
3. An `aborted` order reconciles with closeout obligations waived except the ledger entry.
4. `draft → released` and `review → complete` require the order's declared gate (minimum: human at `draft → released`).
5. Any other transition is illegal and fails the exit condition.
6. **v0.1 dispatch is single-threaded.** The `conflicts` field carries no runtime obligation until a dispatcher exists.

## §9. Definition of Done & Routing

1. **Exit condition (three-part, fixed):** all acceptance checks pass ∧ every status transition was legal ∧ closeout obligations fulfilled (status updated, map repaired, ledger appended, evidence and reconciliation blocks filled).
2. Any other result exits nonzero into exactly one of two paths — the **repair loop** (conformance failure: the code is wrong) or a **change order** (spec gap: the spec is wrong). No third path.
3. **Routing rule:** a failed acceptance check whose check is correct → repair loop. A check that is itself wrong, or a need the spec cannot express → change order. All checks pass but the output feels insufficient → record a concern in the reconciliation block and proceed; that judgment belongs to the human gate, not the builder.
4. An acceptance check failing for environment or infrastructure reasons is neither path: block as `infra_block` (a suspension, not an exit) and report via the escalation payload.
5. On nonzero exit the builder emits the structured escalation payload — never unstructured failure prose.

## §10. Commit & Host-Repo Conduct

1. **Conventional Commits** (lowercase), per `/guides/contributing.md` — which remains the canonical git policy for this repository.
2. `main` is read-only for agents. Work happens on feature branches (`type/agent-task-description`); merge via PR.
3. Stage specific files; never `git add -A`.
4. **Validation before staging:** for governed artifacts, the substitute for a test suite is structural — schema validation of frontmatter (one-off manual run in v0.1; see Ledger L-007) and cross-reference integrity.
