# WO-XXXX — *<Title: imperative and outcome-shaped — "Make X possible," not "Work on X">*

> **Artifact class:** Work Order — Template v0.3.0
> **Written by:** Planner tier
> **Amended by:** Change order only, once status = `released` (free edits while `draft`)
> **Read by:** Builder at execution; Gatekeeper at closeout; Human at declared gates
> **Validated by:** `/schemas/work-order.schema.json` (frontmatter) + gate lint (body conventions)

*Authoring convention: italic lines are instructions — replace them with content. Non-italic lines are fixed boilerplate — do not edit. (This is the fixed/discretion rule applied to the template itself.) `!!…!!` tokens are loud placeholders: an order may not transition to `released` while any remain — they fail gate lint.*

## Abstract

*Non-binding. Two or three sentences for the human skimmer: what this order produces, and why now. Doubles as this order's description line on the capability map. On any conflict, the structured sections below win.*

---

```yaml
id: !!WO-XXXX!!          # zero-padded, unique, never reused
title: "!!TITLE!!"       # mirrors the H1
status: draft            # legal: draft → released → in-progress → (blocked) → review → complete → reconciled
                         # recovery arcs from blocked:
                         #   blocked → in-progress  (block cleared without spec change)
                         #   blocked → draft        (change order required; revise, then re-release)
                         #   blocked → aborted      (fatal; revert or checkpoint writes, ledger the reason)
                         # aborted → reconciled     (after rollback; closeout artifact obligations waived except ledger)
clarifications: []       # optional — ambiguities surfaced while drafting; each entry: {question, resolution, status: open|resolved}
                         # an order with any `open` entry should not pass draft → released (§9.1 exit condition)
assignee_tier: builder   # builder | planner | architect — the lowest tier that can complete the work
gate: human              # transitions requiring human sign-off; minimum: draft → released
parent_feature: !!FEATURE-ID!!  # feature id on the capability map; every order has exactly one parent
depends_on: []           # order ids that must reach `complete` first (sequencing)
conflicts: []            # concurrent orders touching the same writes. v0.1 dispatch is single-threaded;
                         # this field is a forward provision for a parallel dispatcher — it carries no
                         # runtime obligation until one exists.
reads: []                # exhaustive, normative — bounds what the builder may RELY ON (§3)
discovery_roots: []      # optional — subtrees the builder may INSPECT for implementation facts (§3)
writes: []               # exhaustive — bounds what this order may mutate (§3)
spawns: []               # orders expected to be cut at closeout (may grow during reconciliation)
```

---

## 1. Goal

*One paragraph. The visible outcome on completion: what works after this order that didn't before. If the goal needs more than a paragraph, the order is too big — split it.*

## 2. Non-Goals

*Enumerate the adjacent work this order must not do — the scope edge, stated positively. Where the excluded work belongs to another order, name it.*

1. *…* *(owned by WO-YYYY)*
2. *…*

## 3. Reads, Discovery & Writes

All three lists are closed-world (fixed).

**Reads** are normative: they bound what the builder may *rely on* for intent, norms, and contracts. Knowledge from outside the reads list that would shape a norm or contract requires a ledger entry before use.

**Discovery roots** are factual: bounded code/doc subtrees the builder may *inspect* to resolve imports, call sites, types, and existing conventions. Discovery may inform mechanical implementation; it may never introduce a norm, contract, requirement, or scope. Discovery that would change intended behavior or an acceptance criterion → ledger entry or escalation, before acting on it.

**Writes** bound what this order may mutate: an unlisted surface is out of bounds.

**Transitive writes (fixed):** an edit inside `writes` that mechanically breaks an unlisted surface (import path, renamed symbol, compiler type error) may be repaired outside `writes` only within these classes:

| Class | Allowed without change order? | Obligation |
|---|---|---|
| `mechanical-reference` — import path fix, callsite rename, generated type reference | Yes | Log in reconciliation block |
| `compile-repair` — type fix required by a declared API change, no behavior change | Yes | Log + evidence |
| `behavioral-adjacent` — caller logic, fallback behavior, new conditionals | No — escalate | — |
| `contract-expanding` — new field, event, route, or invariant | No — change order | — |

