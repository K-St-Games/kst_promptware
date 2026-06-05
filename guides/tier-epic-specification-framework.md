# Tier & Epic Specification Framework

**Purpose:** Formal instruction set for agents writing specs or roadmaps using the Tier + Epic development model
**Status:** Canonical
**Last Verified:** 2026-06-01
**Sources synthesized from:** `K_St_Core_Contract_v0.md`, `K_St_Meta_Roadmap.md`, `htf_coder/process-improvement/scope-sanity-check-process.md`, `West-Coast-Crypto-Cruise/docs/tiers/TIER_0_SCOPE.md`, `htf_coder/docs/memory_platform/intent_v1.md`, `htf_coder/docs/memory_platform/roadmap.md`

---

## 1. Why a Tier + Epic Model

Agent-assisted development makes it trivially easy to define features. A spec can grow from 9,000 to 30,000+ words in a single session. Every idea sounds good in isolation. The aggregate represents **years of implementation work** with no corresponding increase in delivery capacity.

The Tier + Epic model solves this by providing:

- **Additive nesting** — each tier is a strict superset of the one below. No breaking changes between tiers.
- **Scope boundaries** — every tier has explicit inclusions *and* explicit exclusions with rationale.
- **Exit criteria** — measurable gates that prove a tier is done before the next begins.
- **Graceful deferral** — features deferred to later tiers are named, not silently dropped.

---

## 2. Core Concepts

### Tier
A **maturity level** — each tier represents a demonstrably working, shippable system. Tiers are additive: everything in Tier N works unchanged in Tier N+1. No breaking changes, ever.

### Epic
A **thematic workstream** that spans one or more tiers. Epics group related work (e.g. "multiplayer" or "content pipeline") that may start in Tier 1 and complete in Tier 4. An epic can be in progress across multiple tiers simultaneously or sequentially.

### Gate
A **verification checkpoint** that must be passed before crossing a tier boundary. Gates have concrete audit evidence — not self-report.

---

## 3. The Standard 4-Tier Maturity Model

This is the canonical tier model used across the workspace. Adapt names to fit your domain, but preserve the **additive superset** property.

| Tier | Name | Weight | What it proves |
|------|------|--------|----------------|
| **T1** | Proof of Concept | Lightweight | Core loop is playable; the fundamental seam/architecture holds |
| **T2** | Minimum Viable | Medium | Full vertical slice — every core system once, bounded content |
| **T3** | Featured / Depth | Heavy | Depth systems, polish, persistence, content breadth |
| **T4** | Platform / Integrated | Full | Cross-system tooling, shared kernel, pack/overlay system |

```text
Tier 1: Playable core, minimal content
  └─ Tier 2: Full vertical slice (all core systems, bounded)
       └─ Tier 3: Depth + polish + persistence + breadth
            └─ Tier 4: Platform integration + shared kernel
```

### 3.1 Tier Invariants (Never Relax)

These hold from **T1 onward** and never weaken as tiers grow:

1. **Additive nesting.** A higher-tier runtime loads lower-tier content unchanged. Tiers only *add* optional fields and systems — they never break or require migration of lower-tier content. New fields are optional with safe defaults.
2. **Stable seam.** The architectural boundary (function signature, plugin interface, contract shape) is fixed at T1 and does not change across tiers. Tiers add systems *behind* the seam, not new seams.
3. **Downward readability.** Higher-tier content remains loadable (degraded but valid) by a lower-tier runtime. A T3 entity with extra fields still renders and functions in a T1 build; the extra fields are simply ignored.
4. **Contract vs convention tightens.** What is *enforced* (breaks the build/tests if violated) vs *recommended* grows stricter per tier:
   - **T1:** Architecture invariants are contract. Everything else is convention.
   - **T2:** Determinism tests and primary validation become contract.
   - **T3:** Schema validation enforced; no unvalidated content loads.
   - **T4:** Round-trip validation with external tooling; pack/overlay compatibility enforced.

### 3.2 Generic Per-Tier Exit Criteria

A tier is "done" when:

