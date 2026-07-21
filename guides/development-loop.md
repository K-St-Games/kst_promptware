# Development Loop

**Status:** Practical reference
**Date:** 2026-06-15
**Source:** Observed workflow across projects
**Also available in:** `K_St_Engineering/_docs/agents/Development_Loop.md`
**Formal treatment:** [`projects/K_St_Engineering/_docs/incubator/boresight/project-oneshot-overview.md` §8]
(../K_St_Engineering/_docs/incubator/boresight/project-oneshot-overview.md) — shape → specify → plan → execute → verify → triage → integrate

---

## What This Is

A plain-language description of the iterative development loop used across our projects. Not a template, not a mandate — a shared reference so agents and humans can orient to the same workflow. Each project repo right-sizes its own version from the materials in `AI_Agent_Dev_Docs/` and `K_St_Engineering/_docs/agents/`.

---

## The Loop (Practical Version)

```
Idea → Spec → PM Vet → IC Build → Review → Test → Feedback → Re-implement → Done
                                                      ↻
```

### 1. Idea & Spec

A team member (or agent) has an idea for a product or feature. They work with an agent to develop a **spec document** that captures:

- What needs to be built and why
- Acceptance criteria or desired behavior
- Constraints, non-goals, known edge cases

This spec becomes the shared artifact — the thing everyone works from.

**Output:** A spec document (could be a markdown file, a work order, a feature spec, etc.)

### 2. PM Review

The spec is escalated to a PM agent (or a PM-perspective review) for feedback. The PM checks for:

- Clarity and completeness
- Feasibility
- Missing edge cases or scope gaps
- Alignment with project goals

Feedback is typically appended to or reflected in the spec document. The spec author and PM may iterate here until the spec is solid enough to hand off for implementation.

**Output:** Reviewed spec with PM sign-off (or iterative revisions until ready)

### 3. IC Implementation

The spec goes to an implementation agent (IC). The IC builds exactly what the spec describes, working within any constraints or conventions the project defines.

**Expected behavior:**
- Read the spec and project context first
- Make scoped changes tied to the spec
- Prefer established project patterns
- Add tests where risk justifies it
- Record surprises or questions that surfaced during implementation

**Output:** Implemented code + any handoff notes

### 4. PM Review & Browser Test

The implementation is escalated back to the PM (or a QA perspective) for review. The PM checks:

- Does the implementation match the spec intent?
- Are there obvious gaps or issues?
- **Browser test:** For UI work, someone actually loads the page and verifies behavior visually. (This is the step that catches things static review misses.)

**Output:** Review findings — what passes, what needs changes

### 5. Feedback → Re-implement

Review feedback goes back to the IC agent (or the next IC cycle). The IC implements the changes, and the test/feedback/implement loop repeats until the feature is complete.

**Output:** Iterated feature, verified working

### 6. Feature Complete

Each feature goes through this loop. A project is the collection of multiple implemented features. Features may be independent or sequenced.

---

## How This Maps to Existing Docs

| This doc | Maps to |
|----------|---------|
| Idea → Spec | Human alignment pass + spec artifact |
| PM Vet | PM perspective (WORKFLOW.md) / Front-load human alignment (Workflow Pattern v0) |
| IC Build | IC perspective (WORKFLOW.md) / Agent execution (Workflow Pattern v0) |
| Review & Test | QA perspective (WORKFLOW.md) / Specialized agent review (Workflow Pattern v0) |
| Feedback loop | Repair loop (Boresight) — conformance failure |
| Spec revisions | Change order (Boresight) — spec gap |

---

## Related Docs in This Repo

- [`templates/WORKFLOW.md`](../templates/WORKFLOW.md) — PM/IC/QA session protocol, cadence, compaction
- [`templates/HANDOFF.md`](../templates/HANDOFF.md) — handoff template for session transitions
- [`templates/work-order.md`](../templates/work-order.md) — bounded work order format (Boresight)
- [`charter.md`](../charter.md) — Boresight charter and glossary
- [`constitution.md`](../constitution.md) — Boresight governing norms

Includes all the ceremony options — lightweight track, full loop, work orders — so each project can pick what fits.

---

## Choosing Your Level of Ceremony

Not every task needs the full loop. Guidelines from existing docs:

| Signal | What to use |
|--------|-------------|
| Typo, config tweak, trivial fix | Lightweight track (single IC pass, no handoff) |
| Small feature, single file change | IC + QA / Skip PM review |
| Multi-file feature, schema change, new behavior | Full loop (PM → IC → QA) |
| Ambiguous or speculative work | Front-load human alignment before any agent work |

Adapt per project. The loop is a pattern, not a prison.