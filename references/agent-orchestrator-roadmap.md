# Agent Orchestrator — Long-Term Roadmap (Tiers 2–4)

**Status:** Roadmap — directional, not scope-locked
**Date:** 2026-06-05
**Owner:** Human Product Owner
**Depends on:** `references/agent-orchestrator-spec.md` Tier 1 exit criteria met (≥1 manual pilot; cold-start test passes)
**Source:** condenses `agent-orchestrator-long-term-proposal.md` into the Tier model
**Nature:** a sequence of independently-useful tiers, each a strict superset of the last. No tier may break the Tier 1 seam (`spec.md` §3.1).

---

## 1. Thesis

> Stabilize the workflow contract manually before automating the runtime.

Harnesses (Claude, Codex, Gemini, OpenClaw, NanoBot, future workers) are **swappable execution substrates**. The durable product is the project-owned process, state, and artifact system. Automation is added layer by layer; a later layer never invalidates the earlier documentation model.

---

## 2. Tier Map (continues the spec's tier stack)

| Tier | Theme | Proposal phases | Promotes when |
|------|-------|-----------------|---------------|
| **T2** | Repo-native workspace + thin CLI | L1, L2 | Loop runs locally; invalid gate state detected by tooling |
| **T3** | Runtime adapters | L3, L4 | Packets dispatched to ≥1 harness; output parsed & written back; routing by role |
| **T4** | Platform: Git/GitHub, cartridge, autonomy | L5, L6, L7 | Slice→PR with linked artifacts; state portable across runtimes; autonomy bounded, logged, reversible |

---

## 3. Tier 2 — Repo-Native Workspace + Thin CLI

**Goal:** remove manual file/state bookkeeping without becoming a separate product.

**Deliverables**
- JSON schemas for `project_state.json` and `slice.json` (formalize spec §5).
- Validation script: required artifacts present + gate ordering honored; invalid/incomplete gate state detected locally.
- Thin CLI for state transitions and packet assembly:
  ```bash
  agent-orchestrator init
  agent-orchestrator new-slice --from-roadmap roadmap.md
  agent-orchestrator package --slice slice-001 --role implementer
  agent-orchestrator submit-review --slice slice-001
  agent-orchestrator accept --slice slice-001
  agent-orchestrator status
  ```
- Decision-log appender; status reporter.

**Exit criteria**
- A new project initializes the workspace from templates.
- Human runs the full loop locally, still pasting packets into external harnesses by hand.
- Invalid gate state cannot pass a transition undetected.

**Constraint:** CLI only *reads/writes the same files* a human edits at Tier 1. It introduces no new canonical state.

---

## 4. Tier 3 — Runtime Adapters

**Goal:** connect roles to harnesses, lowest-risk role first.

### Architecture introduced
- **Control plane** (deterministic, inspectable): project registry, slice queue, state transitions, gate enforcement, artifact versioning, runtime-selection policy, recovery.
- **Execution plane** (replaceable): checkout/worktree, edits, shell, test/lint/build, diff, commit, model calls.
- **Runtime adapter** (the new seam): prompt formatting, context packaging, workspace/permission rules, output parsing, error classification, retry, log capture, artifact writeback.

### Sequence
- **T3a — first adapter: Senior-Dev review.** Lower risk than implementation; produces markdown. Deliver adapter interface, one implementation, output parser, log capture, retry/error policy. Exit: orchestrator sends a review packet, captures the review, updates gate E.
- **T3b — PM + Implementer adapters.** Add after the review adapter proves the interface. Deliver runtime registry, role→runtime config, per-project adapter policy. Exit: packets route to different runtimes by role; runtime failures are classified and recoverable.

### Routing by role (not model)
| Role | Indicative runtime | Primary tasks |
|------|-------------------|---------------|
| Product Manager | high-quality planning model | vision, spec, roadmap, slice def, acceptance, PR framing |
| Senior Developer | senior review model | preflight, code review, risk, test assessment, merge rec |
| Implementation Engineer | fast code-gen worker | scoped impl, tests, local verify, revisions, commits (if policy allows) |
| Orchestrator | deterministic local service/CLI | transitions, gate enforcement, packet gen, routing, persistence, recovery |

The orchestrator is a strict process manager, not the highest-cognition agent.