- All build/validation commands pass cleanly.
- Core tier invariants (§3.1) still hold.
- All content authored at this tier validates against the current contract.
- A written scope boundary exists documenting what was included and what was explicitly deferred.
- Progress/tracking documentation is updated.

**Additional per-tier gates from the K_St workspace pattern:**

| Gate | Tier Boundary | What Must Be True |
|------|--------------|-------------------|
| **Gate 1: Seam Lock** | T1 → T2 | All active projects hold the core architectural split. Serializable intent envelopes exist. |
| **Gate 2: Determinism & Schema** | T2 → T3 | Determinism test passes. Load-time schema validation exists. Content schemas are frozen. |
| **Gate 3: Platform Integration** | T3 → T4 | ≥2 projects independently hold T2+ invariants. The same utility exists in ≥2 repos. Shared kernel extraction conditions are met. |

---

## 4. The 6-Phase Scope Process

Use this process when writing a spec or roadmap from scratch:

### Phase 1: Initial Ideation
**Goal:** Capture raw ideas without filtering.

- No criticism during capture
- Record the idea *and* its originator
- Flag obvious dependencies ("this requires X to be built first")
- Document the **motivating problem**, not just the proposed solution

**Output:** Dated notes file. Unfiltered. Messy is fine.

### Phase 2: Scope Expansion
**Goal:** Let the best ideas develop enough to understand their implications.

- Allow elaboration but tag each addition with session/date
- Document cross-dependencies (feature A enables feature B)
- Record "gravity well" warnings if an idea diverges from project goals
- **Do not** commit to building anything yet

**Output:** An enriched ideas document. Length is not yet a concern.

### Phase 3: Scope Rationalization
**Goal:** Inventory the full wishlist and produce a structured, tiered, implementable plan.

**Steps:**
1. **Full inventory** — list every distinct feature, component, and principle from Phase 2
2. **Dependency mapping** — mark what requires what
3. **Value scoring** — for each item, estimate:
   - Impact on project goals (1-5)
   - Implementation complexity (1-5)
   - Blocking dependencies (how many things need this first)
4. **Tier assignment** — group into compatible, phased tiers
5. **Cut decisions** — explicitly identify what is pushed to later tiers or deferred indefinitely
6. **Tier 1 scope lock** — define the minimally viable set of features that constitute a meaningful release

**Output:** A tiered wishlist with explicit cut decisions. Each tier has a written scope boundary.

### Phase 4: Scope Definition Commitment
**Goal:** Freeze the scope for the current development phase.

- Publish the Tier 1 scope boundary
- Write a **one-paragraph statement** of what Tier 1 delivers
- Identify and name the features explicitly **excluded** from Tier 1
- Record the rationale for each exclusion

**Rules:**
- No additions to Tier 1 scope without a corresponding removal
- Additions must come with a Phase 3 re-evaluation, not a casual request
- "This is important" is not a reason to add to scope. "This blocks delivery without it" is.

**Output:** A committed scope document with explicit inclusions, exclusions, and rationale.

### Phase 5: Roadmap Creation
**Goal:** Produce a time-phased plan across all tiers.

1. Order tiers by dependency (no Tier N+1 before Tier N)
2. For each tier, estimate: features, dependencies, **validation criteria**
3. Identify parallelizable work within tiers
4. Set milestone markers per tier
5. Record the **minimum conditions** for moving to the next tier

**Output:** A roadmap document with per-tier milestones, dependencies, and gating conditions.

### Phase 6: Phased Development
**Goal:** Execute each tier independently, with clear done-done criteria before advancing.

**Rules:**
- Tier N must be demonstrably working before Tier N+1 work begins
- Each tier produces a usable, if limited, system
- Validation is against behavioral evidence, not self-report
- Tier N+1 must be backward-compatible with Tier N — no breaking changes

**Output:** Working software per tier, each a complete milestone.

---

## 5. Template: Tier Scope Document

Every tier scope document should follow this structure:

