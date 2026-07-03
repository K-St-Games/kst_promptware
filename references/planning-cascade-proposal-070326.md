# Proposal: The Planning Cascade — Solo Workflow Standard v2

> **Filed:** 2026-07-03
> **Author:** Claude Code review session, commissioned by Doomien
> **Status:** Draft for review — extends and revises `solo-profile-proposal-070326.md` (v1)
> **Scope:** The standard documentation set for a solo developer's *product* repos — multiple agents, multiple harnesses, frequent context compaction, occasional guest reviewers
> **Source:** Five sample docs from the SketchPad workspace (VISION, platform tiers, Phase 0.5 plan, editor improvement plan, 2026-06-29 weekly objectives) + design answers ratified 2026-07-03: **per-repo now / promote to workspace later** · **dated weekly plans with close-out** · **two spec weights with stated criteria**

---

## 0. What Changed From v1, and Why

The v1 Solo Baseline assumed the durable layer was one file (PROJECT.md) and the transactional layer was one file (STATE.md). The maintainer's actual working stack is richer — and, examined closely, it is a coherent **planning cascade** that has independently re-derived most of what the Boresight work-order system is for, at lower ceremony:

| Boresight concept | What the SketchPad stack already does instead |
|---|---|
| Work order with acceptance checks | Phase-plan **slices with exit criteria** and files-to-touch |
| Evidence block | Weekly **close-out table** with SHAs, test counts, artifacts |
| Ledger ("do not invent answers") | **Owner Questions** table with blocks-column |
| Decisions / change-order trail | **"Maintainer decisions already absorbed (do not re-litigate)"** table |
| Scope bounds (`writes:`) | **Working constraints** ("tools/ only") and **"Do not start"** lists |
| Gate | **Entry gate** lines and human-required slice markers |

The revision therefore does not add a governance system — it **names the cascade, standardizes the headers and sections that make it navigable to agents, and adds the one missing mechanism: an alignment chain that survives context compaction.** The two stated pains this version targets:

1. **Compaction is common** → a ~30-line pointer card (`STATE.md`) that is cheap enough to re-read after *every* compaction, carrying the active slice's exit criterion verbatim.
2. **Handoffs between agents need detailed steering** → the weekly plan's steering sections (entry gates, session order, already-done, do-not-start) become *required*, not stylistic.

Everything in v1 that isn't restated here survives unchanged: `CONVENTIONS.md`, `REVIEWS.md` and the guest lane, the harness shims (`CLAUDE.md` → `@AGENTS.md`), the optional `.claude/settings.json` deny list, and `PROJECT.md` (one amendment in §5.7).

---

## 1. The Cascade

Six altitudes. Each answers one question, at one time horizon, within one budget. Every doc below Vision declares which parent it serves.

| # | Altitude | Answers | Horizon | Location | Cadence | Budget |
|---|---|---|---|---|---|---|
| 1 | **Vision** | Why does this exist; what must never break | Years | `docs/vision/VISION.md` | Revisit at inflection points only | ≤ 200 lines |
| 2 | **Tiers** | In what order does the long game get built | Quarters | `docs/vision/tiers.md` | Per tier boundary | ≤ 200 lines |
| 3 | **Phase / release roadmap** | What does the next release require, and what proves it | Weeks–months | `docs/roadmaps/<phase>.md` | One **live** at a time | ≤ 350 lines |
| 4 | **Weekly plan** | What is this week for, in what order, and what is parked | Week | `docs/plans/YYYY-MM-DD-weekly.md` | Dated file per week, closed out | ≤ 120 lines |
| 5 | **Spec / PRD** | How one feature or module gets built | Per feature | `docs/specs/<name>.md` | As needed; two weights (§5.5) | light ≤ 150 / heavy ≤ 350 |
| 6 | **State** | Where are we *right now*; what is the very next action | Hours | `docs/STATE.md` | Every session; after every compaction | ≤ 30 lines |

**Reading rules (fixed):** an agent reads 6 every session and after every compaction; 4 when picking up or planning work; 5 when executing a specced feature; 3 when a scope question arises; 1–2 only when direction itself is in question. Nothing above the agent's current altitude is required reading for routine work — the `Serves:` chain (§2) is what keeps low-altitude work aligned without loading high-altitude context.

**Workspace note (ratified: per-repo now, promote later):** vision-tier docs whose natural scope exceeds the repo carry a scope-note line — *"Natural home: workspace root (`K_St_Games/_docs`); lives here while active work is in this repo; promote when the workspace is formalized."* Until promotion, each repo is self-contained: agents never need to resolve `../_docs/` paths. When two repos need the same vision doc, mirror it with a pointer banner naming the canonical copy.

