---
name: architecture-decision-records
description: Capture an architectural decision as a lightweight ADR document. Invoke when choosing between significant alternatives — frameworks, databases, patterns, API shapes — or when revisiting a past decision.
profile: standard
maturity: canonical
origin: kst_promptware
---

# Architecture Decision Records

Creates structured ADR documents that survive the original conversation.

## When This Skill Fires

Invoke explicitly (`/architecture-decision-records`) or when the conversation includes:
- Choosing between significant technical alternatives
- "We decided to..." language about something architectural
- A question like "why did we choose X?"
- Any decision that would surprise a new team member six months from now

## Procedure

**Step 1 — Identify the decision**

Ask (or infer from context): What is the decision being made? What is the context that makes this decision necessary? What constraints apply (time, budget, existing stack, team skill)?

**Step 2 — Enumerate alternatives**

List at least two alternatives. For each:
- What it enables
- What it forecloses (what becomes harder or impossible)
- What it costs to undo

**Step 3 — Confirm the chosen option**

If the human hasn't stated a choice, present the alternatives with a recommendation and ask for a decision before writing. Do not write an ADR for a decision that hasn't been made.

**Step 4 — Draft the ADR**

Use the Nygard lightweight format:

```markdown
# ADR-XXXX: <Title — the decision in noun form>

**Date:** YYYY-MM-DD
**Status:** Accepted

## Context

<2–4 sentences: the situation that makes this decision necessary and the constraints in play>

## Decision

<1–3 sentences: what we decided and why>

## Alternatives Considered

- **<Option A>:** <what it enables> / <what it costs>
- **<Option B>:** <what it enables> / <what it costs>

## Consequences

**Positive:**
- ...

**Negative / Risks:**
- ...

**Follow-up work:**
- ...
```

Keep it under one page. The goal is "readable in 2 minutes."

**Step 5 — File the ADR**

1. Check whether `docs/adr/` or `decisions/` exists in the project. Use whichever exists; create `docs/adr/` if neither exists.
2. Number sequentially: check existing files for the next number.
3. Present the draft to the human for approval before writing.
4. On approval: write the file and, if an index (`docs/adr/README.md` or `decisions/README.md`) exists, add a one-line entry to it.

## Quality Bar

- Every ADR must name the context and constraints — a decision without context is not an ADR, it's a fact.
- Every alternative must include what it forecloses, not just what it enables.
- Consequences section must include at least one negative consequence or risk. If you can't name one, you haven't thought hard enough.
