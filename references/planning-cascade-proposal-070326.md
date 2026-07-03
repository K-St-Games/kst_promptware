# Planning Docs Toolkit

**Version:** 0.2  
**Date:** 2026-07-03  
**Status:** Proposal — not canonical governance. Adoption is per-repo and optional.

This document describes a small set of planning-doc patterns that have worked across agent-assisted repos. It is a **toolkit**, not a framework. Pick what fits; ignore the rest.

---

## Principles

1. **Per-repo autonomy.** Each repo chooses which doc levels to use, where files live, and what naming convention to follow. Nothing here overrides a repo's existing `AGENTS.md`, startup protocol, or decision log.

2. **Planning docs serve work, not the other way around.** Write a doc when it prevents rework, lost context, or unauthorized scope creep. Skip levels that add no value for the current task.

3. **Close the loop.** Every plan that authorizes work should say how you know it is done and what evidence to leave behind.

4. **Do not redo.** If the work already exists, reference it. Merge or extend; do not rewrite from scratch.

---

## Doc levels (use 3–4, not all)

Most repos need at most four levels. Treat the list below as a menu, not a ladder you must climb.

| Level | Typical name | Purpose | Typical lifetime |
|---|---|---|---|
| **Vision** | `VISION.md`, roadmap memo, trajectory doc | Why this repo exists; north-star outcomes; what is explicitly out of scope | Months–years; revise rarely |
| **Phase / Release Plan** | Phase plan, release pack, work-order README | Scoped chunk of the vision with prerequisites, gates, and disposition | Weeks–months; one active phase at a time |
| **Session / Weekly Plan** | Session plan, weekly note | What this session or week will attempt; exit criteria; blockers | Hours–days; discard or archive after close-out |
| **Spec / PRD / Work Order** | WO-*, PRD, design note | Single deliverable or inspection brief; one reviewed PR per work order | Days–weeks; close when PR merges or item is explicitly deferred |

**Vision** and **Phase** are optional for small repos. **Session** and **Spec** are where most day-to-day planning happens.

### Loose linking (optional)

If a doc has a natural parent, a one-line reference helps agents and humans find context:

```markdown
**Serves:** docs/work-orders/README.md (Phase 1 pack)
```

or

```markdown
**Parent:** VISION.md § Runtime evolution
```

This is a **convention**, not a validator rule. Missing `Serves:` is fine. Deep parent chains are usually a smell — if you need more than one hop, the phase plan may be doing too much.

### Size guidelines (not budgets)

Long planning docs stop getting read. Rough targets:

| Level | Guideline |
|---|---|
| Vision | ≤ ~200 lines; link out for detail |
| Phase / Release Plan | ≤ ~150 lines; table of work orders + gates |
| Session / Weekly Plan | ≤ ~80 lines; 1–3 goals max |
| Medium spec | ≤ ~120 lines |
| Heavy spec | ≤ ~250 lines; escalate only when complexity warrants |

These are **guidelines**. A 300-line vision that earns every line is better than a 50-line stub that omits exit criteria. Prefer splitting over stuffing.

---

## Cross-cutting fields

Use these sections wherever they add clarity. Not every doc needs every field.

### Exit criteria

How you know the item is **done** — observable, checkable, not vibes.

```markdown
## Exit criteria
- [ ] PR merged with linked work order
- [ ] `python -m pytest tests/` green on changed paths
- [ ] CHANGELOG entry added
```

### Close-out evidence

What to leave behind when closing a session, phase, or work order:

- Updated checkpoint or `CURRENT_STATE.md` (if the repo uses one)
- CHANGELOG or decision-log row
- Artifact path (memo, checklist, test results)
- Explicit "deferred" or "blocked" note with owner

### Do not start

Work that looks related but is **not authorized** by this doc. Stops scope creep and agent overreach.

```markdown
## Do not start
- HTF production cutover
- Schema migration on existing `state/` files
- Renaming existing docs directories
```

### Already done / do not redo

Pointers to completed work so the next session does not rebuild it.

```markdown
## Already done / do not redo
- L0 cartridge loader (`cartridge_loader.py`) — extend, do not rewrite
- PR #9 memory-scope amendment — do not re-litigate envelope semantics
```

---

## Two spec weights

