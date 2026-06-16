# Agent Orchestrator — Formal Spec (Tier 1)

**Status:** Draft — pending scope lock
**Date:** 2026-06-05
**Owner:** Docs maintainer / Human Product Owner
**Scope:** Formalizes the *buildable near-term work* — the `templates/orchestrator/` packet and one manual pilot — from `agent-orchestrator-short-term-proposal.md`.
**Companion roadmap:** `references/agent-orchestrator-roadmap.md` (Tiers 2–4: scripts, runtime adapters, cartridge, autonomy)
**Methodology:** `guides/tier-epic-specification-framework.md` (Tier+Epic) for scope/gates; `templates/ProjectPlan/ProjectPlan_Template.md` shape for the Tier 1 task breakdown
**Source of truth if adopted:** `templates/WORKFLOW.md` remains canonical for session protocol; this spec governs only the optional orchestrator track.

---

## 1. One-Paragraph Statement

Tier 1 delivers an **optional, repo-native orchestrator packet** (`templates/orchestrator/`) plus the documentation to run one **fully manual** multi-agent product-development loop — PM → Senior Dev → Implementer → Product Acceptance — entirely through committed artifacts and explicit gates. It proves the *workflow contract* (artifacts, role boundaries, gates, slice format) is clear enough that a human never reconstructs context from a chat transcript, before any automation is built. No scripts, no runtime adapters, no scheduling.

---

## 2. Why This Spec Exists

The short-term and long-term proposals together describe one system across two horizons. The proposals are mutually consistent — the long-term Gates A–G map directly onto the short-term gate booleans, and the artifact names align. This spec **commits** the near-term, buildable slice and resolves the open seams so the manual pack is forward-compatible with the automation roadmap.

The governing thesis (long-term proposal §1) is preserved:

> The workflow contract should be stable before the runtime becomes automated.

Tier 1 is the contract-stabilization tier. Everything in the roadmap depends on it and must not require breaking it.

---

## 3. Tier Model

The orchestrator evolves as an additive tier stack. Each tier is independently useful and a strict superset of the one below; higher tiers add systems *behind* the stable seam (the artifact + gate contract), never replace it.

| Tier | Name | Weight | What it proves |
|------|------|--------|----------------|
| **T1** | Manual Packet (this spec) | Lightweight | The artifact + gate + role contract is clear and runnable by hand |
| **T2** | Thin Script Support | Medium | State/gate validation and packet generation reduce manual file work |
| **T3** | Runtime Adapters | Heavy | A role packet can be dispatched to a harness and its output written back |
| **T4** | Platform / Autonomy | Full | Portable cartridge, GitHub lifecycle, policy-bounded partial autonomy |

T2–T4 are specified in the companion roadmap. **This document specifies T1 only**, plus the invariants every later tier must hold.

### 3.1 The Stable Seam (fixed at T1, never broken)

1. **Artifact contract.** Every stage transition produces a named markdown artifact in a known location. The Tier 1 artifact names and locations remain canonical across tiers. Later tiers may add optional artifacts such as adapter logs, PR metadata, CI summaries, or runtime transcripts, but they must not rename, relocate, or replace the Tier 1 artifacts.
2. **Gate contract.** The seven gates (§6) and their boolean state in `project_state.json` are fixed. Automation may *set* a gate; it may not invent a new gate ordering.
3. **Slice layout.** A slice is a directory `slices/<slice-id>/` containing a fixed file set plus `slice.json` (§5.2). This single decision resolves the short-term-vs-long-term layout seam up front: flat enough to author by hand, structured enough for a later CLI to walk.
4. **Roles, not models.** The four roles (§7) are defined by their input/output contract, never by model identity. T3 adapters bind a role to a runtime; the role contract is unchanged.
5. **Plain files are canonical.** Markdown for reasoning artifacts, JSON for machine-readable state, git for history. No database is canonical at any tier (databases, if added later, are rebuildable caches).

### 3.2 Contract vs Convention at T1

- **Contract (a pilot is invalid without it):** the artifact set per stage exists; gates are honored in order; the Implementer does not modify source-of-truth docs; each handoff names its recipient role and required output.
- **Convention (recommended, not blocking):** estimated times, prose style, optional sections.

