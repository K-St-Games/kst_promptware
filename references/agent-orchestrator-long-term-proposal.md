# Agent Orchestrator Long-Term Proposal

**Status:** Proposal  
**Date:** 2026-06-05  
**Scope:** Longer-term implementation roadmap for an agentic coding harness orchestration system  
**Depends on:** Successful short-term manual pilot from `agent-orchestrator-short-term-proposal.md`  
**Intended phase:** Post-pilot automation and runtime integration

---

## 1. Purpose

This proposal describes the longer-term evolution from a manual, repo-native orchestration workflow into a semi-automated and eventually runtime-integrated agent orchestration system.

The core thesis is:

> The workflow contract should be stable before the runtime becomes automated.

Claude, Codex, Gemini, OpenClaw, NanoBot, and future harnesses should be treated as swappable execution substrates. The durable product is the project-owned process, state, and artifact system.

---

## 2. Strategic Direction

The system should evolve in layers:

1. Manual artifact-driven workflow.
2. Repo-native template pack.
3. Thin CLI for state transitions and packet generation.
4. Runtime adapters for specific harnesses.
5. GitHub/PR integration.
6. Portable project cartridge.
7. Policy-driven partial autonomy.

Each layer should be useful by itself. A later layer must not require invalidating the earlier documentation model.

---

## 3. Long-Term Architecture

### 3.1 Control Plane

The control plane owns process state. It decides what should happen next, which role is responsible, and which gates are open or blocked.

Responsibilities:

- Project registry.
- Slice/task queue.
- State transitions.
- Gate enforcement.
- Artifact versioning.
- Decision log updates.
- Human approval checkpoints.
- Runtime selection policy.
- Recovery after failed or incomplete agent output.

The control plane should be deterministic and inspectable. It should not rely on a long hidden chat transcript.

### 3.2 Execution Plane

The execution plane performs work.

Responsibilities:

- Repo checkout and branch/worktree setup.
- File edits.
- Shell commands.
- Test/lint/build execution.
- Diff generation.
- Commit creation.
- PR creation.
- Runtime-specific model calls.

The execution plane should be replaceable. A task may be executed through Codex, Gemini, NanoBot, OpenClaw, Claude Code, or a future worker if the role contract is satisfied.

### 3.3 Runtime Adapters

Runtime adapters translate orchestration packets into harness-specific calls.

Adapter responsibilities:

- Prompt formatting.
- Context packaging.
- File attachment or workspace access rules.
- Tool permission setup.
- Output parsing.
- Error classification.
- Retry policy.
- Log capture.
- Artifact writeback.

The orchestrator should care about role outputs, not model brand.

### 3.4 Project Cartridge

A project cartridge is a portable project-state bundle.

It may include:

- Manifest.
- Project metadata.
- Current spec.
- Roadmap.
- Active phase and slice.
- Decision log.
- Review history.
- Task queue.
- Repo metadata.
- Runtime adapter configuration.
- Context loading policy.
- Gate policy.

The cartridge is not the same thing as a model memory store. It is a project-owned continuity package that can move between runtimes.

---

## 4. Target State Machine

```text
VisionIntake
  -> SpecDraft
  -> RoadmapDraft
  -> EngineeringReview
  -> PlanRevision
  -> PlanLocked
  -> SliceHandoff
  -> Implementation
  -> CodeReview
  -> Revision
  -> ProductAcceptance
  -> PullRequest
  -> MergeOrClose
  -> NextSlice
```

Blocked or exceptional states:

```text
WaitingForHuman
WaitingForExternalCheck
RuntimeFailed
ValidationFailed
ScopeConflict
SecurityBlocked
Abandoned
```

The state machine should be explicit enough that a process can resume after restart from files and git state.

---

## 5. Gate Policy

Long-term gates should be configurable by project and operation risk.

### Gate A: Vision Approval

Owner: Human Product Owner or delegated PM Agent.

Purpose:

- Confirm the project is solving the right problem.
- Capture user, outcome, constraints, and non-goals.

Required artifact:

- `vision_brief.md`.

### Gate B: Spec Approval

Owner: Human Product Owner plus PM Agent.

Purpose:

- Confirm requirements, non-goals, acceptance criteria, and phase boundaries.

Required artifact:

- `spec.md`.

### Gate C: Engineering Preflight

Owner: Senior Developer Agent.

Purpose:

- Confirm technical feasibility and identify implementation risks before coding.

Required artifact:

- `engineering_review.md`.

### Gate D: Plan Lock

Owner: Human Product Owner or configured policy.

Purpose:

- Prevent implementation against unstable requirements.