---

## 2. The Alignment Chain

### 2.1 Universal doc header (fixed)

Every cascade doc below Vision carries, after its title and governance lines:

```markdown
**Serves:** <parent doc §anchor>            e.g. Phase 0.5 §W1 (docs/roadmaps/phase-0.5.md)
**Status:** live | closed | superseded by <doc>
**Last verified:** YYYY-MM-DD against <commit SHA>
```

- `Serves:` is the goal chain. From any doc, an agent reaches *intent* in ≤ 3 hops (spec → weekly → phase → tier). A doc that can't name what it serves is either vision or scope creep.
- `Status:` kills the stale-plan failure mode: exactly one live phase doc, one live weekly plan. Superseded docs point forward.
- `Last verified:` is the pattern the 06-29 weekly doc already uses ("reconciled against `f6a55cf`") — promoted to a required line. Plans drift from code; this line says how far.

### 2.2 Required steering sections (fixed)

Three sections, proven in the field, become mandatory in their home docs:

| Section | Home | Purpose |
|---|---|---|
| **Decided — do not reopen** | Phase doc (and heavy specs) | Table of settled decisions with resolutions. Stops agents re-litigating deployment posture, editor-of-record, sequencing. |
| **Do not start** | Weekly plan | Named parked work with its owning phase. Scope fences at the exact altitude where scope creep happens. |
| **Close-out** | Weekly plan | Outcome + evidence (SHA / test run / artifact) per slice. The lightweight evidence block; also what makes dated weeklies a usable archive. |

### 2.3 The compaction-recovery protocol (fixed — this is the point of the design)

Goes in `AGENTS.md`, verbatim or adapted:

> **Objective chain:** at any moment you should be able to state (1) the active slice, (2) its exit criterion, (3) which phase it serves. If you cannot — *especially after context compaction* — stop and re-read `docs/STATE.md`, then the live weekly plan's section for your slice, **before your next edit**. Never infer the objective from recent file changes; the chain is written down precisely so it doesn't have to be reconstructed from context.

`STATE.md` is engineered for this: ≤ 30 lines (~250 tokens), always current, and it carries the active exit criterion **verbatim** — the one deliberate duplication in the whole system, because the exit criterion is the alignment target and must survive even when everything else falls out of context.

---

## 3. Directory Taxonomy

Ratifies the structure that evolved in SketchPad, with two consolidations (naming at discretion; the *set* is fixed):

```
docs/
├── STATE.md                  # altitude 6 — pointer card
├── README.md                 # docs index: canonical name + status of every live doc (≤ 20 lines)
├── vision/                   # altitudes 1–2: VISION.md, tiers.md (+ long-term notes)
├── roadmaps/                 # altitude 3: one live phase doc; closed phases remain
├── plans/                    # altitude 4: YYYY-MM-DD-weekly.md (absorbs session-plans/)
├── specs/                    # altitude 5: feature specs, both weights (absorbs implementation-plans/)
├── reviews/                  # commissioned reviews, playtest write-ups (existing practice)
├── decisions.md              # durable decision log (existing practice — see §5.7)
├── CONVENTIONS.md            # from v1, unchanged
└── REVIEWS.md                # from v1, unchanged — guest inbox/triage (index into reviews/)
```

**`docs/README.md` is the anti-drift device.** The five sample docs already disagree about one file's name (`MVP_ROADMAP.md` vs `docs/MVP_ROADMAP.md` vs `docs/roadmaps/survivors-mvp.md`). One index, one rule: *a rename updates the index in the same commit; other docs may point at the index instead of at volatile paths.*

---

## 4. Template: `docs/STATE.md` (v2 — pointer card)

```markdown
# STATE — !!PROJECT!!

**Updated:** !!YYYY-MM-DD!! · **By:** !!agent/harness!! · **System:** 🟢 | 🟡 | 🔴

<!-- Pointer card, not a log. Budget: 30 lines. Overwrite freely.
     Re-read after EVERY context compaction, before the next edit. -->

## You are here
- **Phase:** !!Phase 0.5 — Harden & Consolidate!! → `docs/roadmaps/!!phase-0.5!!.md`
- **Week:** → `docs/plans/!!YYYY-MM-DD!!-weekly.md`
- **Active slice:** !!W1.1 — Path containment!!
- **Exit criterion (verbatim from plan):** !!"Route tests: traversal rejected 400;
  shared resolveContainedPath() used by uploads, uiAssets, draftService."!!

## Next action
!!One concrete step, executable without asking.!!

## Blockers / waiting on human
- !!…!! (delete when clear)

## Gotchas for the next session
- !!Non-obvious facts that would cause a wrong decision.!!
```

