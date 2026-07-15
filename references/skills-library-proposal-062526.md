# Skills Library — Design & Expansion Proposal

> **Filed:** 2026-06-25
> **By:** Claude (builder tier), at Damien's request
> **Status:** Draft — proposed, not yet ratified
> **Type:** Technical proposal
> **Relates to:** Ledger L-011 (library coverage gap), L-014 (lightweight-path precedent), `skills/README.md`, `schemas/skill.schema.json`, `references/2026-06-21_Promptware_Research_Synthesis.md`

---

## 0. Current State (read this first)

A `skills/` folder **already exists** in this repo. It holds 7 skills, created 2026-06-16 as part of the library layer:

| Skill | Profile | Category (this proposal) |
|---|---|---|
| `architecture-decision-records` | Standard | Documentation |
| `multi-agent-design-exploration` | Standard | Decision |
| `project-philosophy-document` | Standard | Documentation |
| `work-order-intake` | Full | Boresight governance |
| `deep-research` | Standard | Research |
| `code-review` | Standard | Engineering |
| `session-handoff` | Minimal | Context |

Those 7 were delivered without a governing work order or a written design rationale — the coverage gap recorded in **Ledger L-011**. This proposal does two things:

1. **Supplies the rationale that was skipped** — what a skill *is* in this repo, what belongs in one, and the conventions every skill follows (§2–§3). This is the design doc the original 7 never got.
2. **Proposes a concrete expansion roster** — the skills worth building next, prioritized, with the governance path to build them (§4–§6).

If the intent was a literal "create the folder," it's done already — and the more useful work is deciding what *goes in it*. That's what the rest of this doc is.

---

## 1. Why a Skills Layer At All

A skill is a **bounded, invokable procedure** — a repeatable workflow encoded once and lifted into any downstream repo. The case for keeping them as a distinct layer, rather than folding them into `AGENTS.md` or rules:

- **Progressive disclosure.** Only a skill's `name` + `description` load at session start; the full `SKILL.md` loads on invocation. This is the Codex-Skills pattern documented in the [research synthesis §3](2026-06-21_Promptware_Research_Synthesis.md) — modular context, paid for only when used.
- **Skill-leakage remedy.** The June 2026 "agent configuration smells" finding (verified in the synthesis addendum: **35% of sampled repos** leak rarely-used procedures into their always-on config) is exactly what a skills folder fixes. Procedures that fire occasionally belong in `skills/`, loaded on demand — *not* in `AGENTS.md`, loaded every session. The skills layer is the structural answer to that smell.
- **Liftability.** A self-contained `SKILL.md` copies cleanly into a downstream `.claude/skills/` directory. That portability is the whole point of this repo as a library.

---

## 2. Skill vs Rule vs Agent (the boundary that keeps the library clean)

The most common way a skills library rots is admitting things that should have been rules or agents. The test:

| If the capability is… | It's a… | Lives in | Loaded |
|---|---|---|---|
| An always-true constraint ("use HTTPS", "write tests") | **Rule** | `rules/` | Every session, always-on |
| A repeatable procedure with a clear start and a bounded deliverable | **Skill** | `skills/` | On invocation |
| A persistent persona with its own tool scope and isolated context | **Agent** | `agents/` | On delegation |

Rule = *what's always true.* Skill = *how to do one thing, on demand.* Agent = *who to hand a category of work to.*

**On deliberate overlap:** `code-review` exists as both a skill and an agent (`agents/code-reviewer.md`). That's intentional, not a bug — invoke the *skill* for a quick inline pass in your current context; delegate to the *agent* when you want an isolated sub-context with its own tool budget doing a heavyweight pass. Any new skill that duplicates an agent should say, in its README, why both forms exist. If it can't justify both, it's one or the other.

**The anti-pattern to reject:** a "skill" whose entire body is a list of standing rules. That's a rule wearing a skill's costume, and it reintroduces the leakage smell the layer exists to prevent.

---

## 3. Conventions Every Skill Follows

Validated against [`schemas/skill.schema.json`](../schemas/skill.schema.json):

- **Directory + file:** `skills/<kebab-name>/SKILL.md`. Optional `README.md` for authoring notes. `name` frontmatter must equal the directory stem.
- **Frontmatter:** `name`, `description` (required); `profile` (minimal/standard/full), `maturity` (canonical/experimental/archived), `tools`, `model`, `origin` (optional).
- **`description` is a router, not a label.** Claude Code matches skills to natural-language requests by this field — write it as "what this does and *when to invoke it*," not a noun phrase.
- **Profile tagging is load-bearing.** It's how a downstream repo lifts only what its complexity warrants. Default new skills to `standard` unless they're Boresight-specific (`full`) or universally cheap (`minimal`).
- **Maturity tagging.** Ship genuinely new procedures as `experimental` until team-vetted, then promote to `canonical`. This gives the library a trial lane without polluting the stable set.
- **Self-containment.** A skill may *reference* repo artifacts (templates, playbooks) but its procedure must be followable after being copied into a foreign repo. No load-bearing dependency on kst_promptware-only paths.

---

## 4. Proposed New Skills

Organized by category. Each is scored on **priority** (build-first / next / candidate) and **profile**. The sharpest gaps are in Boresight governance — the charter defines nouns that no skill currently produces.

### A. Boresight Governance (Full profile)

