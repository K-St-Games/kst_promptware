# Planning Doc Toolkit — v0.2

> **Filed:** 2026-07-03  
> **Author:** Claude Code review session, commissioned by Doomien  
> **Status:** Draft for field-testing — revised from [`planning-cascade-proposal-070326.md`](planning-cascade-proposal-070326.md) per review feedback.  
> **Scope:** A lightweight, no-ceremony toolkit for solo developers who write dated session plans, feature specs, and roadmaps across product repos. Not a governance framework.  
> **Relation:** Replaces the six-altitude cascade model with four levels; companion to the Solo Baseline (v1) for repos that need structured planning.

---

## 0. What Changed

The v0.1 cascade proposed six altitudes (vision → tiers → phase → weekly → spec → state) with a mandatory `Serves:` alignment chain, a `STATE.md` pointer card, and line/token budgets. Real practice across our repos uses four levels, softer linking, and no pointer card yet.

This revision keeps the patterns that proved useful and drops the machinery that didn't.

| v0.1 claim | v0.2 response |
|---|---|
| Six altitudes are what we need | Four levels — we use Vision, Phase/Release, Session/Weekly, Spec. Tiers and STATE.md live elsewhere in the stack when needed. |
| `Serves:` parent-chain headers required | Soft relational links — "Source of truth," "Relates to," "Depends on," "Do not reopen." Easier to maintain, harder to drift. |
| `STATE.md` is the compaction-recovery card | Optional / experimental. Not proven in practice. The session plan itself handles context recovery. |
| Line/token budgets as hard limits | Guidelines. Some docs need more room than a budget allows. |
| Boresight isomorphism table | Cut. The patterns are independently useful; they don't need the comparison. |

---

## 1. The Four Levels

Each answers one question at one time horizon.

| Level | Answers | Horizon | Where it lives | Cadence |
|---|---|---|---|---|
| **Vision** | Why does this exist? What must never break? | Months–years | `docs/vision/VISION.md` | Revisit at inflection points |
| **Phase / Release** | What does this release require? What proves it? | Weeks–months | `docs/plans/<phase>.md` or `docs/roadmaps/<phase>.md` | One live at a time |
| **Session / Weekly** | What is this week or session for? In what order? What is parked? | Day–week | `docs/plans/YYYY-MM-DD-weekly.md` or `docs/session-plans/YYYY-MM-DD.md` | Dated file per session/week, closed out |
| **Spec / PRD** | How does one feature or module get built? | Per feature | `docs/specs/<name>.md` | As needed; two weights |

