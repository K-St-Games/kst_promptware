# AGENTS.md

Cold-start file for AI agents working in this repository. Keep this short — everything else is load-on-demand.

## What this repo is

A **documentation-only** repository, v4 (codename Boresight): a specification-first work-order system for agent-executable development, plus the v3 session framework as a companion layer. No application code.

## The two rules that override everything

1. **`main` is read-only for agents.** Work happens on feature branches. Merge via PR. Full procedure: [`guides/contributing.md`](guides/contributing.md). Branch naming: `type/agent-task-description` where type ∈ {`feature`, `fix`, `docs`, `refactor`}.
2. **Read [`constitution.md`](constitution.md) before any governed work.** Active work orders live in [`work-orders/`](work-orders/); current status surface is [`capability-map.md`](capability-map.md).

## Load on demand

Don't read these reflexively — fetch when relevant.

| When you need to… | Read |
|-------------------|------|
| Understand vision, glossary, artifact classes | [`charter.md`](charter.md) |
| Know the norms, state machine, definition of done | [`constitution.md`](constitution.md) |
| Find active or queued work | [`capability-map.md`](capability-map.md) + [`work-orders/`](work-orders/) |
| Check open questions / deferred decisions | [`ledger.md`](ledger.md) |
| Cut a new work order | [`templates/work-order.md`](templates/work-order.md) + [`schemas/work-order.schema.json`](schemas/work-order.schema.json) |
| Commit, push, open a PR | [`guides/contributing.md`](guides/contributing.md) |
| Write a tiered spec or roadmap | [`guides/tier-epic-specification-framework.md`](guides/tier-epic-specification-framework.md) |
| Log architecture decisions | [`skills/architecture-decision-records/SKILL.md`](skills/architecture-decision-records/SKILL.md) — invoke as `/architecture-decision-records` |
| Explore a design with multiple AI agents | [`skills/multi-agent-design-exploration/SKILL.md`](skills/multi-agent-design-exploration/SKILL.md) — invoke as `/multi-agent-design-exploration` |
| Write a project philosophy doc | [`skills/project-philosophy-document/SKILL.md`](skills/project-philosophy-document/SKILL.md) — invoke as `/project-philosophy-document` |
| Quick git commands | [`guides/git_procedures.md`](guides/git_procedures.md) |
| Deploy a service progressively | [`guides/microservice-workflow-guide.md`](guides/microservice-workflow-guide.md) |
| Code style / engineering baseline | [`templates/general-engineering-standards.md`](templates/general-engineering-standards.md) |
| Run a session (PM / IC / QA loop, cadence, compaction) | [`templates/WORKFLOW.md`](templates/WORKFLOW.md) |
| Hand off mid-task | [`templates/HANDOFF.md`](templates/HANDOFF.md) |
| Background research / philosophy | [`references/README.md`](references/README.md) |
| Install this library into a new project | [`profiles/`](profiles/) — Minimal / Standard / Full manifests |
| Apply always-on coding and workflow rules | [`rules/common/`](rules/common/) + language stubs in `rules/typescript/`, `rules/python/`, `rules/go/` |
| Run a review with a specialized subagent | [`agents/`](agents/) — code-reviewer, security-reviewer, architect, spec-miner, silent-failure-hunter |
| Execute a Boresight work order | [`agents/builder.md`](agents/builder.md) or [`agents/planner.md`](agents/planner.md) |
| Convert a feature request to a work order | [`skills/work-order-intake/SKILL.md`](skills/work-order-intake/SKILL.md) |
| Research and synthesize a topic | [`skills/deep-research/SKILL.md`](skills/deep-research/SKILL.md) |
| Write a structured code review | [`skills/code-review/SKILL.md`](skills/code-review/SKILL.md) |
| Write a session handoff | [`skills/session-handoff/SKILL.md`](skills/session-handoff/SKILL.md) |

## Repository structure

```
/
├── AGENTS.md                     # This file — cold-start only
├── README.md                     # Human-facing overview
├── charter.md                    # North star: vision, glossary, artifact classes
├── constitution.md               # North star: norms, state machine, definition of done
├── capability-map.md             # Derived status surface
├── ledger.md                     # Open questions, deferred decisions (append-only)
├── CHANGELOG.md                  # Framework version history (v01 → v4)
├── LICENSE                       # MIT
├── work-orders/                  # Bounded execution units (WO-XXXX.md)
├── schemas/                      # Frontmatter validation (JSON Schema)
├── features/  decisions/  playbooks/  commands/   # Bundle directories (filling via WO-0002+)
├── templates/                    # work-order.md (bundle) + v3 session framework (companion layer)
├── guides/                       # Operational protocols (git, deployment)
├── profiles/                     # Installation profiles: Minimal / Standard / Full
├── rules/                        # Always-apply guidelines: common/ + language stubs
├── agents/                       # Curated Claude Code subagent definitions
├── skills/                       # Invokable Claude Code skills
├── references/                   # Research, analysis memos — not operational
├── technical-suggestions/        # Tech stack recommendations
└── archive/                      # Deprecated v01/v02 + old framework files
```

## When docs conflict

[`constitution.md`](constitution.md) wins for work-order process norms. [`charter.md`](charter.md) wins for vision and glossary. [`guides/contributing.md`](guides/contributing.md) wins for git policy. [`templates/WORKFLOW.md`](templates/WORKFLOW.md) wins for session protocol. This file (`AGENTS.md`) wins for top-level rules.

## File naming

Prefer **kebab-case**: `microservice-workflow-guide.md`, not `Microservice Workflow Guide.md`. Exceptions: well-known framework files (`README.md`, `CONTEXT.md`, `WORKFLOW.md`, etc.).

## Repo-specific notes

- This is docs-only — there is no test suite. Protocols that mandate "run tests before commit" don't apply here; substitute markdown link checks / cross-reference validation.
- Cross-references between docs are load-bearing. When you rename or move a file, grep the repo for references and update them.