```markdown
# Tier [N] Scope Lock

**Status:** [Locked | Outline | Draft]
**Date:** YYYY-MM-DD
**Gating mechanism:** [How this tier is activated — URL param, flag, branch, etc.]

---

## One-Paragraph Statement

[What this tier delivers as a playable/shippable experience. ~3 sentences max.]

---

## Explicit Inclusions

### [Category 1: e.g. Locations, Features, Systems]
| Item | Detail |
|------|--------|
| [ID] | [Description, notes] |

### [Category 2]
...

---

## Explicit Exclusions (with rationale)

| Feature | Home Tier | Rationale for Exclusion |
|---------|-----------|------------------------|
| Feature name | Tier N | Why it's not here — "Not needed to prove X", "Requires Y to be stable first", etc. |

---

## Preconditions for Moving to Next Tier

All must be met before Tier N+1 work begins:

1. [Measurable condition — e.g. "Complete playthrough succeeds without corruption"]
2. [Testable condition — e.g. "Schema freeze declared with version bump"]
3. [Automation condition — e.g. "Validation script exists and passes"]
4. ...

---

## Schema Freeze Rule (if applicable)

[Which schemas are frozen after this tier. What requires a version bump to change.]
```

### Reference: WCCC Tier 0 Example

From the West-Coast-Crypto-Cruise workspace:

> **Statement:** "Tier 0 proves the architecture works end-to-end: a player can start in Puerto Vallarta, travel through 4 intermediate locations, manage resources, survive events, recruit party members, and reach Basecamp Oregon — all on a trimmed 5-node route that validates the route graph, event engine, save/load, and mobile UI before any content scaling begins."

> **Inclusions:** 5 locations, 3 characters, 6-9 events, validation targets for route graph, event prerequisites, save/load, party recruitment, mobile UI.
>
> **Exclusions:** Full 20-location route (Tier 1), full 11-character roster (Tier 1), profession skill checks (Tier 2), combat (Tier 3), audio (Tier 4).
>
> **Exit:** Complete playthrough without corruption, mobile UI confirmed on real devices, schema freeze with version bump, validation script exists and passes.

---

## 6. Template: Per-Tier Checklist

This is the checklist template used in the K_St workspace. Copy it into each project's roadmap:

```markdown
### 📋 Alignment Checklist (Must Pass to Exit Tier)

#### Tier 1: PoC (Playable Loop)
- [ ] **Architecture Seam:** The core architectural separation is proven. This is the single invariant everything else builds on.
- [ ] **Core Loop Playable:** A complete end-to-end path exists — start to finish through minimal content.
- [ ] **Content-Driven:** Entities load from external data, not hardcoded constants. Identity is separated from behavior and visuals.
- [ ] **Telemetry Exposed:** State is observable (debug output, text snapshot, or API) for verification.
- [ ] **Minimal Tests:** Core loop works; no regressions on the happy path.

#### Tier 2: MVP (Vertical Slice)
- [ ] **Full System Coverage:** Every core system exists at least once — even if minimal.
- [ ] **Determinism Asserted:** Repeatable test proves identical inputs produce identical outputs.
- [ ] **Schema Ready:** Load-time validation for custom behaviors. Closed-set enum enforcement.
- [ ] **Field Preservation:** Unknown fields are preserved by all tooling (enables downward readability).
- [ ] **Bounded Content:** Enough content to demonstrate all systems, but not full depth.

#### Tier 3: Featured (Depth & Polish)
- [ ] **Persistence Integrity:** State serialization proves perfect recovery (save/load fidelity).
- [ ] **Schema Validation Enforced:** All content validated at load time. Invalid content is rejected, not silently degraded.
- [ ] **Depth Systems:** Leveling, branching outcomes, status effects, or equivalent depth mechanics.
- [ ] **Content Breadth:** All planned content categories populated.

#### Tier 4: Platform (Integration)
- [ ] **Cross-System Tooling:** External tooling can read/write content without data loss.
- [ ] **Pack/Overlay Compatibility:** Content can be layered, overridden, and extended at runtime.
- [ ] **Shared Kernel Extraction** (if applicable): Common primitives lifted out into a shared package.
- [ ] **Round-Trip Validation:** End-to-end test: edit content in external tool → load in game → export → re-import → verify no data loss.
```

---

## 7. The Epics Model

Epics are larger organizational units that span multiple tiers. Use epics when a workstream is too large for a single tier and cuts across multiple tiers simultaneously.

