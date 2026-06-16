# Ledger

> **Artifact class:** Ledger (memory — append-only)
> **Written by:** Any tier, at closeout or on discovery
> **Amended by:** Append-only; entries change status (open → resolved → archived) but bodies are never rewritten
> **Read by:** All tiers, before execution — this is the "do not invent answers to these" list
> **Validated by:** Gate lint (entry ids sequential; statuses legal)

Active-section budget: 50 lines of open entries. Resolved entries archive to `/decisions/resolved-ledger.md` when the budget is crossed (trigger-based, not ritual).

## Active Entries

- **L-001** · `resolved 2026-06-12` — **Bootstrap paradox.** WO-0001 predates the schema and capability map that govern it. Resolved: AC-3 passed retroactively (WO-0001 frontmatter validates against `schemas/work-order.schema.json`; evidence in `/work-orders/WO-0001.md`).
- **L-002** · `resolved (partial — release name still open)` — **Project name.** "Boresight" ratified 2026-06-12 as production codename only; codename-neutrality constraint (Constitution §1.3) binds. Public release name deferred. Candidates: Keel, Jig, codejig — also candidates as module names.
- **L-003** · `open` — **Status derivation.** Generated vs. hand-maintained capability map deferred past v0.1. Hand-maintained for now; status canon (Constitution §3) still binds.
- **L-004** · `open` — **Model-tier routing config format.** Tier names stay abstract in v0.1; concrete model pins live in harness adapters, never in work orders.
- **L-005** · `open` — **Role-filtered "compiled" context views.** Deferred; section structure serves as the role filter for now.
- **L-006** · `open` — **Adapter auto-generation (`factory sync`).** Deferred to v0.2; WO-0007 ships hand-authored shim templates.
- **L-007** · `open` — **Gatekeeper validator deferred** to a later maturity tier, against two peer-review recommendations (htf-coder §4, codex §2.7) — deliberate decision 2026-06-12. v0.1 schema validation is a one-off manual run recorded in evidence blocks. Revisit when work-order volume or parallel work justifies standing automation.
- **L-008** · `open` — **v3 session-layer integration.** CONTEXT/PROGRESS/HANDOFF/WORKFLOW remain a companion layer. Full integration (run-records, playbook conversion, `/templates` reorg) deferred to a later development stage; until then `/templates` is shared (Constitution §4.2).
- **L-009** · `open` — **Deferred template features** per codex review: Semantic Versioning of Intent, cost-estimation fields, specialized-reviewer routing (`review_requirements`), hermeticity constraints beyond the `infra_block` trigger. Revisit with real run-record data.
- **L-010** · `open` — **New artifact classes (Rule, Skill, Agent, Profile) pending charter enumeration.** The charter's fixed artifact-class list does not include these four classes (created 2026-06-16 as part of the library layer). Governance headers for these artifacts use nearest-fit existing classes with a parenthetical note. Charter amendment via change order required to make these classes official. Blocked on AC-12 (WO-0001 human gate) before charter can be amended.
- **L-011** · `open` — **Work-order coverage gap for library layer (F-008–F-013).** The promptware library layer (rules, agents, skills, profiles, schemas) was created without governing work orders. Constitution §8 requires work orders for governed artifacts. Gap noted; options: (a) retroactive work orders, (b) change order formalizing a "lightweight path" for library additions. Revisit when library stabilizes.
- **L-012** · `open` — **Model pinning in agent frontmatter.** Agent definitions in `agents/` intentionally omit `model:` field, consistent with L-004 (model pins belong in harness adapters). The `model` field is available in `schemas/agent.schema.json` for downstream consumers to populate. Revisit if Claude Code's default model resolution behavior changes.
- **L-013** · `open` — **Language rule stubs are unpopulated.** `rules/typescript/README.md`, `rules/python/README.md`, `rules/go/README.md` are valid stubs but contain placeholder sections rather than authoritative conventions. Each downstream project should extend them with project-specific content. A future work order should decide whether kst_promptware ships opinionated defaults or only structure for these stubs.