What moved *out* relative to v1: task lists (→ weekly plan), status narrative (→ close-out rows), decisions (→ `decisions.md`). What moved *in*: the chain (phase → week → slice → criterion). The card must stay under budget precisely so that re-reading it after every compaction is a non-decision.

---

## 5. Remaining Templates

### 5.1 `AGENTS.md` — deltas from v1 (rest unchanged)

Add to **Session protocol**: read `docs/STATE.md` first; the **objective-chain rule** (§2.3 verbatim); end-of-session = update STATE.md + add a close-out row if a slice landed. Add to the **Map** table: rows for `docs/README.md` (docs index), `docs/plans/` (this week), `docs/roadmaps/` (current phase), `docs/specs/` (feature specs), `docs/vision/` (direction — rarely needed).

### 5.2 Template: `docs/plans/YYYY-MM-DD-weekly.md`

*Generalized from the 2026-06-29 weekly objectives doc — its structure survives nearly intact.*

```markdown
# Weekly Plan — !!YYYY-MM-DD!! → !!YYYY-MM-DD!!

**Serves:** !!phase doc §!! · **Status:** live · **Last verified:** !!date against SHA!!
**Theme:** !!one line!!

## Slices (priority order)

### 1. !!Slice name!!   *(size S/M/L · status: clear / needs-review / human-required)*
**Objective:** !!observable outcome, one paragraph!!
**Serves:** !!phase §workstream!!
**Spec:** !!docs/specs/<name>.md, or "none — weekly-plan scope"!!
**Entry gate:** !!what must be read or true before starting!!
**Already done — do not re-do:** !!bullets with evidence refs!!
**Session order:** 1. !!…!! 2. !!…!!
**Exit criterion:** !!verbatim, checkable!!

### 2. …

## Do not start
- !!Parked item — owning phase/tier, one-line reason!!

## Close-out  *(filled as slices land; closes the week)*
| Slice | Outcome | Evidence (SHA / test run / artifact) |
|---|---|---|

**Carried forward:** !!items → next week's plan!!
**New work surfaced:** !!discoveries → phase doc or specs, not silently absorbed!!
```

**Weekly ritual (fixed):** Monday (or week start): write the new plan; set last week's `Status: closed`; update `STATE.md` and the docs index. Friday (or week end): fill close-out with evidence; a slice without evidence is *carried forward*, never marked done. Human-required slices (playtests, subjective gates) are labeled so agents prepare but never self-certify them — the 06-29 doc's rule, kept verbatim.

### 5.3 Template: `docs/roadmaps/<phase>.md`

*Generalized from Phase 0.5 — Harden & Consolidate.*

```markdown
# !!Phase N — Name!!

**Serves:** !!tiers.md §Tier N!! · **Status:** live · **Last verified:** !!date, SHA!!
**Phase exit criteria:** §Exit

## 1. Overview
!!What this phase makes true. Why a named phase (what failure mode it prevents).!!

## 2. Decided — do not reopen
| Decision | Resolution |
|---|---|

## 3. Workstreams
### W1 — !!Name!!
**Goal:** !!outcome!!
| Slice | Description | Exit criterion |
|---|---|---|
**Order / parallelism:** !!…!!

## 4. Open questions (owner)
| # | Question | Blocks | Resolved? |
|---|---|---|---|

## 5. Capacity estimate  *(optional)*
## 6. Exit
Numbered, demonstrable criteria — each provable by a test, artifact, or grep.
```

One phase `live` at a time. Slices reference specs when one exists; per-slice implementation detail (files-to-touch) may live here for hardening phases or in a heavy spec for feature work — not both.

### 5.4 Template: light spec (default weight)

*Generalized from EDITOR_IMPROVEMENT_PLAN.*

```markdown
# !!Feature/Module Name!!

**Serves:** !!weekly slice or phase §!! · **Status:** live · **Last verified:** !!date, SHA!!
**Branch / scope fence:** !!e.g. tools/ only; no api/, src/!!
**Audience / intent:** !!who this is for and the experience goal!!

## Phases
### Phase 1 — !!name!!
!!Numbered items: problem → file/line → fix. Concrete enough to execute.!!

## Working constraints
- !!Scope fences, parallel-branch conflicts to avoid, commit expectations!!

## Critical review notes  *(appended by a reviewing agent before implementation)*
!!Findings that modify or gate the plan. A spec without this section has not been reviewed.!!

## Done when
- [ ] !!3–6 observable checks!!
```