---

## 4. Relationship to the Existing Framework

The orchestrator **extends, never replaces** the PM/IC/QA system.

| Existing file | Stays canonical for | Orchestrator relationship |
|---------------|--------------------|---------------------------|
| `templates/WORKFLOW.md` | PM/IC/QA rules, lightweight vs full path, cadence, compaction, escalation | Orchestrator is the *full-path* variant for multi-agent/multi-harness work |
| `templates/PROGRESS.md` | Task status & acceptance criteria | Slices reference `PROGRESS.md` task IDs when one exists |
| `templates/CONTEXT.md` | Current state + durable architecture decisions | Orchestrator `decision_log.md` entries graduate into `CONTEXT.md` only when permanent |
| `templates/HANDOFF.md` | Live session/role transition | Orchestrator handoff packets are *task artifacts*, not a replacement for live handoff |

**Role mapping:** Product Manager Agent → PM; Implementation Agent → IC; Senior Developer Agent → QA + architecture review; Human Product Owner → final product & merge authority.

---

## 5. Tier 1 Artifacts & Layout

### 5.1 Template pack (ships in the framework)

```text
templates/orchestrator/
├── README.md                      # How to copy & run a manual cycle
├── project_state.json             # Durable checklist (§5.3)
├── vision_brief.md
├── spec.md
├── roadmap.md
├── engineering_review.md
├── decision_log.md
└── slices/
    └── slice_NNN/
        ├── slice.json             # Slice metadata (§5.2)
        ├── handoff.md
        ├── implementation_summary.md
        ├── code_review.md
        └── acceptance_report.md
```

Folder name is `orchestrator` (the system responsibility), not `agentic` (the method).

### 5.2 Project-local layout (copied into a real project)

```text
docs/
├── CONTEXT.md
├── PROGRESS.md
├── HANDOFF.md
└── orchestrator/
    ├── project_state.json
    ├── vision_brief.md
    ├── spec.md
    ├── roadmap.md
    ├── engineering_review.md
    ├── decision_log.md
    └── slices/
        └── slice_001/
            ├── slice.json
            ├── handoff.md
            ├── implementation_summary.md
            ├── code_review.md
            └── acceptance_report.md
```

