# Repository Review — Critical Feedback & Recommended Improvements

> **Filed:** 2026-07-03
> **Author:** Claude Code review session, commissioned by Doomien
> **Status:** Review memo for maintainer action — not operational
> **Scope:** Full-repo review focused on two stated tensions: (1) instruction depth vs. token burn, (2) keeping downstream agents on track vs. letting them leverage their own harness strengths
> **Method:** Every file read; all relative links mechanically checked; harness-facing claims verified against the Claude Code docs (code.claude.com/docs) as of 2026-07-03

---

## 0. Executive Summary

The bones are excellent: load-on-demand `AGENTS.md`, the two-audience rule, trigger-based compaction, fixed/discretion tagging, evidence blocks, escalation payloads, and the profile system are all better than most published promptware. The problems cluster in three places:

1. **Installation instructions that silently fail on your primary harness.** Skills installed per the profile manifests will not register in Claude Code, `AGENTS.md` is never auto-loaded by Claude Code, and the library's `code-review` skill shadows Claude Code's bundled reviewer. Downstream projects set up by the book get less than they think they got, with no error.
2. **Duplication as the main drift engine.** The same norms live in 3–5 places, and they have already diverged (branch types, commit types, escalation lists). This is the real token cost too — not any single file's length.
3. **The governance loop is empirically too heavy for its own repo.** The library layer (F-008…F-014) shipped outside the work-order system, and the entire WO queue has been parked behind one human gate since 2026-06-12. The repo's own history is telling you where the lightweight path needs to be codified.