### Epic Structure

Each epic document should define:

| Section | What to Write |
|---------|---------------|
| **Target Tiers** | Which tiers this epic spans (e.g. "Tier 1/2" or "Tier 3") |
| **Objective** | What this epic delivers when complete |
| **Inclusions** | Features, components, and deliverables |
| **Exclusions** | What is deliberately out of scope for this epic |
| **Definition of Done** | What evidence proves this epic is complete |
| **Promotion Criteria** | What proves we can move to the next epic |
| **Abort/Revert Criteria** | What tells us the approach is wrong |

### Epic Dependency Pattern

```text
Epic 1 (Tier 1/2) ──► Epic 2 (Tier 2/3)
                             │
Epic 4 (Tier 4)     ◄── Epic 3 (Tier 3)
```

**Rules:**
- Epics respect tier ordering. No epic targeting Tier N+1 before Tier N prerequisites are met.
- Epics may run partially in parallel if they target non-overlapping tiers.
- An epic labeled "Deferred" that is a prerequisite for another epic is not deferred — it's load-bearing. Re-label accordingly.

---

## 8. Heuristics & Anti-Patterns

### The Gravity Well Test
Quick scope-creep detection: **"If we build this, does it make the Tier 1 deliverable better, or does it make Tier 1 take longer?"**
- **Both** — consider whether the improvement justifies the delay
- **Takes longer, doesn't improve** — it's a gravity well. Defer to a later tier
- **Doesn't affect Tier 1 at all** — it's a separate project. File it there

### The Scope Gratitude Rule
Every feature deferred to a later tier should be **thanked for existing, not mourned**:
- The idea is not bad — it just isn't right for *this* phase
- The idea is captured and won't be lost
- Having a clear tier structure means the deferred feature has a home
- What gets shipped in Tier 1 is better because it's *focused*, not because we couldn't think of more

### Anti-Patterns to Watch For

| Anti-Pattern | Symptom | Fix |
|-------------|---------|-----|
| **Infinite wishlist** | Every idea gets added, nothing gets cut | Run the 6-phase process. Make Tier 1 the smallest shippable thing. |
| **Silent scope creep** | Tier 1 scope grows paragraph by paragraph | Enforce the one-in-one-out rule. No additions without a removal. |
| **Unattached deferrals** | "Later" is named but no tier or epic is specified | Every deferred feature must name its target tier. |
| **Fake dependencies** | Tier 2 depends on Tier 3 features | Distinguish hard prerequisites from "would be nice to have." |
| **Undocumented baseline** | Tier 0 has no written scope | Even a minimal tier needs explicit inclusions, exclusions, and exit criteria. |
| **Epic dependency hiding** | Epic marked "deferred" is actually required by later epics | Audit the dependency graph. Rename or re-plan. |

---

## 9. Putting It All Together: Process Flow

When tasked with writing a spec or roadmap:

1. **Start with the 6-Phase Process** (§4) — capture ideas, expand, rationalize, commit, roadmap, develop.
2. **Define your tiers** using the 4-Tier Model (§3) — adapt names to your domain, preserve additive nesting.
3. **Write Tier 1 scope** using the template (§5) — one-paragraph statement, explicit inclusions, explicit exclusions, exit criteria.
4. **Define epics** (§7) if workstreams span multiple tiers — document dependencies, promotion criteria, abort criteria.
5. **Add per-tier checklists** (§6) so every agent working on the project knows what "done" means.
6. **Apply heuristics** (§8) — gravity well test every proposed addition. Thank deferred features, don't mourn them.
7. **Lock and iterate.** Once Tier 1 scope is committed, no additions without removals. Re-run the rationalization phase for each new tier.

---

## 10. Related Documents

- `AI_Agent_Dev_Docs/templates/WORKFLOW.md` — PM/IC/QA session protocol
- `AI_Agent_Dev_Docs/templates/CONTEXT.md` — Architecture decisions and current state
- `AI_Agent_Dev_Docs/templates/HANDOFF.md` — Session transition template
- `AI_Agent_Dev_Docs/guides/contributing.md` — Git policy and PR workflow
