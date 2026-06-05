# AGENTS.md

Cold-start file for AI agents working in this repository. Keep this short — everything else is load-on-demand.

## What this repo is

A **documentation-only** repository: guides, templates, and protocols for AI-assisted software development. No application code.

## The one rule that overrides everything

**`main` is read-only for agents.** Work happens on feature branches. Merge via PR. Full procedure: [`guides/contributing.md`](guides/contributing.md).

Branch naming: `type/agent-task-description` where type ∈ {`feature`, `fix`, `docs`, `refactor`}.

## Load on demand

Don't read these reflexively — fetch when relevant.

| When you need to… | Read |
|-------------------|------|
| Commit, push, open a PR | [`guides/contributing.md`](guides/contributing.md) |
| Write a tiered spec or roadmap | [`guides/tier-epic-specification-framework.md`](guides/tier-epic-specification-framework.md) |
| Log architecture decisions | See `architecture-decision-log` skill — lightweight ADR format |
| Explore a design with multiple AI agents | See `multi-agent-design-exploration` skill — parallel proposals + synthesis |
| Write a project philosophy doc | See `project-philosophy-document` skill — the "why" before specs |
| Quick git commands | [`guides/git_procedures.md`](guides/git_procedures.md) |
| Deploy a service progressively | [`guides/microservice-workflow-guide.md`](guides/microservice-workflow-guide.md) |
| Code style / engineering baseline | [`templates/general-engineering-standards.md`](templates/general-engineering-standards.md) |
| Start a new project using the framework | [`templates/README.md`](templates/README.md) |
| Run a session (PM / IC / QA loop, cadence, compaction) | [`templates/WORKFLOW.md`](templates/WORKFLOW.md) |
| Hand off mid-task | [`templates/HANDOFF.md`](templates/HANDOFF.md) |
| Background research / philosophy | [`references/README.md`](references/README.md) |

## Repository structure

```
/
├── AGENTS.md                     # This file — cold-start only
├── README.md                     # Human-facing overview
├── guides/                       # Operational protocols (git, deployment)
├── templates/                    # Canonical framework (was v3.0)
│   ├── README.md                 # Framework overview
│   ├── WORKFLOW.md               # PM/IC/QA loop, cadence, compaction
│   ├── CONTEXT.md                # State + decision log template
│   ├── PROGRESS.md               # Active sprint template
│   ├── HANDOFF.md                # Session transition template
│   ├── general-engineering-standards.md
│   ├── Devlog_TEMPLATE.md
│   └── ProjectPlan/
├── references/                   # Research, analysis memos — not operational
├── technical-suggestions/        # Tech stack recommendations
└── archive/                      # Deprecated v01/v02 + old framework files
```

## When docs conflict

[`guides/contributing.md`](guides/contributing.md) wins for git policy. [`templates/WORKFLOW.md`](templates/WORKFLOW.md) wins for session protocol. This file (`AGENTS.md`) wins for top-level rules.

## File naming

Prefer **kebab-case**: `microservice-workflow-guide.md`, not `Microservice Workflow Guide.md`. Exceptions: well-known framework files (`README.md`, `CONTEXT.md`, `WORKFLOW.md`, etc.).

## Repo-specific notes

- This is docs-only — there is no test suite. Protocols that mandate "run tests before commit" don't apply here; substitute markdown link checks / cross-reference validation.
- Cross-references between docs are load-bearing. When you rename or move a file, grep the repo for references and update them.