**Reading order for an agent starting a session:**
1. The active session/weekly plan (that's the current thing)
2. The relevant spec if executing a specced feature
3. The phase doc if a scope question arises
4. The vision only when direction itself is in question

That's it. Documents at each level **link** to their context but don't carry formal parent chains.

---

## 2. Links, Not Chains

Instead of a required `Serves:` header in every doc, use one of these relational tags when they add clarity:

| Tag | When to use |
|---|---|
| **Source of truth:** *path* | This doc is the canonical reference — other docs should defer |
| **Relates to:** *path* | Related context worth reading together |
| **Depends on:** *path* | This work requires that doc's outcomes |
| **Do not reopen:** *path* | A settled decision documented elsewhere; re-litigation is waste |

These are conventions, not requirements. A session plan that reads well without any of them is fine.

---

## 3. The Patterns Worth Keeping

These are the concrete practices that appear across our best plans and specs. The toolkit is built around them.

### Exit criteria
Every slice or track has a checkable exit condition. "Done when X returns 200, Y test passes, Z value appears." Not "finish the work" — a testable statement.

### Close-out evidence
At the end of a session or week, fill a table: outcome per slice, with a SHA, test run, or artifact as evidence. A slice without evidence is carried forward, not marked done.

### "Do not start" lists
Named parked work with a reason. Prevents scope drift into things that are adjacent but unplanned.

### "Already done — do not re-do"
What has been tried, shipped, or decided so the next agent doesn't rebuild or re-litigate. Includes both successful outcomes and dead ends.

### Non-goals on specs
What the spec deliberately does not cover. Guards against scope creep and gives reviewers a boundary to test against.

### Verification gates on session plans
Before editing, list the checks that must pass before the track can start. Often just "confirm no active agent owns this dispatcher" or "check CURRENT_TASK.md first."

### Spec-weight escalation
Two spec weights (medium and heavy) with clear criteria for when to upgrade. Light specs for contained work, heavy specs when contracts, schemas, or cross-agent boundaries are involved.

---

## 4. Template: Session / Weekly Plan

```markdown
# Session Plan: YYYY-MM-DD — [Brief Title]

**Purpose:** One or two sentences: what this session or week aims to accomplish.

**Source of truth:** CURRENT_TASK.md · PROGRESS.md · [relevant phase doc]

**Legend for size:** S ≈ one hour · M ≈ half a day · L = multi-session, needs splitting

---

## Tracks

### Track [Letter]: [Title]

**Size:** [S/M/L] · **Priority:** [🔴/🟡/🟢/🧑 human gate]

**What:** 2–3 sentences describing the track's scope.

**Reads (files to consult):** [what to read before writing]
**Writes (files to mutate):** [what this track changes]

**Entry gate:** What must be true or read before this track starts.

**Already done — do not re-do:** What's already shipped or tried. Saves duplicating effort.

**Verification gates:**
  - [ ] [specific check, e.g. `pnpm typecheck` passes]
  - [ ] [observable outcome]

**Non-goals:**
  - What this track deliberately does not cover.

---

<!-- Repeat per track. Aim for no more than 3–4 tracks per plan. -->

### What NOT to Pick Up This Session
- [item] — why: blocked, human-gated, deferred per plan, not ready yet, etc.

---

## Close-Out (fill in at end of session)

| Track | Outcome | Evidence |
|---|---|---|
| [Letter] | ✅ Shipped / 🟡 Partial / ⏭️ Slipped | Commit SHA, test run, or note |

**Carried forward:** Items that didn't ship → next session's plan.
**New work surfaced:** Discoveries that need tracking outside this plan.
```

---

## 5. Template: Medium Spec (default weight)

Default weight for a contained feature or module. Use the heavy spec (below) when the escalation criteria trigger.

```markdown
# [Feature / Module Name]

**Status:** [draft / in review / live / superseded]
**Relates to:** [phase doc or weekly slice, if applicable]
**Branch / scope fence:** [e.g. tools/ only; no api/ or src/]

## Goal
What this feature does, who it's for, what experience it enables.

## Non-goals
- What this spec deliberately does not cover.

## Approach
Numbered items or a short description of how it gets built. Concrete enough to execute.

### Phase 1 — [Name]
- [ ] [item: problem → file/line → fix or approach]
- [ ] [item]

### Phase 2 — [Name]
- [ ] [item]

## Working constraints
- Scope fences, parallel-branch conflicts, commit expectations.
- Anything another agent working nearby should know.

## Critical review notes
Appended by a reviewing agent before implementation begins. Findings that modify or gate the plan.

## Done when
- [ ] 3–6 observable, testable checks.
```

---

## 6. Template: Heavy Spec

Escalate from the medium weight when **any** of these are true:

1. Work spans **> 3 sessions** or multiple agents in parallel
2. It touches **contracts, schemas, persisted data, auth/security, or money**
3. It sits on a **known regression seam** (locally-correct changes invisible to narrowly-scoped guards)
4. Exit must be **provable to someone else** (release gates, collaborator handoff)

```markdown
# [Feature / Module Name] — Heavy Spec

**Status:** [draft / in review / live / superseded]
**Serves phase:** [phase doc §] or **Relates to:** [weekly slice]
**Branch / scope fence:** [e.g. api/ and types/ only; no ui/]

## Goal
What this feature does and why it justifies the heavy weight.

## Non-goals
- What this spec deliberately does not cover.

## Decided — do not reopen
| Decision | Resolution |
|---|---|

## Workstreams / Slices

### W1 — [Name]
**Exit criteria:** [checkable, testable]

| Slice | Description | Files to touch | Exit criterion |
|---|---|---|---|
| 1.1 | | | |
| 1.2 | | | |

**Order / parallelism notes:**

### W2 — [Name]
...

## Open questions
| # | Question | Blocks | Resolved? |
|---|---|---|---|

## Working constraints
- Scope fences, parallel-branch conflicts, commit expectations.
- Environment or config requirements.

## Critical review notes
Appended by a reviewing agent before implementation begins.

## Done when
- [ ] [phase-level exit checks, not just slice-level]
- [ ] [regression gates: existing tests green, etc.]
```

---

## 7. Optional: `docs/STATE.md`

`docs/STATE.md` is **optional / experimental** in this toolkit. It's a small pointer card (≤ 30 lines) that carries:

- Current phase and active slice
- The active slice's exit criterion (verbatim)
- Next concrete action
- Gotchas for the next session

Use it if compaction recovery is a real pain point in your workflow. Skip it if your session plans already carry enough context for handoffs. The toolkit doesn't depend on it.

If you do use it, a minimal template:

```markdown
# STATE — [Project]

**Updated:** YYYY-MM-DD · **System:** 🟢 / 🟡 / 🔴

## You are here
- **Phase:** → docs/plans/[phase].md
- **Week:** → docs/plans/YYYY-MM-DD-weekly.md
- **Active slice:** [name]
- **Exit criterion:** [verbatim from the plan]

## Next action
[one concrete step, executable without asking]

## Blockers
- [item — delete when clear]

## Gotchas
- Non-obvious facts that would cause a wrong decision.
```

---

## 8. No Adoption Mandate

This is a toolkit, not a standard. No repo needs to adopt all of it. The proposal for what to do next:

1. Field-test the templates in **one repo** first — Punchlist-lite or 7-segments
2. Keep what works, drop what doesn't
3. After one session cycle, promote useful patterns to `kst_promptware/templates/planning-docs/`
4. Only then consider whether any of this should be a profile

The patterns are the point. The templates are just the packaging.