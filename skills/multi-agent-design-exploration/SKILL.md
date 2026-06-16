---
name: multi-agent-design-exploration
description: Explore a significant design decision by generating multiple distinct stances in parallel, then synthesizing a recommendation. Use when you want adversarial analysis of a technical choice before committing.
profile: standard
maturity: canonical
origin: kst_promptware
---

# Multi-Agent Design Exploration

Structures a design decision as a small deliberation — multiple stances explored, then synthesized — so the human gets adversarial analysis rather than the first plausible answer.

## When to Invoke

- Major technical choices with significant lock-in (language, framework, database, architecture pattern)
- Decisions where you suspect the first answer might not be the best answer
- When the human has a preference but wants it stress-tested
- When multiple stakeholders have conflicting intuitions

## Procedure

**Step 1 — Frame the question**

Confirm with the human:
- What is the decision, stated precisely?
- What are the constraints? (scale, team size, timeline, non-negotiables)
- What criteria matter most? (correctness, speed, simplicity, cost, reversibility)
- Are there options already on the table, or should the exploration generate them?

**Step 2 — Generate N stances**

Produce 2–4 distinct stances. Each stance advocates for one option as if making the strongest possible case for it. Stances should be genuinely different — not variations on the same approach.

For each stance, write:

```markdown
### Stance: <Option Name>

**Advocate's case:**
<3–5 bullet points making the strongest case for this option>

**Key assumptions:**
<What must be true for this option to be the right choice>

**Where this stance is weakest:**
<The honest critique of this option, even from the advocate's perspective>
```

**Step 3 — Cross-examine**

For each stance: what does Stance B say about Stance A's weaknesses? Surface the real tensions between options.

**Step 4 — Synthesize**

```markdown
## Synthesis

**Recommendation:** <Option>

**Why:** <2–3 sentences connecting the recommendation to the stated criteria and constraints>

**The live tension:** <The most legitimate objection to this recommendation and how to mitigate it>

**Conditions that would change the recommendation:**
- If X, prefer <alternative> instead
- If Y, reconsider because...

**Proposed next step:** <One concrete action>
```

**Step 5 — Record if significant**

If the decision is significant enough to warrant an ADR (will affect multiple components, is hard to reverse, would surprise a new team member), offer to run `/architecture-decision-records` to document the outcome.

## Quality Bar

- Every stance must include its own weakest point. An advocate who can't name a weakness is not a useful advocate.
- The synthesis must name a winner, not hedge with "it depends." If it genuinely depends on an unresolved question, name the question and ask for a decision.
- Stances must be meaningfully distinct. "Option A with slightly different tradeoffs" is not a separate stance.
