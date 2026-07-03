# Session Plan Template

Copy to `docs/session-plans/YYYY-MM-DD.md` or `docs/plans/YYYY-MM-DD-weekly.md` when scoping a new session or week. Replace bracketed content.

---

# Session Plan: YYYY-MM-DD — [Brief Title]

**Purpose:** One or two sentences: what this session or week aims to accomplish.

**Source of truth:** CURRENT_TASK.md · PROGRESS.md · [relevant phase doc]

**Legend for size:** S ≈ one hour · M ≈ half a day · L = multi-session, needs splitting

---

## Tracks

### Track [Letter]: [Title]

**Size:** [S/M/L] · **Priority:** [🔴/🟡/🟢/🧑 human gate]

**What:** 2–3 sentences describing the track's scope.

**Reads (files to consult):**
  - [path] — [why]
**Writes (files to mutate):**
  - [path] — [what change]
**Discovery (inspect, don't change):**
  - [path] — [what to look up]

**Entry gate:** What must be true or read before this track starts.

**Already done — do not re-do:** What's already shipped, tried, or decided. Saves duplicating effort.

**Verification gates:**
  - [ ] [specific check, e.g. `pnpm typecheck` passes]
  - [ ] [observable outcome — "X returns 200", "Y test passes"]

**Non-goals:**
  - What this track deliberately does not cover.

---

<!-- Repeat per track. Aim for no more than 3–4 tracks per plan. -->

## Priority Recommendation

1. **[Track Letter]** — one-line why it goes first
2. **[Track Letter]** — one-line why it goes second

## What NOT to Pick Up This Session

- [item] — why: blocked, human-gated, deferred per plan, validation gate not met
- [item] — why

---

## Close-Out (fill in at end of session)

| Track | Outcome | Evidence |
|---|---|---|
| [Letter] | ✅ Shipped / 🟡 Partial / ⏭️ Slipped | Commit SHA, test run, or brief note |

**Carried forward:** Items that didn't ship → next session's plan.
**New work surfaced:** Discoveries that need tracking outside this plan (add to PROGRESS.md or equivalent).