***Reads:** the context the builder consumes, by section — point, don't inline.*

***Discovery roots:** the subtrees inspectable for implementation facts; omit the subsection if none.*

***Writes:** restate the frontmatter list with one line each on the nature of the mutation.*

***Flow:** include a sketch (ASCII or Mermaid) whenever the order touches more than one contract surface; at discretion otherwise.*

## 4. Constraints in Force

*Point to binding constitution sections by anchor — never inline them. Then list order-specific prohibitions on **how** the work is done. (Scope exclusions belong in Non-Goals, not here.)*

1. Constitution: *<§ references>*
2. **Do not:** *<conduct prohibitions — e.g., "do not upgrade dependencies," "do not reformat untouched files">*

## 5. Steps

Discretion rule (fixed): ambiguity about a **norm or contract** → ledger entry, stop or flag. Ambiguity about **phrasing or structure** → builder's discretion.

*Numbered, in execution order. Tag every step `(fixed)` or `(at discretion)` — an untagged step fails gate lint. Name the output of any step a later check verifies.*

1. *…* *(fixed)*
2. *…* *(at discretion)*

## 6. Acceptance Checks

*Tag every check with its method: `(diffable)`, `(grep)`, `(lintable)`, `(runnable)`, or `(human gate: <who>)`. Prefer mechanical — an order verified only by human gates is under-specified. Checks verify the Goal, not the Steps.*

- [ ] *…* *(runnable)*
- [ ] **Human gate:** *<who ratifies what, at which transition>*

## 7. Escalation Triggers

Standing triggers (fixed) — stop, set status `blocked`, and emit the escalation payload (§8); do not improvise — if:

1. A step requires mutating a surface outside `writes` beyond the transitive-write classes, or relying on context outside `reads` for a norm or contract.
2. A step requires a framework noun not resolvable by the constitution's glossary procedure.
3. The schema or a contract cannot express something this order needs.
4. An acceptance check fails for environment or infrastructure reasons (network, runner, external service). This is neither conformance failure nor spec gap — block as `infra_block` and report; do not enter the repair loop.

*Order-specific triggers below — the known risk points where improvisation will be most tempting:*

5. *…*

## 8. Closeout

**Artifact obligations (fixed):** file this order under `/work-orders/`; update its status and the capability map; append ledger entries for anything deferred or discovered.

*Order-specific obligations: …*

**Evidence block** *(fixed; filled at closeout — every acceptance check gets a row)*:

```yaml
evidence:
  commit: ""             # git sha or working-tree marker
  checks:
    - id: AC-1
      method: runnable   # runnable | diffable | grep | lintable | human-gate
      command: ""
      result: pass       # pass | fail
      artifact: ""       # optional pointer to a run record or output; keep logs out of the order
```

**Reconciliation block** *(empty until closeout)*:

- Deltas from plan:
- Transitive writes (class · file · reason):
- Follow-ups spawned:
- Ledger entries added:
- Concerns: *(checks pass but something may be underspecified — flag it here and proceed; the human reviews at the next gate. A concern does not block exit.)*

**Escalation payload** *(emitted only on nonzero exit — replaces hand-written failure prose)*:

```yaml
escalation:
  trigger_type: ""       # constraint_violation | check_failure | spec_contradiction | infra_block
  violating_step: 0
  failed_assertion: ""
  diagnostic_snippet: ""
  suggested_action: ""
```

**Routing rule (fixed):** a failed acceptance check whose check is correct → repair loop (conformance failure). A check that is itself wrong, or a need the spec cannot express → change order (spec gap). All checks pass but the output feels insufficient → record a concern and proceed to closeout; that judgment belongs to the human gate, not the builder.

**Exit condition (fixed, three-part):** all acceptance checks pass ∧ every status transition was legal ∧ closeout obligations fulfilled. Any other result exits nonzero into exactly one of two paths: the repair loop (conformance failure) or a change order (spec gap). No third path. (`infra_block` is a suspension, not an exit.)