Required artifacts:

- Locked spec.
- Roadmap.
- First slice handoff.
- Updated state file.

### Gate E: Code Review

Owner: Senior Developer Agent.

Purpose:

- Confirm correctness, maintainability, scope control, and verification.

Required artifact:

- `code_review.md`.

### Gate F: Product Acceptance

Owner: PM Agent plus human when product judgment is required.

Purpose:

- Confirm the implemented slice satisfies product intent.

Required artifact:

- `acceptance_report.md`.

### Gate G: Merge Approval

Owner: Human Product Owner or explicit merge policy.

Purpose:

- Confirm branch is ready to merge.

Required artifacts:

- PR body.
- Review trail.
- Tests/validation summary.

---

## 6. Runtime Role Model

The long-term orchestrator should route by role contract, not hard-coded model identity.

### Product Manager Role

Preferred current runtime:

- Claude / high-quality product-planning model.

Primary tasks:

- Vision clarification.
- Spec writing.
- Roadmap phasing.
- Slice definition.
- Product acceptance.
- PR framing.

### Senior Developer Role

Preferred current runtime:

- Codex / senior engineering review model.

Primary tasks:

- Architecture preflight.
- Code review.
- Risk detection.
- Test assessment.
- Merge recommendation.

### Implementation Engineer Role

Preferred current runtime:

- Gemini or other fast code-generation worker.

Primary tasks:

- Scoped implementation.
- Test updates.
- Local verification.
- Revision packets.
- Commit creation when policy allows.

### Orchestrator Role

Preferred implementation:

- Deterministic local service or CLI.

Primary tasks:

- State transitions.
- Gate enforcement.
- Packet generation.
- Runtime routing.
- Artifact persistence.
- Recovery and escalation.

The orchestrator should not be modeled as the most creative or highest-cognition agent. It should be a strict process manager.

---

## 7. Long-Term Roadmap

### Phase L0: Manual Pilot Baseline

Goal:

- Prove the artifact-driven workflow manually.

Deliverables:

- Short-term template pack.
- One or more completed pilots.
- Retrospective on artifact friction, gate usefulness, and role clarity.

Exit criteria:

- At least two slices complete through planning, implementation, review, and acceptance.
- Human operator reports less context re-explanation.
- Docs are sufficient to resume after a cold start.

### Phase L1: Repo-Native Orchestrator Workspace

Goal:

- Make orchestration artifacts first-class inside project repos.

Deliverables:

- Standard `docs/orchestrator/` layout.
- JSON schema for `project_state.json`.
- JSON schema for slice metadata.
- Markdown templates for each stage.
- Validation script that checks required artifacts and gate status.

Exit criteria:

- A new project can initialize the orchestration workspace from templates.
- Invalid or incomplete gate state is detected locally.

### Phase L2: Thin CLI

Goal:

- Reduce manual state editing and packet setup.

Example commands:

```bash
agent-orchestrator init
agent-orchestrator new-slice --from-roadmap roadmap.md
agent-orchestrator package --slice slice-001 --role implementer
agent-orchestrator submit-review --slice slice-001
agent-orchestrator accept --slice slice-001
agent-orchestrator status
```

Deliverables:

- CLI for state transitions.
- Handoff packet generator.
- Review packet generator.
- Decision log appender.
- Status reporter.

Exit criteria:

- Human can run the loop locally while still manually pasting packets into external harnesses.

### Phase L3: Runtime Adapter Prototype

Goal:

- Connect one role to one runtime without generalizing too early.

Recommended first adapter:

- Senior Developer review adapter, because review is lower-risk than implementation and produces markdown artifacts.

Deliverables:

- Adapter interface.
- One implemented adapter.
- Output parser.
- Log capture.
- Retry/error policy.

Exit criteria:

- The orchestrator can send a review packet, capture the returned review, and update gate state.

### Phase L4: Multi-Role Runtime Adapters

Goal:

- Add PM and Implementer adapters after the review adapter proves the interface.

Deliverables:

- PM adapter.
- Implementer adapter.
- Runtime registry.
- Role-to-runtime configuration.
- Per-project adapter policy.

Exit criteria:

- The system can route packets to different runtimes by role.
- Runtime failures are classified and recoverable.

### Phase L5: Git And GitHub Integration

Goal:

- Connect slices to branch and PR lifecycle.

Deliverables:

- Branch helper.
- Commit policy helper.
- Diff capture.
- PR body generator.
- GitHub PR creation.
- CI/status check reader.
- Merge recommendation artifact.

Exit criteria:

