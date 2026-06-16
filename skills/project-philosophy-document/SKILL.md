---
name: project-philosophy-document
description: Draft a project philosophy document — the 'why before specs' foundation that governs future decisions. Invoke at project inception or when a project has drifted from its original intent and needs reorientation.
profile: standard
maturity: canonical
origin: kst_promptware
---

# Project Philosophy Document

Captures the core intent, values, and non-goals of a project before the specification work begins. This document answers "why does this project exist and what principles govern every decision within it?" — the layer above the spec.

## When to Invoke

- At project inception, before writing any specs or work orders
- When a project has accumulated scope creep and needs to be reoriented
- When the team is arguing about a decision that should be resolvable by referring back to first principles
- When onboarding a new agent or human who needs to understand the "why" quickly

## Procedure

**Step 1 — Interview the human (5 questions)**

Ask these questions; do not proceed until you have answers:

1. **Who is this for?** Describe the primary user in one sentence.
2. **What is the one problem this solves?** The core problem, not the feature list.
3. **What would success look like in 6 months?** A concrete, observable outcome.
4. **What are you explicitly NOT building?** List at least 3 things this project will refuse to become.
5. **What principle would you never violate, even under pressure?** (e.g., "we never sacrifice user data privacy for growth")

**Step 2 — Draft the document**

```markdown
# <Project Name> — Philosophy

> One-sentence summary: what it is and why it matters.

## Who It's For

<Primary user description. Be specific — "developers who deploy to production daily" not "developers".>

## The Core Problem

<The specific pain this exists to solve. Not a feature description — the underlying need.>

## Success Looks Like

In 6 months, success is observable as:
- <Concrete outcome 1>
- <Concrete outcome 2>
- <Concrete outcome 3>

## Governing Principles

These principles resolve disputes. When two options are otherwise equal, the option that better satisfies these principles wins.

1. **<Principle>:** <One sentence explaining it and what it rules out>
2. **<Principle>:** ...
3. **<Principle>:** ...

## Explicit Non-Goals

This project will not:
- <Non-goal 1>
- <Non-goal 2>
- <Non-goal 3>

*(If you find yourself building one of these, stop and ask whether the philosophy has changed or the scope has drifted.)*

## What We'd Sacrifice

If forced to choose: <The trade-off this project makes deliberately. e.g., "We sacrifice setup simplicity for operational simplicity.">
```

**Step 3 — Pressure-test**

Before finalizing: read each principle back against the stated non-goals. If a principle would also justify building a non-goal, it's either too broad or the non-goal isn't stated precisely enough. Revise until they're consistent.

**Step 4 — File**

- Recommended location: `docs/philosophy.md` or `references/philosophy.md`
- This document changes rarely. When it does, the change is architectural — log a ledger entry or ADR.

## Quality Bar

- Every principle must rule something out. A principle that permits everything is not a principle.
- Non-goals must be things someone might actually want to build — not obviously out-of-scope things.
- The document should be readable in under 5 minutes. If it's longer than one page, cut.
