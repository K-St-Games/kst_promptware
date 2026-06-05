# Agent Orchestrator Short-Term Proposal

**Status:** Proposal  
**Date:** 2026-06-05  
**Scope:** Enhance the current `AI_Agent_Dev_Docs` framework with a lightweight agent orchestrator layer  
**Intended phase:** Next development phase, manual/semi-manual pilot  
**Source of truth if adopted:** `templates/WORKFLOW.md` remains canonical for session protocol

---

## 1. Purpose

The current framework already gives AI-assisted projects a compact operating system:

- `CONTEXT.md` for active project state and durable decisions.
- `PROGRESS.md` for scoped sprint tasks and acceptance criteria.
- `HANDOFF.md` for session or role transitions.
- `WORKFLOW.md` for PM / IC / QA perspective rules, cadence, compaction, and escalation.

This proposal adds a short-term orchestration layer on top of that system without replacing it. The goal is to make multi-agent work repeatable across specialized harnesses while keeping the first version repo-native, inspectable, and cheap to run.

The immediate target is not full autonomy. The immediate target is a boring, durable workflow where a human can move work through a PM -> Senior Dev -> Implementer -> Reviewer -> Product Acceptance loop using explicit files and gates.

---

## 2. Problem

The current docs framework supports handoff between agents or perspectives, but it does not yet define a complete multi-harness product-development loop.

Observed workflow:

1. A human starts with a rough product vision.
2. A PM-style model turns the vision into a spec and roadmap.
3. A senior-developer model reviews the plan for technical risk.
4. The PM-style model revises and locks a v0 plan.
5. An implementation model builds a scoped slice.
6. A senior-developer model reviews the diff.
7. The implementation model repairs findings.
8. The PM-style model reviews product fit and creates PR framing.
9. The human approves, redirects, or advances to the next slice.

Today that process is mostly managed by chat, copy/paste, and human memory. The framework should capture this as a repeatable artifact-driven process.

---

## 3. Design Principles

### 3.1 Extend, Do Not Replace

The existing PM / IC / QA loop remains the foundation.

The orchestrator layer should map specialized agent roles onto the existing perspectives:

- Product Manager Agent maps primarily to PM.
- Implementation Agent maps to IC.
- Senior Developer Agent maps to QA plus architecture review.
- Human Product Owner remains final product and merge authority.

### 3.2 Artifacts Over Chat Memory

Every major transition should produce a file that can be reviewed, committed, linked, and replayed. The repo should be able to explain the task without reconstructing a chat transcript.

### 3.3 Bounded Slices

Implementation begins only when a slice has:

- A slice ID.
- A clear objective.
- Explicit in-scope and out-of-scope boundaries.
- Acceptance criteria.
- Testing or verification expectations.
- Known risks or escalation triggers.

### 3.4 Review Gates Before Forward Motion

The orchestrator should make gates visible:

- Vision/spec reviewed before roadmap lock.
- Engineering review before implementation.
- Code review before product acceptance.
- Product acceptance before PR/merge recommendation.

For the short-term version, gate enforcement can be manual. The important step is that the artifacts and criteria exist.

### 3.5 Human Judgment At The Right Boundaries

Humans should be asked for product direction, creative judgment, scope tradeoffs, merge approval, and ambiguous risk decisions. Routine formatting, scoped implementation corrections, and checklist-driven review should be handled by agents when the docs provide enough guidance.

---

## 4. Proposed Short-Term Repository Additions

Add a small optional orchestration packet to the framework:

```text
templates/orchestrator/
├── README.md
├── project_state.json
├── vision_brief.md
├── spec.md
├── roadmap.md
├── engineering_review.md
├── slice_handoff.md
├── implementation_summary.md
├── code_review.md
├── acceptance_report.md
└── decision_log.md
```

These templates should be copied into a project's docs area only when a task is large enough to justify multi-agent orchestration.

Recommended project-local layout:

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
    ├── slices/
    │   ├── slice_001_handoff.md
    │   ├── slice_001_implementation_summary.md
    │   ├── slice_001_code_review.md
    │   └── slice_001_acceptance_report.md
    └── decision_log.md
