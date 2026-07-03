# Proposal: The Solo Baseline — Revised Lightweight Profile

> **Filed:** 2026-07-03
> **Author:** Claude Code review session, commissioned by Doomien
> **Status:** Draft for review — promotes to `profiles/` on ratification
> **Scope:** Replaces `profiles/minimal.md` as the default starting profile for real projects
> **Target user:** A solo developer running multiple agents across multiple harnesses, who occasionally invites one collaborator whose agents review and suggest improvements
> **Companion:** implements the applicable recommendations from `repo-review-070326.md` (§1.1–1.2, §3.1–3.5, §4.1)

---

## 0. Why Minimal Doesn't Fit This User

The current Minimal profile assumes a *single agent* doing *short-lived work*: one HANDOFF.md, two rules installed into a Claude-Code-specific directory, no durable project memory, and no concept of a second party. The actual primary use case — yours — breaks all four assumptions:

1. **Multiple harnesses.** Rules in `.claude/rules/` and skills in `.claude/skills/` only exist for Claude Code. Cursor, Codex, Copilot, and Gemini sessions get `AGENTS.md` at best. The baseline must be harness-neutral; harness-specific machinery must be an optional adapter, never a requirement.
2. **Multiple agents, interleaved.** HANDOFF.md alone carries transition state but nothing durable. Two sessions later, the *why* behind a decision is gone, and the next agent re-litigates it. Solo-with-many-agents needs the durable/transactional split more than a team does, because there is no human colleague holding the context between sessions — the docs are the only institutional memory.
3. **A guest lane.** An invited collaborator's agent has no defined place to put findings, no boundary on what it may touch, and no way to know which past suggestions were already considered and declined. Un-triaged suggestions accumulate as noise; re-proposed declined ideas burn sessions.
4. **Token discipline.** The profile should demonstrate the balance the library preaches: a cold start under ~1,000 tokens, depth loaded only on demand.

**Design principles** (each traces to the repo review):

- **Harness-neutral core, harness-native adapters.** The required doc set works on any harness that can read markdown. Claude Code extras (settings enforcement, path-scoped rules, skills) are optional add-ons.
- **Outcomes, not procedures.** The docs bind what must be *recorded*, never how the agent works. Native plan modes, task tools, and built-in reviewers are explicitly welcome.
- **Two speeds of documentation.** One transactional file (overwritten freely) and one durable file (edited deliberately). Nothing in between.
- **One home per norm.** Every rule appears in exactly one file; everything else points.
- **Budgets everywhere.** Every file declares its line budget in an HTML comment agents can see and humans can enforce.

---

## 1. The File Set

```
project-root/
├── AGENTS.md                        # Cold-start + hard rules + guest lane (≤ 45 lines)
├── CLAUDE.md                        # One line: @AGENTS.md  (Claude Code shim)
└── docs/
    ├── STATE.md                     # Transactional: status, now/next, blockers, gotchas (≤ 80)
    ├── PROJECT.md                   # Durable: what/why, architecture, invariants, decisions (≤ 120)
    ├── CONVENTIONS.md               # Code + git conventions, load-on-demand (≤ 150)
    └── REVIEWS.md                   # Guest findings inbox + owner verdicts (≤ 60 active)
```

| File | Speed | Who writes | Cadence |
|---|---|---|---|
| `AGENTS.md` | Static | Owner | Policy changes only |
| `docs/STATE.md` | Transactional | Every session, at end | Overwrite freely |
| `docs/PROJECT.md` | Durable | Owner or agent, deliberately | Decision lands / architecture shifts |
| `docs/CONVENTIONS.md` | Static | Owner, once per stack | Rarely |
| `docs/REVIEWS.md` | Transactional | Guests append; owner triages | Per review visit |

**Cold-start read: `AGENTS.md` + `docs/STATE.md` ≈ 600–900 tokens.** `PROJECT.md` is read on the first session in a repo and whenever architecture questions arise. `CONVENTIONS.md` is read when writing code in earnest. Nothing else is required context.

Mapping from the v3 layer, for migration: `HANDOFF.md` + `CONTEXT.md` §1–2 → `STATE.md` · `CONTEXT.md` §3–4 → `PROJECT.md` · `PROGRESS.md` → `STATE.md` Next/Later · `general-engineering-standards.md` + the two Minimal rules → `CONVENTIONS.md`.

---

## 2. Template: `AGENTS.md`

*Structure fixed; wording at discretion. Replace every `!!…!!`.*