`slice.json` (machine-readable slice metadata — resolves long-term open question #2 by keeping per-slice state local to the slice):

```json
{
  "slice_id": "slice_001",
  "title": "",
  "roadmap_ref": "",
  "progress_task_id": null,
  "objective": "",
  "in_scope": [],
  "out_of_scope": [],
  "acceptance_criteria": [],
  "verification": [],
  "risks": [],
  "state": "handoff",
  "review_outcome": null,
  "acceptance_outcome": null,
  "last_updated": "YYYY-MM-DD"
}
```

`state` enum (T1 subset of the long-term state machine): `handoff → implementation → code_review → revision → product_acceptance → done`, plus `blocked`.

### 5.3 `project_state.json`

Human-editable durable checklist — *not* a scheduler.

```json
{
  "project": "",
  "phase": "vision_intake",
  "active_slice": null,
  "status": "draft",
  "roles": {
    "product_manager": "",
    "senior_developer": "",
    "implementer": "",
    "human_owner": ""
  },
  "gates": {
    "vision_approved": false,
    "spec_reviewed": false,
    "engineering_reviewed": false,
    "plan_locked": false,
    "implementation_complete": false,
    "code_review_approved": false,
    "product_accepted": false,
    "merge_approved": false
  },
  "open_questions": [],
  "blockers": [],
  "last_updated": "YYYY-MM-DD"
}
```

---

## 6. Gates (fixed contract)

Gates must be honored in order; T1 enforcement is **manual** (a human or agent confirms the artifact exists and sets the boolean). The long-term Gates A–G are the same gates with stricter ownership and automated enforcement — see roadmap.

| # | Gate (state key) | Required artifact | Owner at T1 |
|---|------------------|-------------------|-------------|
| A | `vision_approved` | `vision_brief.md` | Human / PM Agent |
| B | `spec_reviewed` | `spec.md` (+ `roadmap.md`) | Human + PM Agent |
| C | `engineering_reviewed` | `engineering_review.md` | Senior Dev Agent |
| D | `plan_locked` | locked spec + roadmap + first `slices/slice_001/handoff.md` | Human |
| E | `code_review_approved` | `slices/<id>/code_review.md` | Senior Dev Agent |
| F | `product_accepted` | `slices/<id>/acceptance_report.md` | PM Agent (+ human for judgment) |
| G | `merge_approved` | PR body + review trail + validation summary | Human |

**Rule:** no forward motion past a gate whose artifact is missing or whose blocking findings are unresolved. A human may explicitly waive a finding; the waiver is recorded in `decision_log.md`.

---

## 7. Role Contracts (fixed)

### Product Manager Agent
- **Owns:** vision clarification, spec drafting, roadmap phasing, slice definition, product acceptance, PR framing.
- **Must not:** write production code during planning; expand scope during acceptance; treat open questions as resolved without evidence.

### Senior Developer Agent
- **Owns:** engineering preflight, architecture risk review, code review, test/maintainability critique, merge recommendation.
- **Must not:** silently expand product scope; rewrite the roadmap unless asked; approve from a summary when a diff is required.

### Implementation Agent
- **Owns:** scoped code changes, local verification, implementation summary, post-review revision.
- **Must not:** replan product direction; modify source-of-truth docs except its own implementation summary / assigned docs; cross scope boundaries without escalating.

### Human Product Owner
- **Owns:** product taste & direction, scope tradeoffs, final merge/PR authority, escalated ambiguity.

Every handoff packet **must name** its intended recipient role and the required output (resolves the role-confusion risk).

---

## 8. Tier 1 Stage Loop

| Stage | Actor | Produces | Done when |
|-------|-------|----------|-----------|
| 1. Vision Intake | PM | `vision_brief.md` | target user, outcome, constraints, non-goals, open questions captured |
| 2. Spec & Roadmap | PM | `spec.md`, `roadmap.md` | spec understandable without chat; roadmap phased; first slice small enough to implement+review |
| 3. Engineering Preflight | Senior Dev | `engineering_review.md` | blocking concerns resolved or explicitly accepted; revisions fed back |
| 4. Plan Lock | Human/PM | updated `project_state.json`, `slice_001/handoff.md` | `plan_locked: true`; first handoff exists; human approved/delegated |
| 5. Implementation Slice | Implementer | `implementation_summary.md`, code, commits | code complete; validation run or skipped-with-rationale; summary records files/tests/surprises/questions |
| 6. Code Review | Senior Dev | `code_review.md` | outcome recorded (approve / approve-with-nits / request-changes / reject); blocking findings fixed or waived |
| 7. Product Acceptance | PM | `acceptance_report.md` | accepted / accepted-with-follow-ups / sent back; follow-ups added to `PROGRESS.md` or roadmap |
| 8. PR / Merge Readiness | Human/PM | PR body | body links spec/roadmap, handoff, tests run, review outcome, acceptance outcome, follow-ups |

---

## 9. Tier 1 Implementation Plan (task breakdown)

ProjectPlan-style breakdown of the work to *build and validate* Tier 1. Maps to short-term proposal phases S1–S2.

### Phase 1.1 — Author the template pack
**Prerequisites:** this spec scope-locked.
**Goal:** `templates/orchestrator/` exists and is self-explanatory.

- **Task 1.1.1 — Create the packet skeleton**
  - Files to create: every path in §5.1.
  - Success criteria:
    - [ ] All template files present with placeholder content and a top-of-file purpose line.
    - [ ] `project_state.json` and `slice.json` are valid JSON and match §5.2/§5.3.
- **Task 1.1.2 — Write `templates/orchestrator/README.md`**
  - Must cover: when to use the orchestrated track (vs lightweight path), copy-into-project steps, the §8 stage loop, the §6 gate table, the §7 role contracts.
  - Success criteria:
    - [ ] A reader can run one manual cycle from the README alone.
    - [ ] Explicitly states each artifact's owning role and the gate it satisfies.
- **Task 1.1.3 — Wire into framework indexes**
  - Files to modify: `templates/README.md` (add optional orchestrated-track section), `references/README.md` (link this spec + the roadmap).
  - Success criteria:
    - [ ] `templates/README.md` describes the optional track and points here.
    - [ ] `references/README.md` lists this spec and the roadmap with decision relevance.

**Deliverables:** template pack; updated indexes.
**Exit gate:** a project can copy the pack and begin a cycle without reading the proposals.

### Phase 1.2 — Manual pilot
**Prerequisites:** Phase 1.1 complete.
**Goal:** run one real cycle end-to-end.

- **Task 1.2.1 — Select pilot** — moderate complexity; prefer a docs/harness-design slice over production code (low blast radius, exposes contract gaps). Record choice in `decision_log.md`.
- **Task 1.2.2 — Run Stages 1–8** for at least one slice through plan → implementation → review → acceptance.
- **Task 1.2.3 — Retrospective** — capture artifact friction, gate usefulness, role clarity; file fixes against this spec.

**Deliverables:** completed pilot artifacts; retrospective.
**Exit gate (Tier 1 → Tier 2):** see §10.

---

## 10. Tier 1 Exit Criteria (gate into Tier 2)

All must be true before any Tier 2 (scripting) work begins:

1. The template pack exists and a project initialized from it ran ≥1 complete slice.
2. **Cold-start test passes:** a person who did not run the pilot can understand the completed slice from committed files alone — no chat transcript.
3. Every review finding and decision in the pilot is traceable to a file.
4. The retrospective is written and any contract-breaking gaps are fixed in this spec (not deferred).
5. The §3.1 seam held throughout — no artifact was renamed/relocated mid-pilot to make it work.

Per the proposals' premature-automation mitigation: **two** successful pilots are recommended before T3 runtime-adapter work, but one is sufficient to exit T1.

---

## 11. Non-Goals (Tier 1)

Explicitly out of scope here — each has a home in the roadmap:

| Deferred | Home |
|----------|------|
| State/gate validator, packet generator, `new-slice` script | Tier 2 |
| Runtime adapters (Claude/Codex/Gemini/etc.) | Tier 3 |
| GitHub/PR lifecycle integration, CI reading | Tier 4 |
| Portable project cartridge | Tier 4 |
| Policy-driven partial autonomy, auto-merge, deployment | Tier 4 |
| Web dashboard, multi-project scheduler, cross-project memory, DB-backed state | Out of plan until justified |

These are deferred, not rejected — each is load-bearing for a later tier and named so it isn't lost.

---

## 12. Risks

| Risk | Mitigation |
|------|------------|
| Documentation bloat | Pack is optional; used only when a task is too large for the lightweight path |
| Conflicting sources of truth | `WORKFLOW/PROGRESS/CONTEXT/HANDOFF` stay canonical; orchestrator artifacts are slice-specific supporting evidence |
| Premature automation | Require ≥1 manual pilot before scripts; ≥2 before runtime adapters |
| Role confusion | Every handoff names recipient role + required output |
| Layout churn between tiers | §3.1 fixes the slice layout and gate set at T1 |

---

## 13. Decisions This Spec Locks (vs. the proposals)

1. **Slice layout:** `slices/<slice-id>/` directories with `slice.json`, adopted at T1 (resolves short-term flat-files vs long-term subdir+JSON seam; resolves long-term open question #2).
2. **Gate set:** the seven gates of §6 are the canonical, fixed ordering across all tiers.
3. **Spec home:** orchestrator specs live in `references/`; only graduated decisions move into a project's `CONTEXT.md`.
4. **Scope of *this* document:** Tier 1 only. Tiers 2–4 live in `references/agent-orchestrator-roadmap.md`.

### Open questions still owned by the Human Product Owner
- Where the eventual CLI lives (this repo / new repo / harness repo) — *Tier 2 decision, not blocking T1.*
- Whether implementers may commit directly or only produce patches — *recommend "commit at logical breakpoints" for T1 pilot; revisit at T3.*
- First runtime to receive an adapter — *Tier 3 decision; roadmap recommends Senior-Dev review first.*

---

## 14. Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-06-05 | Initial Tier 1 formal spec synthesized from short- & long-term proposals | Docs maintainer |