| Skill | What it does | Why it's a gap |
|---|---|---|
| **`change-order`** | Walk an agent through authoring a change order when a released plan meets reality that disagrees — the negotiated spec/contract amendment. | The charter defines "Change order" as a noun and the **entire two-path exit depends on it** (Constitution §9.2), yet no skill produces one. This is the single biggest hole in the skills set. **Build first.** |
| **`feature-spec`** | Author a feature spec: behavior + mechanically-checkable acceptance criteria + touched contracts. | "Feature spec" is a charter noun with no authoring skill. Pairs upstream of `work-order-intake`. **Build first.** |
| **`work-order-closeout`** | Fill the evidence + reconciliation blocks and run [`playbooks/validation-checklist.md`](../playbooks/validation-checklist.md) before `review → complete`. | Operationalizes the gatekeeper's job as an invokable procedure and gives the new validation playbook a front door. **Build next.** |

### B. Engineering Procedures (Standard profile)

| Skill | What it does | Priority |
|---|---|---|
| **`root-cause-debug`** | Systematic reproduce → isolate → hypothesize → minimal fix → regression test. | **Build first** — universally useful, no current equivalent. |
| **`contract-first-api`** | Design an API / data / event contract *before* implementation, so cheaper tiers can build against a fixed boundary. | Next — directly serves the charter's "Contract" noun; contract-first was the most-cited pattern in the synthesis. |
| **`pr-authoring`** | Produce a structured PR description: what changed, why, risk surface, test evidence. | Next — pairs with `code-review`; the synthesis cited PR-description quality as a measurable reviewability lever. |
| **`test-plan`** | Derive a test plan and cases from acceptance criteria. | Next — pairs with `rules/common/testing.md` as its on-demand procedure. |
| **`safe-refactor`** | Behavior-preserving refactor guarded by characterization tests; maps to the `compile-repair` vs `behavioral-adjacent` distinction. | Candidate (experimental). |
| **`dependency-review`** | Evaluate adding or upgrading a dependency: security, breaking changes, maintenance health. | Candidate. |

### C. Context & Onboarding (Standard / Minimal)

| Skill | What it does | Priority |
|---|---|---|
| **`codebase-orientation`** | Build a mental model of an unfamiliar repo and emit a `CONTEXT.md`. | Next — pairs with the v3 `CONTEXT.md` template. |
| **`context-compaction`** | The trigger-based compaction routine from `WORKFLOW.md`, as an invokable skill (Minimal-friendly). | Candidate — pairs with the existing Minimal `session-handoff`. |

### D. Research & Decision (Standard)

| Skill | What it does | Priority |
|---|---|---|
| **`tech-stack-selection`** | Structured stack decision with a weighted matrix and a recorded rationale. | Next — generalizes the existing [`technical-suggestions/tech-stack-recommendations.md`](../technical-suggestions/tech-stack-recommendations.md) (the Godot-vs-Phaser matrix) into a reusable procedure. |
| **`incident-postmortem`** | Blameless postmortem: timeline, root cause, action items. | Candidate — pairs with `architecture-decision-records`. |

### E. Domain Pack — Game Dev (optional; open question)

Given the `K-St-Games` org and the Godot/Phaser material already in the repo, two domain skills are plausible: **`vertical-slice-spec`** (spec a playable vertical slice of a game feature) and **`playtest-synthesis`** (turn playtest feedback into prioritized, spec'd changes).

**Open question for Damien:** should the *general* library carry domain-specific skills at all, or do these belong in a downstream game repo that lifts from this one? A general promptware library stays cleaner if domain packs live downstream — but a `skills/game/` subfolder, clearly profiled, is a defensible alternative. Flagging rather than assuming.

---

## 5. Roadmap

| Tier | Skills | Rationale |
|---|---|---|
| **Build first** | `change-order`, `feature-spec`, `root-cause-debug`, `work-order-closeout` | Close the sharpest governance gaps + the one universal engineering procedure. |
| **Build next** | `contract-first-api`, `pr-authoring`, `test-plan`, `codebase-orientation`, `tech-stack-selection` | High-value, broadly liftable, each pairs with an existing rule/template/agent. |
| **Candidate** | `safe-refactor`, `dependency-review`, `context-compaction`, `incident-postmortem` | Useful but narrower; ship `experimental`, promote on use. |
| **Optional** | `vertical-slice-spec`, `playtest-synthesis` | Pending the §4E domain-pack decision. |

---

## 6. Governance & Next Steps

The original 7 skills were delivered outside the work-order system (L-011). This batch is the chance to **not repeat that** — and to partially close L-011 by giving the skills layer the evidence trail it never had.

Recommended path:

1. **Cut one work order** — `WO-00xx: Skills library batch 1 (build-first tier)` — governing the four build-first skills. A single bounded order gives them acceptance checks (each `SKILL.md` validates against `skill.schema.json`; each `name` matches its directory; each is invocable) and an evidence block. This is the L-011-correct move.
2. **Lightweight-path the rest** via a ledger entry, per the L-014 precedent, once the batch-1 shape is proven.
3. **Wire each new skill** into `skills/README.md`, `AGENTS.md` (load-on-demand table), and the relevant profile manifest at creation — a skill nobody can find is a dead reference.
4. **Resolve the §4E domain-pack question** before building E.

No runtime, no build step, no new dependency — every skill is markdown the same way the existing 7 are.

---

## Appendix: Skills That Were *Not* Proposed (and why)

To keep the library from bloating, these were considered and rejected:

- **`commit-message` / `commit-craft`** — that's a rule (`rules/common/git-workflow.md`), not a procedure with a deliverable.
- **`code-style-enforcer`** — a rule, and exactly the "lint leakage" smell (62% of sampled repos) the synthesis warns against.
- **`security-audit`** — already covered by `agents/security-reviewer.md`; a skill form would need a distinct justification under the §2 overlap rule, and doesn't have one yet.