```markdown
# AGENTS.md

Cold-start for any AI agent in this repo. Read this file, then `docs/STATE.md`.
Everything else is load-on-demand.

## Project
!!NAME!! — !!ONE_LINE_DESCRIPTION!!. Stack: !!STACK!!.
Full picture (architecture, invariants, decisions): `docs/PROJECT.md` — read it
on your first session here.

## Session protocol
1. Read `docs/STATE.md` before changing anything.
2. Work the top item in its **Next** list unless directed otherwise.
3. Before ending: update STATE.md (status, next, gotchas). If a durable decision
   was made, add one row to `docs/PROJECT.md` §Decisions in the same edit.

These docs bind **outcomes, not procedures**: use your harness's native
planning, task-tracking, and review tools freely — these files are the shared
record between agents, not a script.

## Hard rules
- `main` is protected. Feature branches (`type/short-description`), merge via PR.
- Conventional Commits, lowercase. Stage specific files — never `git add -A`.
- Do not touch without asking: !!PROTECTED_PATHS — e.g. migrations/, .env*, deploy config!!
- Stop and ask a human when: auth, payments, or stored user data are involved;
  a schema or data migration is needed; the task is ambiguous enough that two
  reasonable implementations would differ in behavior.
- Full conventions: `docs/CONVENTIONS.md` — read before writing significant code.

## Guests (invited collaborator agents)
Your lane is **review and suggestion**, not implementation. Read
`docs/PROJECT.md` (especially Non-goals and Invariants), then `docs/STATE.md`,
then review. File findings as PR review comments (line-level) or append rows to
`docs/REVIEWS.md` §Inbox (repo-level). Do not edit code, STATE.md, or
PROJECT.md; do not merge. Suggestions are triaged by the owner — check
§Triaged before proposing something, it may already be decided.

## Map
| Need | Read |
|---|---|
| Where things stand right now | `docs/STATE.md` |
| Architecture, invariants, past decisions | `docs/PROJECT.md` |
| Code + git conventions | `docs/CONVENTIONS.md` |
| Review findings and verdicts | `docs/REVIEWS.md` |
```

---

## 3. Template: `docs/STATE.md`

*The inter-agent bus. Overwrite discipline is the whole design: history lives in git, not in the file.*

```markdown
# STATE — !!PROJECT!!

**Updated:** !!YYYY-MM-DD!! · **By:** !!agent/harness!! · **System:** 🟢 working | 🟡 in progress | 🔴 broken

<!-- Overwrite freely — this is a dashboard, not a log. Budget: 80 lines.
     Assumes one active session at a time. Running parallel agents? Give each
     a branch/worktree and reconcile this file at merge. -->

## Status
One or two sentences: what just happened, where things stand.

## Now
What is mid-flight: files being changed and their state (e.g. "mid-refactor,
tests failing"). Empty if the tree is clean.

## Next (ordered — take the top item)
1. [ ] !!Task!! — done when: !!observable check!!
2. [ ] …

## Later (unordered pool — promote deliberately)
- …

## Blocked
- !!What · why · what unblocks it!! (delete rows when cleared)

## Gotchas for the next session
- Non-obvious facts that would cause a wrong decision: workarounds in place,
  temporary state, "X looks unused but isn't", env quirks.

<!-- Durable decisions do NOT live here — put them in PROJECT.md §Decisions
     at the moment you'd be tempted to write them here. -->
```

Task granularity rule (fixed): every **Next** item carries a "done when" that an agent can verify without asking. If Next exceeds ~7 items or Later exceeds ~15, that's the signal to graduate to a real tracker or the Standard profile — not to grow the file.

---

## 4. Template: `docs/PROJECT.md`

*The durable memory. This file is why agent #4 on harness #3 doesn't re-litigate what agent #1 decided.*

```markdown
# PROJECT — !!NAME!!

<!-- Durable: edit deliberately, rarely. Budget: 120 lines.
     If a section wants to change weekly, its content belongs in STATE.md. -->

## What & why
2–4 sentences: what this is, who it's for, the one problem it solves.

## Non-goals
- At least three things this project deliberately will not become.

## Architecture in one screen
**Stack:** !!runtime, framework, versions!!
**Layout:**
| Path | Role |
|---|---|
| `src/…` | !!role!! |

**Core flow:** 3–5 bullets or one ASCII sketch of the main data/control path.
**External services:** !!list, or "none"!!

## Invariants
<!-- Things that must never break. A change that would violate one requires a
     Decisions row first. Guests: review against THESE, not your own defaults. -->
- e.g. "Save files round-trip across versions", "No PII in logs", "Core loop stays framework-agnostic"

## Decisions
<!-- Append-only, newest first. This is the do-not-re-litigate list. -->
| Date | Decision | Why | Would revisit if |
|---|---|---|---|
| | | | |

## Run & verify
dev: `!!cmd!!` · test: `!!cmd!!` · build: `!!cmd!!`
```

The **"Would revisit if"** column is the ADR discipline at one-tenth the weight: it tells a future agent (or guest reviewer) exactly what evidence would legitimately reopen a decision — anything else is re-litigation.

---

## 5. Template: `docs/REVIEWS.md`

*The guest lane's landing zone and the owner's decision trail over suggestions.*

```markdown
# REVIEWS — external findings & verdicts

<!-- Guests append to Inbox only. Owner moves rows to Triaged with a verdict.
     Declined rows are never deleted while relevant — they prevent
     re-proposal. Budget: 60 active lines; prune long-closed rows (git keeps
     history). -->

## For guest agents
1. Orient first: AGENTS.md → PROJECT.md → STATE.md. Review against the
   project's stated Non-goals and Invariants, not your own defaults.
2. Line-level findings → PR review comments. Repo-level findings → one Inbox
   row each: specific, actionable, severity-tagged.
3. Check §Triaged before filing — a declined verdict answers you unless you
   bring new evidence (name it).
4. No style opinions unless `docs/CONVENTIONS.md` backs them.

## Inbox (awaiting owner triage)
| Date | Source | Sev | Finding — what, where, suggested fix |
|---|---|---|---|
| | | | |

## Triaged
| Date | Finding | Verdict | Why / follow-up |
|---|---|---|---|
| | | accepted → STATE.md Next · declined · deferred | |
```

