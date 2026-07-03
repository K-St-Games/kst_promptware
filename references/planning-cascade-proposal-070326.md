# Proposal: The Planning Cascade — Solo Workflow Standard v2

> **Filed:** 2026-07-03
> **Revised:** 2026-07-03 (v2.1) — absorbs a multi-repo review (agent with visibility across 7-segments-project-ide, paperclip-app, punchlist-lite, kst-beta-ide, kst_promptware)
> **Author:** Claude Code review session, commissioned by Doomien
> **Status:** Draft for review — extends `solo-profile-proposal-070326.md` (v1)
> **Scope:** The standard documentation set for a solo developer's product repos — multiple agents, multiple harnesses, occasional guest reviewers
> **Design rules (v2.1):** template **patterns are defaults** (they cost nothing when idle); coordination **mechanisms are trigger-gated** (each names the observed failure that justifies switching it on). This is WORKFLOW.md §5's "trigger-based, not ritual" applied to the meta-docs themselves.

---

## 0. What This Is

A standardization of planning patterns that already exist, in variant forms, across the maintainer's active repos: exit criteria per slice, evidence-backed close-out tables, "already done — do not re-do" blocks, "do not start" scope fences, "decided — do not reopen" tables, entry gates, and pre-implementation review notes. The contribution is naming the toolkit, fixing the sections that field experience shows get skipped, and defining *when* heavier coordination machinery earns its keep — not inventing a governance system.

### Review absorption (v2.1)

| v2 said | Multi-repo review found | v2.1 does |
|---|---|---|
| Six required altitudes | 3–4 altitudes are real practice; Tiers is rare, a state card exists nowhere (though `CURRENT_TASK.md` plays that role ad hoc) | **4 core altitudes; Tiers and the state card are optional, trigger-gated** (§1, §5) |
| `Serves: <doc §anchor>` required | Anchor chains break on every reorg; repos link loosely ("Relates to:", "Source of truth, in order:") | **Parent reference is a loose convention** — name the parent by title; no anchors; existing idioms compliant (§2) |
| Flat line budgets | Real vision docs run 350+ lines and should | **Budget rigor scales with read frequency**; rare-read docs effectively unbudgeted (§1) |
| Fixed directory taxonomy | No two repos share names (`docs/plans/` vs `doc/plans/` vs `session-plans/`) | **The altitude set is fixed; paths and names are per-repo**, mapped in AGENTS.md or the index (§1) |
| Boresight comparison table | Reads as justification, not specification | Cut. |
| — | Close-out tables are "often left 'Not started' or never filled" | **New rule: closing the previous plan is the entry gate for cutting the next one** (§3) |
| Docs index "not needed yet" (review) | Pointer drift is *observed now* in SketchPad's own docs (three names for one roadmap); 7-segments grew `DOCS_INVENTORY.md` organically | Index kept, **trigger-gated**: >10 live docs or first observed drift (§5) |

---

## 1. Altitudes

Four core altitudes — each answers one question at one horizon. Two optional altitudes with explicit adoption triggers.

| Altitude | Answers | Horizon | Cadence | Budget posture |
|---|---|---|---|---|
| **Vision** | Why this exists; commitments; what is deliberately unresolved | Years | Revisit at inflection points | Effectively unbudgeted — read rarely, expansiveness is the point |
| **Phase / release plan** | What the next release requires and what proves it | Weeks–months | One **live** at a time | Guideline ~350 lines |
| **Weekly / session plan** | What this period is for, in what order, what is parked | Days–week | Dated file per period, closed out | Guideline ~150 lines — this one matters; it's the recovery target (§4) |
| **Spec / PRD** | How one feature or module gets built | Per feature | As needed; two weights (§6.4) | Light ≤~150 / heavy ≤~350 |
| *Optional:* **Tiers** | Long-game sequencing between vision and phases | Quarters | Per tier boundary | Adopt when a genuine multi-quarter maturity sequence exists (platform-scale work like SketchPad); lives in the vision folder |
| *Optional:* **State card** | Where are we *right now* | Hours | Per session | Trigger-gated — see §5 |

