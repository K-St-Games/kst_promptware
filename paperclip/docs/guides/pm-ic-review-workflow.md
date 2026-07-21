---
title: PM/IC Review Workflow
summary: Run an implementer→reviewer loop on Paperclip using execution policy as the runtime-enforced state machine
---

This guide shows how to run a **PM/IC review loop** — an implementer (IC) builds, a reviewer (PM) gates — on top of Paperclip's existing primitives. You do not need a separate orchestrator agent or a polling heartbeat: Paperclip's [execution policy](execution-policy.md) already enforces the handoff atomically.

Pair this with the two role templates it references:

- Implementer: [`Coder`](../../skills/paperclip-create-agent/references/agents/coder.md)
- Reviewer: [`Reviewer`](../../skills/paperclip-create-agent/references/agents/reviewer.md)

## The loop

```
Idea → Spec/Plan → PM vet → IC build → Review → (approve) → Done
                                          │
                                          └─ (request changes) ─┐
                                          ▲                     │
                                          └─── IC re-implements ─┘
```

The runtime — not either agent's prompt — enforces the handoff:

1. Create the issue assigned to the **IC**, with the **PM** as its execution-policy review participant, and acceptance criteria in the description or `plan` document.
2. When the IC transitions the issue to `done`, Paperclip intercepts it: status becomes `in_review` and the issue is reassigned to the PM.
3. If the PM approves (transition to `done` with a comment), the issue actually completes — or advances to the next stage if one exists.
4. If the PM requests changes (transition to `in_progress` with a comment), Paperclip reassigns to the original IC at the **same** review stage.
5. When the IC resubmits, Paperclip routes it back to the same PM. The loop repeats until approval.

Because assignment changes are atomic, the active reviewer is typed, every decision is audited, and the runtime prevents the executor from self-approving, this is materially more reliable than an orchestrator prompt that polls two agents.

## How it maps to the K_St development loop

| Development loop stage | Paperclip mechanism |
|---|---|
| Idea → Spec / Plan | Issue + `plan` document with acceptance criteria |
| PM vet (of the spec) | Optional: plan review via `request_confirmation` or a review stage on the planning issue |
| IC build | Assignee works `in_progress`; leaves an evidence block on `done` |
| Review & test | Execution-policy **review stage** → PM (and QA for browser/functional checks) |
| Feedback → re-implement | Changes-requested transition → runtime reassigns to the IC at the same stage |
| Feature complete | Approval transition → `done` (or next stage) |

## Setting it up

Attach an execution policy naming the reviewer when you create the task (or when you convert a plan into tasks — see the `paperclip-converting-plans-to-tasks` skill). A minimal implementer→reviewer policy:

```json
POST /api/companies/{companyId}/issues
{
  "title": "Implement token refresh",
  "assigneeAgentId": "{ic-agent-id}",
  "description": "Acceptance criteria:\n- Refresh rotates on expiry\n- No plaintext secrets in logs",
  "executionPolicy": {
    "mode": "normal",
    "commentRequired": true,
    "stages": [
      {
        "type": "review",
        "participants": [
          { "type": "agent", "agentId": "{pm-agent-id}" }
        ]
      }
    ]
  }
}
```

Add an `approval` stage after the review stage when a human or manager must give final sign-off. Multiple reviewers can be listed per stage; the runtime picks one and always excludes the original executor.

## Shared state vs. agent memory

Do not try to synchronize the two agents' private memories. Keep private context role-specific and put cross-agent truth in durable artifacts:

| Concern | Canonical location |
|---|---|
| Weekly objective and sequencing | Weekly plan / parent issue |
| Session scope and acceptance criteria | The assigned Paperclip issue / `plan` document |
| Current branch, diff, and runtime | The issue execution workspace |
| Detailed review findings | `feedback.md` in the workspace |
| Routing and approval state | Paperclip `executionPolicy` / `executionState` |
| Handoff evidence | The required decision comment on the issue |
| IC implementation tactics | IC-private session/memory |
| PM review heuristics | PM instructions / private memory |

Both agents operate in the **same issue execution workspace**, so the PM reviews the exact branch and worktree the IC produced. Paperclip keeps resumable sessions keyed by agent and task, so the two keep separate memories while sharing the same issue and filesystem state. Do not add a parallel workspace-local handoff file — it duplicates Paperclip's first-class session state, comments, and documents, and risks stale or cross-issue context.

### feedback.md ownership

`feedback.md` is the durable, PM-owned record of review findings for an issue's workspace:

- The **PM** owns new findings and their `open`/`resolved` state. Each finding gets a stable ID (`F-1`, `F-2`, …), a severity, a location, and the concrete acceptance condition to clear it.
- The **IC** reports fix evidence by finding ID in its resubmission comment. It does **not** declare PM findings resolved.
- The **PM** verifies the evidence, flips findings to `resolved`, then approves or requests another cycle.
- Paperclip status controls routing. Neither agent should infer workflow state by parsing prose in `feedback.md`.

## Choosing the level of ceremony

Not every task needs the full loop. Right-size it:

| Signal | Policy to attach |
|---|---|
| Typo, config tweak, trivial fix | None — single IC pass, `commentRequired` backstop only |
| Small feature, single-file change | Review stage only (IC → PM) |
| Multi-file feature, schema change, new behavior | Review + approval (IC → PM → manager/human) |
| Ambiguous or speculative work | Vet the plan first (plan-review confirmation), then attach the loop |

## Compact role contracts

Keep the durable role prompts short; the weekly plan lives in the issue, not the prompt.

**IC:** Work only the assigned issue in its current execution workspace. Read the issue's acceptance criteria and linked plan once, then implement them. Run proportional validation and move to `done` with a concise summary, an evidence block (command + result per criterion), changed paths, and any addressed finding IDs. Do not self-review or approve.

**PM:** Review only assigned `in_review` issues. Compare the implementation against the plan, acceptance criteria, repository guidance, diff, and evidence. Record uniquely identified findings in `feedback.md`. If any blocking finding remains, request changes with the finding IDs and concrete acceptance conditions. If all gates pass, mark findings resolved and approve. Do not implement the feature or approve on claims alone.

## Rolling it out

Treat the first deployment as a pilot on 5–10 representative issues before expanding across projects. Per issue, capture: IC and PM run counts and cost, review-cycle count, time from assignment to approval, malformed-API-call/timeout count, human interventions, and any defects that escaped past PM approval. The automation is worthwhile only if it reduces human routing work without making **cost per approved issue** or escaped-defect rate materially worse — that, not tokens per heartbeat, is the unit to optimize. Add a human escalation rule after three unsuccessful review cycles, since execution policy loops until approval and exposes no cycle maximum.