The **Critical review notes** convention from the editor plan is kept as a *required* section: a second agent (or guest) reviews the spec and appends findings **before** implementation starts. That one habit caught a contract-level data-loss bug (`itemClass` stripping) that the plan's author had scoped around — cheap, and exactly the multi-agent strength worth institutionalizing.

### 5.5 Heavy spec — when and what (two weights, ratified)

Escalate a light spec to the heavy weight when **any** of:

1. Work spans **> 3 sessions** or multiple agents in parallel;
2. It touches **contracts, schemas, persisted data, auth/security, or money**;
3. It sits on a **known regression seam** (the Phase 0.5 rationale: "locally-correct changes invisible to narrowly-scoped guards");
4. Exit must be **provable to someone else** (release gates, collaborator handoff).

The heavy weight adds to the light template: slices table with per-slice exit criteria (§5.3 shape), **files-to-touch per slice**, sequencing/parallelism notes, its own **Decided — do not reopen** table, and owner questions. That is — deliberately — the useful core of a Boresight work order (bounded scope, checkable exits, evidence, escalation points) without the machinery (frontmatter schema, status state machine, gate lint, closed-world reads). If a project later adopts the Full profile, heavy specs convert to work orders nearly 1:1.

### 5.6 `docs/vision/` conventions

- `VISION.md`: the why, audience, architectural commitments, and the deliberately-open questions — with the tie-breaker clause kept: *when roadmap decisions conflict, the vision's commitments decide.* Revisit triggers named in the footer (new game loop, open question resolves, workspace formalized).
- `tiers.md`: tier/epic sequencing with entry criteria per tier and cross-tier notes. Coarser than any roadmap; no dates.
- Both carry the workspace scope-note (§1). Vision docs have **no** `Serves:` line — they are the top of the chain.

### 5.7 `PROJECT.md` and `decisions.md` — reconciliation

Repos following this standard keep the existing `docs/decisions.md` as the single durable decision log (dated entries, "would revisit if" encouraged). `PROJECT.md` §Decisions from v1 becomes a *pointer* to it. Phase-doc **Decided** tables are snapshots for steering; when a phase closes, rows worth keeping graduate to `decisions.md`.

---

## 6. Token Economics

| Moment | Reads | ≈ Tokens |
|---|---|---|
| Any session start | AGENTS.md + STATE.md | 500–700 |
| **After a compaction** | STATE.md (+ your slice's section of the weekly) | **250–700** |
| Picking up the week | + weekly plan | +1,000 |
| Executing a specced feature | + one spec | +800–2,500 |
| Scope question | + live phase doc | +2,500 |
| Direction question (rare) | + vision/tiers | +3,000 |

The cascade's budget discipline is what makes the compaction story work: recovery costs a few hundred tokens because the *card* is small and the *criterion* is on it — not because the system is small.

---

## 7. Adoption: SketchPad Worked Example

Mostly ratification, not migration:

| Existing | Becomes | Change |
|---|---|---|
| `docs/vision/VISION.md`, `LONG_TERM_ROADMAP.md` | `docs/vision/` | Add Status/Last-verified lines |
| Platform development tiers doc | `docs/vision/tiers.md` | Header lines only |
| Phase 0.5 plan | `docs/roadmaps/phase-0.5.md` | Add `Serves: tiers §1`; already model-conformant |
| Weekly objectives, `docs/session-plans/*` | `docs/plans/YYYY-MM-DD-weekly.md` | Consolidate name; structure already conforms |
| `EDITOR_IMPROVEMENT_PLAN.md`, `docs/implementation-plans/*` | `docs/specs/*` | Add Serves/Status header; review-notes already present |
| *(new)* | `docs/STATE.md`, `docs/README.md` | Create — the only genuinely new files |

Plus: add the objective-chain rule to `AGENTS.md`, and fix the three drifted roadmap pointers via the new index.

## 8. Relationship to the Profile Ladder

- **Solo Baseline (v1)** remains the floor: repos that don't need multi-week planning (small tools, experiments that stuck around). STATE.md there keeps its v1 task-carrying form.
- **This standard (Solo + Cascade)** is the default for product repos: v1's core files + altitudes 1–5 + the v2 STATE card.
- **Standard / Full (Boresight)** remain upgrades for real teams or delegated multi-session work needing enforceable gates. The heavy-spec weight is the on-ramp.

## 9. Promotion Path for This Proposal

On ratification: land templates under `templates/cascade/`; add the profile (`profiles/solo-cascade.md` or fold into a revised `solo.md` with the cascade as its planning layer); update the profiles comparison table; mark `solo-profile-proposal-070326.md` §3 (STATE.md) as superseded by the v2 card for cascade-profile projects. Until then, this document is self-contained and the templates can be copied directly.