**Budget principle (replaces v2's flat budgets):** budget rigor scales with read frequency. Files read every session (AGENTS.md, a state card if adopted) stay tight because they're paid for constantly and adherence drops with length. The weekly plan gets a soft ceiling because it's the compaction-recovery target. Vision and phase docs are load-on-demand; write them as long as they need to be.

**Naming is per-repo (fixed).** The standard requires that each core altitude *has a home*; it does not name the directories. `docs/plans/`, `doc/plans/`, `docs/session-plans/`, `docs/roadmaps/` are all conformant. The repo's AGENTS.md map (or docs index, once adopted) states the actual paths — that's the portability layer for agents, not a shared taxonomy.

**Workspace stance (ratified):** per-repo now, promote later. Vision docs whose scope exceeds the repo carry a scope-note naming their eventual workspace home; until promotion, every repo is self-contained and agents never resolve `../` paths. Mirrored copies carry a pointer banner naming the canonical one.

---

## 2. Linking & Status Conventions

Three header lines for planning docs, in descending force:

1. **`Status: live | closed | superseded by <doc>`** — required on phase and weekly plans. Exactly one live phase doc and one live weekly plan per repo at any time. This is the cheapest rule in the standard and kills the stale-plan failure mode outright.
2. **`Last verified: <date> against <SHA>`** — strong convention for any doc that makes claims about code (the 06-29 weekly plan's "reconciled against `f6a55cf`" pattern, promoted). Says how far the plan may have drifted from reality.
3. **Parent reference — loose convention.** One line near the top naming what the doc serves, by title: `Serves: Phase 0.5 — Harden & Consolidate (W1)`. No paths, no §anchors — resilient to renames and reorgs. Existing idioms (`Relates to:`, `Source of truth, in order:`) are compliant. The purpose is that an agent (or guest) can climb from any doc to intent in a few hops; the mechanism is a name, not a link that rots.

---

## 3. The Required Template Sections (the actual standard)

These sections are the toolkit that field practice validates. They are **required in their home docs** — the whole point of v2.1 is that the *sections* are the standard, and everything else is supporting convention.

| Section | Home | What it prevents |
|---|---|---|
| **Exit criterion, per slice** — verbatim, checkable | Weekly plan; heavy specs | "Done" by assertion; agents self-certifying subjective gates |
| **Close-out table** — outcome + evidence (SHA / test run / artifact) per slice | Weekly plan | Silent non-completion; unverifiable weeks |
| **Already done — do not re-do** — with evidence refs | Weekly plan; specs | Re-implementation of shipped work after context loss |
| **Do not start** — parked items with owning phase | Weekly plan | Scope creep at the altitude where it actually happens |
| **Decided — do not reopen** — decision + resolution table | Phase docs; heavy specs | Re-litigation of settled calls (deployment posture, editor-of-record…) |
| **Entry gate** — what must be read/true before starting a slice | Weekly plan slices | Agents starting cold on the wrong footing |
| **Critical review notes** — appended by a second agent before implementation | Specs | Contract-level defects surviving into implementation (this section caught the `itemClass` data-loss bug) |
| **Human-required markers** — slices agents prepare but may not certify | Weekly plan | Agents declaring playtests/subjective gates passed |

**The close-out follow-through rule (new in v2.1, fixed):** a new weekly plan may not be cut while the previous one is `live`. Closing it — filling the close-out table with evidence, or explicitly carrying slices forward — is the entry gate for the next plan. This converts close-out from a discipline that gets skipped (the documented failure across repos) into a structural step that can't be.

---

## 4. Compaction & Handoff Protocol

Lives in `AGENTS.md`; operates on the live weekly plan directly — no extra file required:

> **Objective rule:** at any moment you should be able to state (1) the active slice, (2) its exit criterion, (3) what it serves. If you cannot — after a context compaction, at session start, or on receiving a handoff — re-read the live weekly plan's section for your slice **before your next edit**. Never reconstruct the objective from recent file changes or conversation remnants; it is written down so it doesn't have to be inferred.

This works because §3 guarantees the weekly plan carries verbatim exit criteria, entry gates, and already-done fences — the plan *is* the steering document. The end-of-session obligation is the mirror image: update the plan's slice status (and close-out row if one landed) so the next agent's re-read is true.

---

## 5. Trigger-Gated Mechanisms

Machinery is off by default. Each mechanism names the failure that turns it on — adopt on first observation, not in anticipation.

| Mechanism | Adoption trigger | Evidence so far |
|---|---|---|
| **State card** (`docs/STATE.md` or the existing `CURRENT_TASK.md` idiom): ≤30-line pointer card — live phase → live plan → active slice → exit criterion verbatim → next action → gotchas. Overwritten per session; re-read after every compaction. | Any of: an agent demonstrably worked the wrong objective after compaction or a handoff; ad-hoc pointer files (`CURRENT_TASK.md`-style) are appearing anyway — standardize them; live weekly plans regularly exceed ~150 lines, making §4's re-read expensive; 3+ agents interleave within one plan. | Not yet demonstrated as *needed* in any repo (multi-repo review); `CURRENT_TASK.md` exists ad hoc, suggesting latent demand. **Status: experimental.** Template preserved in §6.5. |
| **Docs index** (`docs/README.md` / `DOCS_INVENTORY.md`): canonical name + status + path of every live doc; renames update it in the same commit; other docs may point at the index instead of volatile paths. | >10 live planning docs, or first observed pointer drift. | **Triggered in SketchPad now** — three names for one roadmap across its own docs; 7-segments grew `DOCS_INVENTORY.md` organically. Small repos (punchlist) skip it. |
| **Tiers altitude** (`docs/vision/tiers.md`) | The project has a real multi-quarter maturity sequence with entry criteria per tier (platform-scale work). | Exists and earns its keep in SketchPad; absent elsewhere — correctly. |

---

## 6. Templates

### 6.1 `AGENTS.md` — deltas from v1 (rest unchanged)

Add to **Session protocol**: the objective rule (§4 verbatim); end-of-session = update the live plan's slice status + close-out row if a slice landed. Add to the **Map** table: one row per altitude with this repo's actual paths ("This week's plan → `docs/plans/…`", "Current phase → …", "Specs → …", "Vision → … (rarely needed)").

### 6.2 Weekly / session plan — `<plans-dir>/YYYY-MM-DD-<name>.md`

```markdown
# Weekly Plan — !!YYYY-MM-DD!! → !!YYYY-MM-DD!!

**Status:** live · **Last verified:** !!date, SHA!! · **Serves:** !!parent phase, by title!!
**Theme:** !!one line!!

## Slices (priority order)

### 1. !!Slice name!!   *(size S/M/L · clear / needs-review / human-required)*
**Objective:** !!observable outcome, one paragraph!!
**Spec:** !!<specs-dir>/<name>.md, or "none — weekly-plan scope"!!
**Entry gate:** !!what must be read or true before starting!!
**Already done — do not re-do:** !!bullets with evidence refs!!
**Session order:** 1. !!…!! 2. !!…!!
**Exit criterion:** !!verbatim, checkable!!

## Do not start
- !!Parked item — owning phase, one-line reason!!

## Close-out  *(filling this is the entry gate for the next plan)*
| Slice | Outcome | Evidence (SHA / test run / artifact) |
|---|---|---|

**Carried forward:** !!→ next plan!!
**New work surfaced:** !!→ phase doc or a spec — never silently absorbed into this week!!
```

### 6.3 Phase / release plan — `<plans-dir>/<phase-name>.md`

```markdown
# !!Phase N — Name!!

**Status:** live · **Last verified:** !!date, SHA!! · **Serves:** !!vision/tier, by title!!

## 1. Overview
!!What this phase makes true, and the failure mode a named phase prevents.!!

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

## 5. Exit
Numbered, demonstrable criteria — each provable by a test, artifact, or grep.
```

### 6.4 Specs — two weights (ratified)

**Light spec (default):** parent/Status/Last-verified header · branch & scope fence ("`tools/` only") · audience/intent · phases as concrete numbered items (problem → file → fix) · working constraints · **Critical review notes** (a second agent reviews and appends findings *before* implementation — required) · "Done when" (3–6 observable checks).

**Escalate to the heavy weight when any of:** work spans >3 sessions or parallel agents · touches contracts, schemas, persisted data, auth/security, or money · sits on a known regression seam · exit must be provable to someone else. The heavy weight adds: slices table with per-slice exit criteria, files-to-touch per slice, sequencing/parallelism, its own Decided table, and owner questions. Heavy specs convert nearly 1:1 to Boresight work orders if a project ever adopts the Full profile — that's the on-ramp, and the only Boresight coupling this standard keeps.

### 6.5 State card (optional — install only when a §5 trigger fires)

```markdown
# STATE — !!PROJECT!!
**Updated:** !!date!! · **By:** !!agent/harness!! · **System:** 🟢 | 🟡 | 🔴
<!-- Pointer card, not a log. ≤30 lines. Overwrite per session; re-read after every compaction. -->

## You are here
- **Phase:** !!name!! → !!path!!
- **Plan:** → !!path to live weekly plan!!
- **Active slice:** !!id/name!!
- **Exit criterion (verbatim):** !!…!!

## Next action
!!one concrete step!!

## Blockers / waiting on human
## Gotchas for the next session
```

### 6.6 Vision folder

`VISION.md`: the why, audience, commitments, deliberately-open questions, and the tie-breaker clause (*when plans conflict, the vision's commitments decide*). Optional `tiers.md` per §5. Both carry the workspace scope-note. No parent reference — this is the top. No budget — read rarely, write it as long as it needs to be.

Durable decisions: keep the repo's existing `docs/decisions.md` as the single log; phase-doc Decided tables are steering snapshots whose keeper rows graduate there when the phase closes.

---

## 7. Two Real Instantiations (names differ, altitudes match)

| Altitude | SketchPad | 7-segments (per multi-repo review) |
|---|---|---|
| Vision | `docs/vision/VISION.md` (+ platform tiers) | `docs/vision/VISION.md` (~350 lines — fine) |
| Phase | `docs/roadmaps/phase-0.5.md` | `docs/plans/<phase>.md` |
| Weekly/session | weekly objectives + `docs/session-plans/` | `docs/session-plans/YYYY-MM-DD-….md` |
| Specs | `docs/implementation-plans/`, standalone plans | `docs/specs/` |
| Index (triggered?) | **Yes — drift observed**; adopt | `DOCS_INVENTORY.md` already exists |
| State card (triggered?) | Not yet — watch for it | `CURRENT_TASK.md` exists ad hoc — bless or retire deliberately |

Adoption in both cases is ~90% ratification: add Status/Last-verified lines, make the §3 sections required in the templates already in use, and adopt the close-out entry-gate rule. No renames required.

## 8. Ladder & Promotion

- **Solo Baseline (v1)** — floor for small tools and experiments (its STATE.md keeps the v1 task-carrying form there).
- **This standard** — default for product repos: v1 core files + the four altitudes + §3 sections + trigger table.
- **Standard / Full (Boresight)** — for real teams or delegated work needing enforceable gates; heavy specs are the on-ramp.

On ratification: land templates under `templates/cascade/`, add the profile entry, update the comparison table. Until then this document is self-contained.