```

The folder name is intentionally `orchestrator`, not `agentic`, because the work is process coordination. "Agentic" describes the method; "orchestrator" describes the system responsibility.

---

## 5. Minimal State Model

Short-term state should be JSON so scripts can read it later, but it should remain human-editable.

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

This is not a scheduler. It is a durable checklist that lets a human or future CLI know where the process is.

---

## 6. Short-Term Workflow

### Stage 1: Vision Intake

The human provides the rough vision. The PM Agent creates `vision_brief.md`.

Done when:

- Target user or beneficiary is clear.
- Product outcome is clear.
- Constraints and non-goals are named.
- Open questions are recorded.

### Stage 2: Spec And Roadmap

The PM Agent writes `spec.md` and `roadmap.md`.

Done when:

- The spec can be understood without chat context.
- The roadmap has phased implementation.
- The first slice is small enough to implement and review.

### Stage 3: Engineering Preflight

The Senior Developer Agent reviews the spec and roadmap and writes `engineering_review.md`.

Review focus:

- Scope risk.
- Architecture risk.
- Ambiguous acceptance criteria.
- Missing tests or verification.
- Suggested implementation order.
- Simplifications.

Done when:

- Blocking concerns are resolved or explicitly accepted.
- Required revisions are fed back into the spec/roadmap.

### Stage 4: Plan Lock

The PM Agent reconciles feedback and marks the plan ready for implementation.

Done when:

- `project_state.json` has `plan_locked: true`.
- The first slice handoff exists.
- The human has approved or explicitly delegated implementation.

### Stage 5: Implementation Slice

The PM Agent or orchestrator writes `slice_001_handoff.md`. The Implementation Agent executes only that slice and writes `slice_001_implementation_summary.md`.

Done when:

- Code changes are complete.
- Relevant validation commands are run or skipped with rationale.
- Commits are made at logical breakpoints when appropriate.
- The summary records files changed, tests run, surprises, and open questions.

### Stage 6: Senior Developer Code Review

The Senior Developer Agent reviews the diff against the slice handoff and writes `slice_001_code_review.md`.

Possible outcomes:

- Approve.
- Approve with nits.
- Request changes.
- Reject as misaligned or unsafe.

Done when:

- Blocking findings are either fixed or waived by a human.
- The review outcome is recorded.

### Stage 7: Product Acceptance

The PM Agent reviews whether the implemented slice satisfies product intent and writes `slice_001_acceptance_report.md`.

Done when:

- The slice is accepted, accepted with follow-ups, or sent back for revision.
- Follow-up tasks are added to `PROGRESS.md` or the roadmap.

### Stage 8: PR / Merge Readiness

The human or PM Agent prepares PR framing.

Done when the PR body includes:

- Summary.
- Linked spec or roadmap section.
- Slice handoff.
- Tests run.
- Code review outcome.
- Product acceptance outcome.
- Follow-up tasks.

---

## 7. Role Contracts

### Product Manager Agent

Owns:

- Vision clarification.
- Spec drafting.
- Roadmap phasing.
- Slice definition.
- Product acceptance review.

Must not:

- Implement production code during planning.
- Expand implementation scope during acceptance.
- Treat open questions as resolved without evidence.

### Senior Developer Agent

Owns:

- Engineering preflight.
- Architecture risk review.
- Code review.
- Test and maintainability critique.
- Merge recommendation.

Must not:

- Silently expand product scope.
- Rewrite the roadmap unless asked.
- Approve based on summaries when a diff is required.

### Implementation Agent

Owns:

- Scoped code changes.
- Local verification.
- Implementation summary.
- Revision work after review.

Must not:

- Replan product direction.
- Modify source-of-truth docs except the implementation summary or assigned docs.
- Cross scope boundaries without escalating.

### Human Product Owner

Owns:

- Product taste and direction.
- Scope tradeoffs.
- Final merge/PR authority.
- Escalated ambiguity.

---

## 8. Relationship To Existing Framework Files

### `templates/WORKFLOW.md`

Remains canonical for:

- PM / IC / QA rules.
- Lightweight vs full path.
- Cadence.
- Compaction.
- Escalation.

The orchestrator proposal adds a larger full-path variant for multi-agent/multi-harness work.

### `templates/PROGRESS.md`

Remains canonical for task status. Orchestrator slices should reference task IDs from `PROGRESS.md` when possible.

### `templates/CONTEXT.md`

Remains canonical for current project state and durable architecture decisions. Orchestrator decision logs should be copied into `CONTEXT.md` only when they become permanent project decisions.

### `templates/HANDOFF.md`

Remains canonical for live session transition. Orchestrator handoff packets are task artifacts, not replacements for live handoffs.

---

## 9. Short-Term Deliverables

### Phase S0: Proposal Review

Deliverables:

- This proposal.
- Long-term companion proposal.
- Agent/human review feedback.

Exit criteria:

- Decision made on whether to add `templates/orchestrator/`.
- Minimum artifact set approved.

### Phase S1: Template Pack

Deliverables:

- `templates/orchestrator/README.md`.
- Template files listed in Section 4.
- Update `templates/README.md` with an optional orchestrated-track section.
- Update `references/README.md` to link accepted proposal docs.

Exit criteria:

- A project can copy the template pack and run one manual orchestration cycle.

### Phase S2: Manual Pilot

Deliverables:

- One real project pilot using the orchestrator templates.
- At least one slice completed through plan -> implementation -> review -> acceptance.
- Pilot retrospective.

Exit criteria:

- The human does not need to reconstruct context from chat to understand the completed slice.
- Review findings and decisions are traceable to files.

### Phase S3: Thin Script Support

Deliverables:

- Optional script to create a new slice from a roadmap item.
- Optional script to validate required files/gates exist.
- Optional script to package a handoff packet.

Exit criteria:

- Scripts reduce manual file setup without becoming a separate product.

---

## 10. Non-Goals

Short-term work should not attempt:

- Fully autonomous multi-agent execution.
- Runtime adapters for Claude, Codex, Gemini, or other tools.
- A web dashboard.
- Background scheduling.
- Automated merge.
- Complex cross-project memory.
- Replacing the existing PM / IC / QA framework.

---

## 11. Risks

### Risk: Documentation Bloat

Mitigation: Keep orchestrator templates optional and only use them for tasks too large for the lightweight path.

### Risk: Conflicting Sources Of Truth

Mitigation: Make `WORKFLOW.md`, `PROGRESS.md`, `CONTEXT.md`, and `HANDOFF.md` remain canonical. Orchestrator artifacts are slice-specific supporting evidence.

### Risk: Premature Automation

Mitigation: Require at least one manual pilot before adding scripts, and at least two successful pilots before runtime adapters.

### Risk: Agent Role Confusion

Mitigation: Each handoff packet must name intended recipient role and required output.

---

## 12. Recommended Next Step

Create `templates/orchestrator/` with the minimal template pack and run one manual pilot on an active project. The pilot should be chosen for moderate complexity: large enough to require planning and review, small enough to complete within one or two implementation cycles.

The best early pilot is a docs or harness-design slice rather than production code, because the failure mode is low-risk and the process will expose whether the artifact contracts are clear.