- A slice can move from handoff to PR with artifacts linked and review state recorded.
- Human approval remains required for merge unless a project explicitly configures otherwise.

### Phase L6: Portable Project Cartridge

Goal:

- Make project orchestration state portable across runtimes and machines.

Deliverables:

- Cartridge manifest.
- Artifact bundle format.
- Runtime configuration.
- State export/import.
- Integrity checks.
- Migration policy.

Exit criteria:

- A project can move between supported runtimes without losing spec, roadmap, decision, task, or review state.

### Phase L7: Policy-Driven Partial Autonomy

Goal:

- Allow safe autonomous progress inside configured boundaries.

Examples:

- Auto-generate implementation handoff after plan lock.
- Auto-run review after implementer summary appears.
- Auto-request revision for blocking findings.
- Auto-create PR after product acceptance.

Non-examples:

- Auto-merge production changes without explicit policy.
- Auto-deploy without a narrow deployment authority model.
- Silently expanding scope.

Exit criteria:

- Autonomy boundaries are documented, configurable, logged, and reversible.

---

## 8. Data Model Direction

Use a hybrid model:

- Markdown for human-authored and agent-authored reasoning artifacts.
- JSON for machine-readable state.
- Git for version history.
- Optional SQLite later for indexed task/event history.
- Optional vector or retrieval stores only as rebuildable caches.

Canonical state should remain in plain files until there is a proven reason to introduce a database.

---

## 9. Failure Handling

The long-term system must handle bad or incomplete agent output as a normal case.

Failure classes:

- Missing required artifact.
- Malformed artifact.
- Runtime timeout.
- Runtime hallucinated unavailable files.
- Implementation scope expansion.
- Tests failed.
- Review disagreement.
- Human rejection.
- Security/policy violation.

Required behavior:

- Mark state as blocked or failed.
- Preserve logs.
- Record the failing artifact.
- Generate a repair packet when possible.
- Escalate to human when policy requires.

---

## 10. Agent Disagreement Model

Agent disagreement should be represented as structured evidence, not hidden in chat.

Recommended fields:

- Disagreement topic.
- Agent positions.
- Artifacts reviewed.
- Severity.
- Decision required.
- Default recommendation.
- Human decision.
- Date.

Disagreements that affect architecture or scope should be promoted to the decision log after resolution.

---

## 11. Metrics

Evaluate the system by delivery quality, not by how autonomous it feels.

Useful metrics:

- Time from vision to first implementation slice.
- Number of human clarifications per slice.
- Number of review cycles per slice.
- Percentage of slices accepted without major revision.
- Regressions caught before merge.
- Regressions escaping after merge.
- Amount of repeated context explanation.
- Scope creep incidents.
- Artifact completeness rate.
- Runtime failure rate.
- Human satisfaction with product direction.

---

## 12. Security And Authority

Long-term automation should keep authority narrow.

Policy dimensions:

- Read access.
- File write access.
- Shell execution.
- Dependency installation.
- Git commit.
- Git push.
- PR creation.
- Issue/comment creation.
- CI rerun.
- Merge.
- Deployment.
- Secret access.

Each project should define defaults. Higher-risk operations should require explicit approval or narrow tools.

---

## 13. Non-Goals Until After Phase L3

Do not build these before at least one runtime adapter is proven:

- Full web dashboard.
- Multi-project autonomous scheduler.
- Auto-merge.
- Deployment automation.
- Cross-project memory graph.
- Complex agent identity/personality system.
- General personal assistant features.
- Heavy database-backed state store.

---

## 14. Open Design Questions

1. Should the first CLI live inside this repo, a new repo, or the eventual harness repo?
2. Should `project_state.json` be the only machine state in L1, or should slices have separate metadata files?
3. Which runtime should receive the first adapter: Senior Dev review, PM spec, or Implementer?
4. What minimum validation should block a state transition?
5. Should implementation agents be allowed to commit directly, or only produce patches?
6. How should the orchestrator distinguish fixable agent failure from human-required ambiguity?
7. How should project-specific approval policy be represented?
8. What artifacts belong in the project repo versus an external project cartridge?
9. When should a decision move from slice artifact to durable project decision log?

---

## 15. Recommended Long-Term Path

Adopt the short-term proposal first. Run manual pilots. Then build automation in the following order:

1. Template pack.
2. State validator.
3. Packet generator.
4. Review adapter.
5. PM adapter.
6. Implementer adapter.
7. GitHub integration.
8. Portable cartridge.
9. Policy-driven autonomy.

This sequence keeps the system grounded in the working human process. It prevents the project from becoming an orchestration platform before the workflow contract has been proven.
