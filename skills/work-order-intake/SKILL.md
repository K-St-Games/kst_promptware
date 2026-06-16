---
name: work-order-intake
description: Convert a feature request or task description into a draft Boresight work order, ready for human gate review. Invoke when you have a concrete task to formalize into the work-order system.
profile: full
maturity: canonical
origin: kst_promptware
---

# Work Order Intake

Converts product intent into a machine-checkable, bounded work order. The output is a filled `templates/work-order.md` with no `!!…!!` tokens, ready for the human gate at `draft → released`.

## Prerequisites

This skill requires the Boresight Full profile. Read before running:
- `charter.md` — glossary (noun budget applies here)
- `constitution.md` §§5 (noun budget), 7 (closed-world reads/writes), 8 (state machine), 9 (definition of done)
- `capability-map.md` — current feature inventory and WO queue

## Procedure

**Step 1 — Understand the intent**

Ask until you have:
- **Goal:** What capability does this work order produce?
- **Non-goals:** What is explicitly NOT in scope? (At least 2)
- **Parent feature:** Which feature on the capability map does this advance? If none, propose a new feature entry.
- **Who executes:** Which tier is the assignee (builder, planner, architect)?

**Step 2 — Enumerate reads and discovery roots**

Reads are exhaustive and normative — the builder may only rely on context from these files to make decisions about norms or contracts.

- What documents does the builder need to understand the intent? (specs, prior decisions, ADRs)
- What existing code must the builder understand? (discovery roots — factual inspection only, no norm extraction)

**Step 3 — Enumerate writes**

Writes are exhaustive — if a file isn't listed, this order cannot create or mutate it.

- What files will be created?
- What files will be modified?
- What transitive writes might be forced? (e.g., reference updates when a file is renamed)

**Step 4 — Write acceptance checks**

Every AC must be:
- **Observable** — can be checked by running a command or reading output
- **Mechanical where possible** — prefer `grep`, `runnable`, `diffable`, `lintable` over `human-gate`
- **Complete** — covers failure paths and edge cases, not just the happy path

Target 5–10 ACs. More than 15 suggests the work order is too large.

**Step 5 — Identify escalation triggers**

What situations should cause the builder to stop and emit an escalation payload rather than improvise?
- Surfaces outside `writes:` that would need to be touched
- Framework nouns not in the charter glossary
- Discovery findings that would change the intended behavior

**Step 6 — Draft the work order**

Fill `templates/work-order.md`. Required fields:
- `id`: next available WO-XXXX (check `work-orders/` for the current max)
- `status`: `draft`
- `assignee_tier`: the lowest tier that can complete the work
- `gate`: `human` minimum at `draft → released`; add `review → complete` gate if the work is high-risk
- `parent_feature`: the feature id from the capability map
- `reads`, `writes`: the exhaustive lists from Steps 2–3

**Step 7 — Present for human gate**

Present the draft work order and ask the human to:
1. Confirm the reads and writes lists are complete
2. Confirm the acceptance checks are verifiable
3. Approve to transition from `draft` to `released`

Do not transition status until the human approves. Do not write the work order file until the human confirms the draft is correct.

## Quality Bar

A good work order passes this check: could a new agent cold-start, read only the files in `reads:`, and complete the work order successfully — without asking any questions not covered by the escalation triggers?

If the answer is no, the work order is underspecified.