### Failure handling (normal case, not exception)
Classes: missing/malformed artifact, runtime timeout, hallucinated files, scope expansion, tests failed, review disagreement, human rejection, security/policy violation.
Required behavior: mark blocked/failed, preserve logs, record the failing artifact, generate a repair packet when possible, escalate to human when policy requires.

### Agent disagreement model
Represent as structured evidence (topic, positions, artifacts reviewed, severity, decision required, default recommendation, human decision, date) — never buried in chat. Architecture/scope disagreements graduate to the decision log after resolution.

**Non-goal until ≥1 adapter is proven:** dashboards, multi-project scheduler, auto-merge, deployment, cross-project memory graph, heavy DB-backed state.

---

## 5. Tier 4 — Platform: Git/GitHub, Cartridge, Autonomy

### T4a — Git & GitHub integration (L5)
Branch helper, commit-policy helper, diff capture, PR-body generator, GitHub PR creation, CI/status reader, merge-recommendation artifact.
Exit: a slice moves handoff→PR with artifacts linked and review state recorded; **human approval still required for merge** unless a project explicitly configures otherwise.

### T4b — Portable project cartridge (L6)
A project-owned continuity bundle (manifest, metadata, current spec, roadmap, active phase/slice, decision log, review history, task queue, repo metadata, adapter config, context-loading policy, gate policy). Distinct from model memory.
Deliver: cartridge manifest, artifact-bundle format, runtime config, state export/import, integrity checks, migration policy.
Exit: a project moves between supported runtimes without losing spec, roadmap, decision, task, or review state.

### T4c — Policy-driven partial autonomy (L7)
Allowed (inside config): auto-generate impl handoff after plan lock; auto-run review after impl summary; auto-request revision for blocking findings; auto-create PR after product acceptance.
Disallowed: auto-merge production without explicit policy; auto-deploy without narrow deployment authority; silent scope expansion.
Exit: autonomy boundaries documented, configurable, logged, reversible.

### Security & authority (applies from T3 onward)
Per-project defaults across: read, file-write, shell, dependency install, git commit, git push, PR creation, issue/comment, CI rerun, merge, deployment, secret access. Higher-risk operations require explicit approval or narrow tools.

---

## 6. Data Model Direction (all tiers)

- Markdown for human/agent reasoning artifacts.
- JSON for machine-readable state.
- Git for version history.
- SQLite *optional, later*, for indexed task/event history — a cache, never canonical.
- Vector/retrieval stores *optional*, only as rebuildable caches.

Canonical state stays in plain files until a database is *proven* necessary.

---

## 7. Metrics (judge by delivery quality, not how autonomous it feels)

Time vision→first slice; clarifications per slice; review cycles per slice; % slices accepted without major revision; regressions caught pre-merge vs escaped post-merge; repeated-context volume; scope-creep incidents; artifact-completeness rate; runtime-failure rate; human satisfaction with product direction.

---

## 8. Open Design Questions (owner: Human Product Owner)

Carried from the long-term proposal; each is a tier-gated decision, none blocks Tier 1:

1. CLI home: this repo / new repo / eventual harness repo? *(T2)*
2. Single `project_state.json` vs per-slice metadata? *(resolved in spec §13 — per-slice `slice.json`)*
3. First runtime adapter: Senior-Dev review / PM / Implementer? *(T3 — roadmap recommends review first)*
4. Minimum validation that blocks a state transition? *(T2)*
5. May implementers commit directly, or only produce patches? *(T3 policy)*
6. How to distinguish fixable agent failure from human-required ambiguity? *(T3 failure model)*
7. How is project-specific approval policy represented? *(T4 security model)*
8. What lives in the repo vs the external cartridge? *(T4b)*
9. When does a decision move from slice artifact to durable `CONTEXT.md` entry? *(any tier; spec §4 gives the rule)*

---

## 9. Recommended Build Order

1. Template pack *(Tier 1 — see spec)*
2. State/gate validator
3. Packet generator
4. Senior-Dev review adapter
5. PM adapter
6. Implementer adapter
7. GitHub integration
8. Portable cartridge
9. Policy-driven autonomy

This keeps the system grounded in the working human process and prevents it from becoming an orchestration platform before the workflow contract is proven.

---

## 10. Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-06-05 | Initial roadmap (Tiers 2–4) extracted from long-term proposal | Docs maintainer |