Most implementation or inspection work fits one of two templates (see `planning-docs-toolkit/templates/`).

| Weight | When to use | Escalate to heavy when |
|---|---|---|
| **Medium spec** | Single module or doc change; clear files; inspection → memo | Touches >3 subsystems, approval boundaries, schema, deploy, or external actions |
| **Heavy spec** | Cross-cutting change; multiple approval gates; operational cutover | — |

**Escalation rule:** If you are unsure, start medium. Add sections (approval gates, verification matrix, risk table) when the medium template feels cramped — that is the signal to escalate, not a separate doc level.

---

## Session / weekly planning discipline

Before a non-trivial work block:

1. Read the repo's startup protocol and current-state doc (whatever the repo calls it).
2. Open or skim the active phase plan, if one exists.
3. Write a **session plan** (template: `planning-docs-toolkit/templates/session-plan.md`) with 1–3 goals and exit criteria.
4. At session end, record close-out evidence and update the checkpoint if the repo uses one.

Weekly: roll up open session plans into a short weekly note only if multiple sessions share one phase goal. Otherwise, session plans are enough.

---

## Optional: `STATE.md` (experimental)

Some repos may want a single **live operational snapshot** file distinct from vision or phase planning — e.g. `STATE.md` or the repo's existing `CURRENT_STATE.md`.

- **Optional.** Do not introduce `STATE.md` where the repo already has a working equivalent.
- **Experimental.** No workspace-wide requirement; no agent-os enforcement.
- **Not a planning level.** It is runtime/orientation truth, not a plan. Phase and session docs may *reference* it; they do not replace it.

---

## Optional: workspace note for cross-repo vision

Repos that share a product vision (e.g. Crowbot + agent-os + a deploy host) may keep a **workspace-level** note outside any single repo:

- One file or short index, not a sixth planning level
- Links to each repo's own vision/phase docs
- No authority over per-repo `AGENTS.md` or approval boundaries

Example placement: `C:\Lonnie\agent-os\docs\workspace-vision-note.md` (create only if useful; not required by this toolkit).

---

## Directory taxonomy (descriptive, not prescriptive)

Repos already organize docs differently. Common patterns:

| Pattern | Example paths | Fits |
|---|---|---|
| Work orders under `docs/` | `docs/work-orders/WO-*.md` | Crowbot-style phase packs |
| Architecture + operations split | `docs/architecture/`, `docs/operations/` | Long-lived contracts |
| References / proposals | `references/` | Non-canonical proposals like this file |
| Evolution / roadmap | `docs/evolution/` | Disposition tables, phase rationale |

**Do not rename existing docs directories** to match a taxonomy. New docs should follow the repo's existing layout.

---

## What this toolkit is not

- Not canonical governance for any repo or for agent-os
- Not a requirement to add files, levels, or `STATE.md`
- Not a compaction or context-recovery guarantee — agents still read the repo's real sources of truth
- Not a substitute for `AGENTS.md`, decision logs, or approval boundaries already in the repo

---

## Templates and migration

| Artifact | Path |
|---|---|
| Session plan template | `planning-docs-toolkit/templates/session-plan.md` |
| Medium spec template | `planning-docs-toolkit/templates/medium-spec.md` |
| Heavy spec template | `planning-docs-toolkit/templates/heavy-spec.md` |
| Migration note | `planning-docs-toolkit-migration.md` |

---

## Appendix A — Boresight isomorphism (optional reference)

Some teams map planning levels to a "boresight" metaphor (aim → trajectory → firing solution). This table is **historical context only**; the toolkit does not require Boresight terminology.

| Boresight term | Toolkit level |
|---|---|
| Strategic aim | Vision |
| Engagement trajectory | Phase / Release Plan |
| Firing solution | Session / Weekly Plan |
| Round / shot | Spec / PRD / Work Order |

Use plain names (Vision, Phase, Session, Spec) in new docs unless the repo already standardizes on Boresight vocabulary.

---

## Appendix B — Open questions for v0.3

- Whether any repo wants a shared workspace vision index (and who maintains it)
- Whether medium/heavy templates need a third "inspection-only" stub lighter than medium
- Concrete before/after examples of session close-out in Crowbot (to ground compaction claims in evidence)