Priorities: fix the harness-facing defects first (§1 — they're cheap and currently corrupting every downstream install), then adopt the token mechanics in §3 (path-scoped rules, anchor-instead-of-inline, config-level enforcement), then land the one conceptual amendment in §4.1 (artifact obligations, not procedure obligations) — that single clause is the answer to your rigidity question.

---

## 1. Verified Defects (broken today)

### 1.1 Skill install paths are wrong for Claude Code — skills silently won't register

`skills/README.md` and all three profile manifests instruct: *"Copy `SKILL.md` … to `.claude/skills/<skill-name>.md`"* (a flat file). Claude Code discovers skills only as **directories**: `.claude/skills/<skill-name>/SKILL.md`. A flat `.md` file directly under `.claude/skills/` is not a skill; the flat-file layout belongs to `.claude/commands/`. Anyone following the manifests gets no `/session-handoff`, no `/work-order-intake`, etc., and nothing tells them.

**Fix:** change every manifest row to `.claude/skills/<skill-name>/SKILL.md`. Also fix `skills/README.md`'s note that frontmatter `name` "must match the filename stem" — the command name comes from the **directory** name.

### 1.2 Claude Code never reads `AGENTS.md` — the cold-start file silently doesn't load

The official docs are explicit: Claude Code reads `CLAUDE.md`, not `AGENTS.md`, and recommend a one-line shim (`@AGENTS.md` import) or a symlink. This repo has `AGENTS.md` and no `CLAUDE.md`; every profile's project template creates `AGENTS.md` at root with no shim. So on the harness this library is most obviously built for (`.claude/rules/`, `.claude/agents/`, `.claude/skills/`), the two-rules-that-override-everything file is never injected — the agent only finds it if it happens to read it.

**Fix:** commit a 2-line `CLAUDE.md` (`@AGENTS.md` + nothing else) to this repo, and add the same file to all three profile manifests. This is WO-0007's real content, and it's the highest-leverage item in the whole queue — pull it forward. (Note: `/init` does read `AGENTS.md` when generating, but that only helps users who run `/init`.)

### 1.3 The library's `code-review` skill shadows Claude Code's bundled `/code-review`

Per the docs: *"a `code-review` skill in your project's `.claude/skills/` replaces the bundled `/code-review`."* Claude Code ships a bundled reviewer with typed findings, effort levels, and verification passes; installing the Standard profile as documented replaces it with the library's markdown checklist. That's a strict downgrade on this harness, and it happens silently.

**Fix:** rename the library skill (e.g. `review-report` or `structured-code-review`), or document the shadowing as a deliberate choice in the manifest row. Audit future skill names against the bundled set (`/code-review`, `/security-review`, `/verify`, `/run`, `/debug`, …).

### 1.4 Broken cross-references

A mechanical link check (all relative links, all `.md` files) found:

| File | Broken link |
|---|---|
| `agents/README.md` | `../skills/claude-api/` — **skill does not exist** in this repo (referenced for model pricing/routing) |
| `guides/microservice-workflow-guide.md` | `screenshots/screenshot1.png` — file absent |
| `references/cursor-recommendations.md` | 3 root-relative links written as if from repo root (`guides/…`, `references/…`) |

AGENTS.md declares cross-references load-bearing, and Constitution §10.4 mandates cross-reference integrity — the repo currently fails its own norm. See §3.6 for the tooling recommendation.

### 1.5 Skill catalog / profile manifests contradict each other

`skills/README.md`'s catalog and the skills' own frontmatter mark `multi-agent-design-exploration` and `project-philosophy-document` as **Standard** profile — but `profiles/standard.md` doesn't install them; only `profiles/full.md` does. One of the two surfaces is wrong. (The profiles comparison table agrees with the manifests: Standard = 4 skills.)

**Fix:** either add both to the Standard manifest or change frontmatter + catalog to `full`. Pick whichever matches intent, then make the gatekeeper/consistency checklist compare `profile:` frontmatter against manifests so this can't drift again.

### 1.6 Model guidance is stale and contradicts the library's own schema

- `agents/README.md`: *"Current model IDs: claude-opus-4-8, claude-sonnet-4-6, claude-haiku-4-5-20251001"* — `claude-sonnet-4-6` is no longer current (Sonnet 5 / `claude-sonnet-5` exists; so does the Fable/Mythos 5 tier, which `agent.schema.json` already anticipates with `fable`).
- `profiles/standard.md`: *"Add `model: claude-sonnet-4-6` if you want to pin"* — a full model ID, while `schemas/agent.schema.json` restricts `model` to the enum `[haiku, sonnet, opus, fable]`. The harness accepts both aliases and full IDs, but the library's own schema rejects the library's own advice.

**Fix:** this is L-012's lesson landing in your own docs — pins rot. Remove concrete model IDs from prose entirely; recommend family aliases only (`sonnet`, `opus`), let the schema stay as-is, and point at the harness's model docs instead of restating them.

### 1.7 Git conventions have already drifted across four files

| Surface | Branch types | Commit types |
|---|---|---|
| `AGENTS.md` | feature, fix, docs, refactor | — |
| `guides/contributing.md` (canonical) | feature, fix, docs, refactor | 10 (incl. `build`, `ci`) |
| `rules/common/git-workflow.md` | + `chore` | 8 (no `build`, `ci`) |
| `templates/general-engineering-standards.md` | — | 10 |

Each individually reasonable; jointly they guarantee an agent can be "wrong" against one doc while following another. This is the concrete instance of the duplication problem (§2.3).

**Fix:** one enumeration lives in `contributing.md`; every other file states the rule in one line and links. The library rule file (which ships downstream, where `contributing.md` doesn't) becomes the canonical copy *for downstream* — so mark the repo-local docs as pointing to it, not vice versa.

### 1.8 Index rot

- `references/README.md` is missing rows for `improvement-proposal-052726.md` and `enhancement-proposal-062126.md`; "Last Updated: May 25" predates four additions.
- `AGENTS.md`'s repository-structure tree and `README.md`'s docs map both omit `research/` (added 2026-06-21).
- `research/2026-06-21_Promptware_Research_Synthesis.md` violates the repo's own kebab-case naming rule.

### 1.9 Full profile ships this repo's constitution as-is — with dangling instance references

`profiles/full.md` says: *"Copy `constitution.md` as-is. The constraints are universal."* They aren't: §1.3 (Boresight codename neutrality), §4.2 (brownfield `/templates` exception, citing Ledger L-008), and §4.3 reference this repo's history and ledger entries that won't exist downstream. A new project gets a constitution citing an L-008 it doesn't have. Note the asymmetry: the charter gets "create fresh from the structure" treatment, the constitution doesn't — it deserves the same.

**Fix:** add `templates/constitution-template.md` — the universal §§ with `!!…!!` slots for instance-specific clauses — and point the Full manifest at it. Keep the repo's own `constitution.md` as the worked instance.

### 1.10 Library schemas define fields the harness doesn't read (and miss one it does)

`skill.schema.json` defines `tools`; Claude Code's skill frontmatter field is `allowed-tools` (plus `disable-model-invocation`, `paths`, `context`, `agent`, `user-invocable` — none modeled). The custom fields (`profile`, `maturity`, `origin`) are harmlessly ignored by the harness, but that's worth stating explicitly in `skills/README.md` so nobody expects `profile:` to *do* anything at runtime. Same note for agents: docs show `tools:` as a comma-separated string; the library's YAML flow arrays appear to parse, but normalizing to the documented form costs nothing and removes a portability question.

**Fix:** align schema field names with the harness where they overlap (`allowed-tools`), keep library-only metadata but label it as such, and consider adopting `paths`/`disable-model-invocation` where useful (§3.2, §4.2).

---

## 2. Structural Feedback

### 2.1 Three stacked frameworks, one repo — say so in three lines

A cold-start agent currently has to infer the relationship between the v4 bundle (charter/constitution/work orders), the v3 session layer (PM/IC/QA + state files), and the library (rules/agents/skills/profiles). The pieces are all documented but the *frame* isn't stated anywhere compact. Add to `AGENTS.md`, right after "What this repo is":

> Three layers: **library** (`rules/ agents/ skills/ profiles/` — what downstream projects install), **bundle** (`charter … work-orders/` — how governed work runs), **session layer** (`templates/` — how any session behaves). If you're not executing a work order, the bundle is read-only context.

Three lines, and the biggest cold-start ambiguity is gone.

### 2.2 The governance loop is too heavy for its own repo — codify the lightweight path

Two pieces of empirical evidence from your own history:

- The entire library layer (F-008…F-014, 30+ files) shipped **outside** the work-order system (L-011). The framework's own maintainers routed around the framework.
- WO-0001 has sat in `review` since 2026-06-12; AC-12 gates the charter amendment (L-010), which gates the library's artifact classes, which gates… everything. Three weeks of queue parked behind one human gate, with no aging mechanism.

This isn't an indictment of the design — it's the design telling you its own applicability boundary. Recommendations:

1. **Resolve L-011 as option (b), by change order:** define which artifact classes require the full WO loop (contracts, schemas, north-star amendments, anything with a `writes:` surface other agents depend on) and which take the lightweight path (library content, references, guides) — PR + review, evidence in the PR description, no WO. Make the boundary risk-based, not universal.
2. **Add gate aging:** a standing rule that a gate pending > N days auto-generates a ledger entry and appears on the capability map (`review (stale)`). Gates that can silently stall forever are how spec-first systems die in practice.
3. **Ratify or re-scope WO-0001.** Everything downstream is blocked on AC-11/AC-12. If the full ratification ceremony is the blocker, that's §2.2 point 1 again.

### 2.3 Duplication is your drift engine (and your real token cost)

Beyond the git-convention drift (§1.7), the same content lives in multiple normative places:

- **Escalation triggers:** 4+ variants (`WORKFLOW.md` §6, `rules/common/security.md`, `rules/common/performance.md`, profile AGENTS templates' Conduct sections) — overlapping but not identical lists.
- **"Never `git add -A`":** AGENTS.md, Constitution §10.3, git-workflow rule, contributing.md, engineering standards.
- **Transitive-write table:** Constitution §7, the work-order template §3, *and every instantiated order* (WO-0001 carries it verbatim). `builder.md` paraphrases it a fourth way.
- **Review methodology:** `agents/code-reviewer.md` and `skills/code-review/SKILL.md` are ~90% the same document, maintained twice.
- **Standard profile installs both `general-engineering-standards.md` and the 6 rules**, which overlap heavily (naming, comments, commit format, performance).

Principle to adopt (it's already your Constitution §6.3 instinct, applied horizontally): **every norm has exactly one home; every other mention is one sentence + a link.** Concretely: retire `general-engineering-standards.md` from the Standard manifest (its unique content — data-driven design, debug overlays — moves into `rules/` or a playbook); collapse escalation triggers into a single `rules/common/escalation.md` that the others reference; fix the template inlining per §3.4.

### 2.4 The closed-world reads doctrine has an ambient-context hole

Constitution §7.1: *"knowledge from outside an order's reads list that would shape a norm or contract requires a ledger entry."* But `agents/builder.md` step 1 mandates reading `constitution.md` §§7–9 — which is itself outside every order's `reads:` list (WO-0001's included), and which shapes norms by definition. A builder applying §7.1 literally must ledger its own protocol. Fix with one sentence in §7: *"The north-star artifacts, the ledger, and the capability map are ambient reads for every order; they are never listed in `reads:`."*

### 2.5 Noun collisions against your own noun budget

"**Tier**" means mutation-rights class (builder/planner/architect) in the charter glossary and maturity level (T1–T4) in `guides/tier-epic-specification-framework.md`. "**Gate**" similarly does double duty (WO transition sign-off vs. tier-boundary checkpoint). Both guides are canonical; an agent holding both simultaneously has a genuine ambiguity. Cheapest fix: rename in the tier-epic guide ("maturity levels", "milestone checkpoints") or add a disambiguation note to the charter glossary. Constitution §5 exists precisely for this — apply it to yourself.

---

## 3. Token Economics — Where the Burn Actually Is

Measured word counts (≈ tokens × 1.33). Always-on cost of a Standard-profile downstream project on Claude Code:

| Always-on context | Words | ≈ Tokens |
|---|---|---|
| 6 common rules (auto-loaded from `.claude/rules/`) | ~2,300 | ~3,100 |
| 1 language rule (populated stub) | ~500 | ~650 |
| AGENTS.md (once shimmed per §1.2) | ~250 | ~330 |
| Session-start reads per template (CONTEXT + PROGRESS + WORKFLOW) | ~1,800 | ~2,400 |
| **Total before any work** | **~4,850** | **~6,500** |

6.5k tokens is ~3% of a 200k window — the burn is not catastrophic. The real cost is **adherence dilution** (the harness docs themselves note long instruction files reduce compliance) and **drift surface** (§2.3). Mechanisms, in descending value:

### 3.1 Path-scope the rules — the harness already has the mechanism you need

`.claude/rules/` supports `paths:` frontmatter; rules with it load **only when Claude touches matching files**. The library uses this nowhere. Apply it:

- `typescript.md` → `paths: ["**/*.{ts,tsx,js,jsx}"]`, `python.md` → `["**/*.py"]`, `go.md` → `["**/*.go"]`
- `testing.md` → scope to test globs + `src/**` if desired; `performance.md` is a candidate too
- Keep `git-workflow.md`, `security.md`, `coding-style.md` unconditional

This roughly halves Standard's always-on rule load and is *exactly* your depth-vs-burn balance implemented in config instead of judgment. Add the `paths` recommendation to each rule stub's header and to the profile manifests.

### 3.2 Stop re-reading WORKFLOW.md every session

The profile AGENTS templates say to read `CONTEXT + PROGRESS + WORKFLOW` at every session start. WORKFLOW.md is ~975 words of *static* protocol; the parts that matter mid-session are §4 (cadence) and §5 (compaction), needed only at decision points. Put the 10-line loop summary in the AGENTS template and change the instruction to "read WORKFLOW.md §4–5 when deciding whether to update or compact." ~1,300 tokens/session back for free.

### 3.3 The work-order template inlines what it tells authors to anchor

Template §4 instructs: *"Point to binding constitution sections by anchor — never inline them."* The template itself inlines the transitive-write table, the routing rule, and the three-part exit condition — so every instantiated order re-ships ~300 words of constitution, creating copies that can diverge (and per §2.4, the constitution is ambient anyway). Replace with anchors (`Constitution §7.4`, `§9.2–9.3`); keep only the one-line discretion rule inline. Saves ~300 words per order forever and deletes a drift surface.

### 3.4 Governance headers don't belong in downstream runtime context

The four-line governance header is valuable *in this repo*. Installed into `.claude/rules/`, it's ~40 words per file of provenance metadata injected into every session (~250 words across a Standard install). Either strip headers on install (add to the manifest instructions), or convert them to HTML comments in library files — the harness strips block-level HTML comments from CLAUDE.md/rules content before injection, while humans and Read-tool access still see them. (The v3 templates already use the HTML-comment trick for STALE CHECK notes — same idea.)

### 3.5 Enforce mechanically what can be enforced mechanically — zero tokens, stronger than prose

Your own research synthesis flagged it: instructions are "context, not hard enforcement." The library's hardest rules are all hook/permission-shaped, yet the profiles ship no settings. Add an optional `.claude/settings.json` snippet to each profile:

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

plus a note that branch protection covers the server side. Every rule moved from prose to config is tokens saved *and* a rule that can't be rationalized away mid-session. The prose keeps the *why*; the config owns the *no*.

### 3.6 Ship the validator — it would have caught today's defects

L-007 defers the gatekeeper validator, but §1.4/§1.5/§1.7 above were all found by ~40 lines of script (link resolution + frontmatter-vs-manifest comparison). Constitution §1.2 already permits validators as "tooling conveniences." Commit `tools/validate.py` (link check, schema check, placeholder grep, profile-consistency check) plus an optional GitHub Action. Keeping L-007's *spirit* (no bundle dependency) while ending the "one-off manual run" era costs an afternoon and stops shipping broken installs downstream.

### 3.7 Extend the budget discipline you already have

v3 state files have explicit line budgets — the library doesn't. Adopt: **always-on context (rules + AGENTS.md) ≤ 300 lines per project; anything over must be path-scoped or demoted to a skill.** One sentence in `rules/README.md`, and the depth-vs-burn tradeoff becomes a checkable number instead of a vibe.

---

## 4. Rigidity vs. Harness Leverage

### 4.1 The one amendment that answers the balance question: obligations are artifact-level, not procedure-level

Right now `builder.md` and the templates prescribe *procedure* ("execute steps in order", "read X then Y then Z", "commit with this exact command"). Harnesses increasingly have native machinery — plan mode, task trackers, subagent orchestration, auto memory, bundled reviewers — that the procedures silently forbid by omission. Add a **harness accommodation clause** to the constitution (and echo it in the profile AGENTS templates and `builder.md`):

> **Harness accommodation (fixed):** This system binds *outcomes and artifacts* — status transitions, evidence blocks, reconciliation, escalation payloads, ledger entries — not the mechanics of producing them. An agent may use any native harness capability (planning modes, task trackers, subagents, built-in review tools) to satisfy an obligation, provided the required artifact lands in the required file in the required form. Where a harness offers a stronger native equivalent of a prescribed step, prefer it and record the substitution in the reconciliation block.

That's the whole answer to "on track without rigidity": the *what* is closed-world; the *how* is explicitly open. It also future-proofs the bundle against harness evolution, which is the real risk to a runtime-agnostic design.

### 4.2 Collapse the skill/agent duplicates using harness-native binding

Claude Code skills support `context: fork` + `agent: <name>` — a skill can *be* the invocation surface for a subagent. Keep `agents/code-reviewer.md` as the single source; reduce the skill to frontmatter + three lines ("run the code-reviewer agent against the current diff; report in its format"). Same option for security-review if you add a skill for it later. One methodology, two invocation surfaces, zero duplicated maintenance.

### 4.3 Prefer `disallowedTools` where the intent is "don't write"

`gatekeeper.md` says "you read; you do not write" but expresses it as an allowlist (`tools: [Read, Grep, Glob, Bash]`) — which both under-enforces (Bash can write) and over-enforces (new read-only tools are excluded by default). `disallowedTools: Write, Edit` states the actual intent and inherits future tools. Same review for the other agents: allowlists for *capability scoping*, denylists for *prohibitions*.

### 4.4 Distribution: the library wants to be a plugin (on this harness)

`profiles/README.md`'s sync story is "check the CHANGELOG, copy changed files, re-apply customizations" — version-blind (files carry no `version:` field) and entirely manual. Claude Code plugins are the native answer: versioned installs, a marketplace manifest, skills/agents/hooks bundled together, update flow included. Recommendation: keep the raw-markdown bundle as the canonical runtime-agnostic layer (charter non-goal 1 stands), and *additionally* publish the library layer as a plugin for Claude Code consumers. If that's too much, the minimum viable fix is a `version:` field in library frontmatter + a per-profile manifest file listing file → version, so downstream diffs become tractable.

### 4.5 Acknowledge the harness's native features where they overlap the v3 layer

Two notes worth adding to `templates/WORKFLOW.md` / the profile docs:

- **Auto memory:** Claude Code now accumulates machine-local memory per repo. It is *not* committed and *not* shared — state that committed docs (`CONTEXT.md`/`PROGRESS.md`) remain the team-shared source of truth and win on conflict. One sentence prevents a real confusion mode.
- **Bundled skills:** point users at `/code-review`, `/security-review`, `/verify` as harness-native alternatives, and say when to prefer the library's versions (portability, house format) vs. the bundled ones (deeper harness integration). This is the honest version of §1.3.

### 4.6 State the "when not to use this" rule at the bundle level

The v3 layer has the Lightweight Track — an explicit off-ramp with signals. The bundle has no equivalent decision rule; profiles gesture at it ("pick the lightest profile") but nothing tells an agent mid-project *"this task doesn't need a work order."* Add one rule to the constitution or `AGENTS.md`: *"If the change fits in one session, touches no contract surface, and one reviewer can verify it from the diff, it does not need a work order — use the session layer and note it in PROGRESS.md."* Mirrors §2.2's lightweight path from the agent's-eye view.

---

## 5. Smaller Items

1. **State machine:** the §8 diagram shows `blocked` reachable only from `in-progress`, but §9.4's `infra_block` can plausibly fire during `review`-stage checking. Clarify whether `review → blocked` is legal; the template's linear comment (`… → (blocked) → review → …`) reads like blocked is a pipeline stage.
2. **`schemas/*.json` `$id`s** are bare filenames; draft-07 expects URIs. Harmless locally; a nit for downstream tooling.
3. **`guides/microservice-workflow-guide.md`** is advisory content (with a missing screenshot) in a directory defined as "operational protocols" — better filed under `technical-suggestions/`.
4. **AGENTS.md quick-reference table** is at 26 rows — approaching the point where it stops being "one screen, pointers only" (AC-11). Group rows by layer (library / bundle / session) when it next grows.
5. **`work-orders/WO-0001.md` `reads:`** point at an external incubator path a cold agent can't resolve. Fine as a ledgered bootstrap exception, but say so in the order (one line pointing at L-001).
6. **`references/README.md`** — while fixing §1.8, add a one-line "proposals absorbed into the ledger get a status header" convention; `improvement-proposal-052526.md` already models it.

---

## 6. Prioritized Punch List

**P0 — downstream installs are broken on your primary harness (do first, ~1 session):**
1. Skill install paths → directory form, everywhere (§1.1)
2. `CLAUDE.md` → `@AGENTS.md` shim: this repo + all three profile templates; fold into/pull forward WO-0007 (§1.2)
3. Rename or document the `code-review` bundled-skill shadowing (§1.3)
4. Fix broken links, catalog/profile contradictions, stale model IDs (§1.4–1.6, §1.8)

**P1 — stop the drift, cut the burn (1–2 sessions):**
5. Single-source the git conventions and escalation triggers; retire the standards/rules overlap (§1.7, §2.3)
6. Path-scope language + optional rules; WORKFLOW.md on-demand; template anchors instead of inlined constitution content (§3.1–3.3)
7. Ship `tools/validate.py` + optional CI (§3.6); strip/comment governance headers on install (§3.4)
8. Constitution template for the Full profile (§1.9); ambient-reads sentence (§2.4)

**P2 — the conceptual upgrades:**
9. Harness accommodation clause (§4.1) — the keystone for your rigidity question
10. Lightweight path codified by change order + gate aging; unstick WO-0001 (§2.2, §4.6)
11. Optional `.claude/settings.json` enforcement snippets in profiles (§3.5)
12. Skill→agent `context: fork` dedup; `disallowedTools` review (§4.2–4.3)
13. Plugin packaging or `version:` fields for the sync story (§4.4)

---

*Filed to `references/` per repo convention for external review memos. This document is load-on-demand reference material — do not install it downstream.*
