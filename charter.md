# Charter

> **Artifact class:** Charter (north star)
> **Written by:** Planner tier (bootstrap intake, WO-0001)
> **Amended by:** Human approval only — vision-level changes are gated by Damien
> **Read by:** All tiers, at intake and whenever an order's intent is ambiguous
> **Validated by:** Human gate (no schema in v0.1); gate lint for governance header

## Vision

A specification-first software factory: a portable artifact bundle that converts ambiguous product intent into verified software through a sequence of increasingly concrete, machine-checkable artifacts.

> CI/CD automated everything downstream of the commit. This automates everything upstream of it.

The product is not code. It is **code plus the artifact trail** — the durable record of what was decided, built, verified, changed, and deferred. The more non-deterministic the worker, the more explicit the process wrapper must be: schemas and acceptance checks are the exit codes that make agent work compose.

This framework is **v4** of this repository's lineage. v3 (the PM/IC/QA session framework in `/templates`) remains operational as a **companion layer** — the session-execution discipline inside a work order's `in-progress` state.

## Users

1. **Solo developers and small teams** using AI agents for day-to-day implementation, who need bounded delegation without losing the decision trail.
2. **Agent harnesses** — any runtime that reads markdown. The bundle is runtime-agnostic; harnesses learn the rules from the artifacts themselves.
3. **The human sponsor** (Damien), who gates vision-level changes and ratifies north-star artifacts.

## Problem

**Specification debt:** messiness in intent — undefined roles, vague boundaries, missing acceptance criteria, inconsistent terminology. Agentic systems suffer badly from it because models fill gaps confidently. Chat-based development produces code and loses the reasoning; harnesses ship features and discard the contract that justified them. Reducing specification debt before code is written — and preserving the trail after — is the purpose of this framework.

## Success Criteria

1. A bounded work order can be specified, executed, verified, and reconciled using only the bundle's artifacts — no runtime dependency.
2. Every completed order leaves a durable trail: evidence block, reconciliation block, ledger deltas.
3. The two-path exit holds in practice: every nonzero exit routes to the repair loop or a change order — no silent improvisation.
4. A new agent cold-starts from `AGENTS.md` → constitution → active work order, each one screen or less of pointer-following.
5. The framework installs into an existing repository without reorganizing it (proven by this repo's own brownfield bootstrap).

## Non-Goals

1. **Not an agent runtime.** No scheduler, dispatcher, model router, or cost estimator. Runtimes are adapters around the bundle; the bundle stays valid without one.
2. **Not a project-management system.** Work-order state is local artifact lifecycle. Delivery state (approvals, merges, tracker items) belongs to external orchestration and is mapped, never absorbed.
3. **Not a prompt library or rules file.** The differentiation is process — freeze ordering, mutation rights, and escalation semantics. A good `AGENTS.md` is a byproduct, not the product.
4. **Not a one-prompt promise.** Initialization is one *conversation*, not one prompt. The factory's value is in the loop, not a magic incantation.
5. **No parallel dispatch in v0.1.** Execution is single-threaded; the `conflicts` field is a forward provision for a dispatcher that does not yet exist.

## Name

**"Boresight" is the production codename** (the *Blue Harvest* convention) — it may appear in prose, READMEs, and the changelog, but never in machine-readable surfaces (schema `$id`s, field names, canonical directory names). The public release name is deferred; candidates logged in Ledger L-002: Keel, Jig, codejig — also candidates as module names.

## License

MIT. Copyright holder: **Damien O'Donnell**. See `/LICENSE`.

## Glossary

Binding noun set — the noun budget (Constitution §5) admits new framework nouns only through this glossary via change order, or the ledger as deferred terminology.

| Noun | Definition |
|---|---|
| **Charter** | This document. Vision, users, problem, success criteria, explicit non-goals. |
| **Constitution** | Norms: format rules, mutation rights, state machine, definition of done, routing rules. |
| **Capability map** | Capabilities → features inventory; the single status surface. A *derived index* — work-order frontmatter is canonical. |
| **Contract** | A stable agreement between parts of a system (API, data, event, component). Lets cheaper tiers implement against boundaries designed by smarter ones. |
| **Feature spec** | Behavior, mechanically checkable acceptance criteria, touched contracts. Slices are sections within it. |
| **Slice** | A small, user-meaningful, verifiable increment — a section of a feature, not its own file. |
| **Work order** | The execution-ready, bounded unit handed to a builder: closed-world reads/discovery/writes, tagged steps, acceptance checks, three-part exit. |
| **Change order** | The negotiated delta when a released plan meets reality that disagrees. The institutional form of a spec or contract amendment. |
| **Ledger** | Append-only record of open questions, deferred decisions, and known issues — the "do not invent answers to these" list. |
| **Discovery roots** | Subtrees a builder may *inspect* for implementation facts. Never a source of norms, contracts, requirements, or scope. |
| **Transitive write** | A mechanical repair outside `writes` forced by an edit inside it; class-bounded and logged (Constitution §7). |
| **Evidence** | The closeout record of what was run, against what revision, with what result — per acceptance check. |
| **Escalation payload** | The structured block a builder emits on nonzero exit, in place of failure prose. |
| **Specification debt** | Messiness in intent, as technical debt is messiness in code. |
| **Tier** | A mutation-rights class, not a task type: builder, planner, architect, human. Gatekeeper is a validation role. |

**Artifact classes (enumeration, fixed):** Charter, Constitution, Capability Map, Feature Spec, Work Order, Template, Schema, Ledger, ADR, Playbook, Command, Change Order.