**The triage loop (owner):** on each guest visit's close, move every Inbox row to Triaged with a verdict; `accepted` rows become STATE.md **Next/Later** items in the same edit. Inbox empty = review absorbed. This is the ledger pattern scaled down: the verdict column is what stops your collaborator's agent from re-suggesting the same refactor every visit.

**Invite snippet** (paste to your collaborator): *"Point your agent at `AGENTS.md` in the repo root — there's a Guests section defining its lane. Findings go to PR comments or `docs/REVIEWS.md` Inbox. `PROJECT.md` has the invariants worth reviewing against."*

---

## 6. `docs/CONVENTIONS.md` — Distill, Don't Copy

Do **not** install the six `rules/common/` files. Distill `coding-style.md` + `git-workflow.md` + the relevant language stub into one file, keeping only rules you'd actually flag in review. Suggested skeleton (≤ 150 lines): Naming · Files & functions (size ceilings) · Error handling (no silent failures; empty catch requires a comment) · Testing (what must be tested; test-name style) · Git (the full commit-type enumeration — `AGENTS.md` states the two-line version and points here) · Language specifics (your tsconfig strictness, package manager, etc.).

Guests cite this file for style findings; if it isn't written down here, it isn't a finding.

---

## 7. Harness Adapters

The core set above requires nothing harness-specific. Adapters, from mandatory-shim to optional:

| Harness | Adapter |
|---|---|
| **Claude Code** | `CLAUDE.md` containing exactly `@AGENTS.md` — Claude Code does not read AGENTS.md natively. **This shim is part of the baseline, not optional.** |
| **Codex / Cursor** | Read `AGENTS.md` natively — no shim. |
| **GitHub Copilot** | `.github/copilot-instructions.md`: three lines — "Read `AGENTS.md` at the repo root first and follow its session protocol; it is the canonical agent instruction file." |
| **Gemini CLI** | Symlink `GEMINI.md → AGENTS.md`, or set `contextFileName`. |

**Optional Claude Code extras** (install only if the project lives mostly in Claude Code):

1. **Zero-token enforcement** — `.claude/settings.json`:
   ```json
   {
     "permissions": {
       "deny": [
         "Bash(git add -A*)", "Bash(git add .*)",
         "Bash(git push* main*)", "Bash(git push --force*)"
       ]
     }
   }
   ```
   Binds only Claude Code; server-side branch protection covers everyone else.
2. **Path-scoped rules** — mirror CONVENTIONS.md language sections into `.claude/rules/<lang>.md` with `paths:` frontmatter so they inject only when matching files are touched. Keep CONVENTIONS.md canonical; the rule files are generated copies.
3. **A `wrap-up` skill** — `.claude/skills/wrap-up/SKILL.md` (directory form) that walks the end-of-session STATE.md update. Optional sugar; the six-line protocol in AGENTS.md is the actual contract.

---

## 8. What This Profile Deliberately Drops

No work orders, no charter/constitution/ledger/capability map, no PM/IC/QA hats, no WORKFLOW.md, no agents roster, no governance headers on installed files. Explicit upgrade triggers:

- **STATE.md Next chronically > 7 items, or you're writing acceptance criteria in paragraphs** → Standard profile (structured sessions, PROGRESS.md).
- **A second *regular* contributor, or you're delegating multi-session work you won't personally review line-by-line** → Full profile (bounded delegation, work orders, evidence).
- Occasional guest reviewers are **not** an upgrade trigger — that's what REVIEWS.md is for.

Downgrade from Minimal is nil: Minimal remains available for throwaway repos, but this profile becomes the recommended floor for any project expected to survive a month.

## 9. Adoption Checklist

- [ ] Create the five docs from §§2–6; replace every `!!…!!`
- [ ] Add the `CLAUDE.md` shim (+ Copilot/Gemini shims if used)
- [ ] Seed PROJECT.md: non-goals (≥3), invariants (≥1), first Decisions row (stack choice)
- [ ] Seed STATE.md with 3–5 Next items, each with a "done when"
- [ ] Optional: `.claude/settings.json` deny list; branch protection on `main`
- [ ] Verify cold start: a fresh agent reading only AGENTS.md + STATE.md knows what to do first
- [ ] Commit everything

## 10. Promotion Path for This Proposal

On ratification: (1) land the four templates under `templates/solo/`; (2) rewrite `profiles/minimal.md` → `profiles/solo.md` with the §1 manifest, or keep both with Minimal demoted to "experiments only"; (3) update the profiles comparison table and README pointers; (4) fold the harness-adapter table into WO-0007's scope. Until then this document is self-contained — the templates above can be copied into a project directly.
