# AI Agent Dev Docs — v4 (codename: Boresight)

A specification-first software factory for AI-assisted development: product intent is compiled into bounded, auditable **work orders** whose completion can be validated, escalated, and reconciled without relying on chat memory. The v3 session framework (PM/IC/QA loop, context handoffs) remains operational as a companion layer.

**Cold-start for AI agents:** [`AGENTS.md`](AGENTS.md).

## Start Here

1. **Read the vision and glossary:** [`charter.md`](charter.md) — what this system is and the nouns it uses.
2. **Read the norms:** [`constitution.md`](constitution.md) — mutation rights, state machine, definition of done.
3. **Find the work:** [`capability-map.md`](capability-map.md) — status surface; orders live in [`work-orders/`](work-orders/).
4. **Read the contribution rules:** [`guides/contributing.md`](guides/contributing.md) — git workflow and PR policy.
5. **Run a session using the companion framework:** [`templates/README.md`](templates/README.md) — PM/IC/QA loop, cadence, compaction.

A full README rewrite is queued as WO-0005; this page is the reconciled v4 identity plus the existing docs map.

---

## Docs Map

### Work-order system (v4 core)

- [Charter](charter.md) — vision, users, non-goals, glossary, artifact classes.
- [Constitution](constitution.md) — norms, closed-world reads/writes, state machine, routing rules.
- [Capability map](capability-map.md) — derived status surface for features and orders.
- [Ledger](ledger.md) — open questions and deferred decisions.
- [Work-order template](templates/work-order.md) + [frontmatter schema](schemas/work-order.schema.json).
- [Validation playbook](playbooks/validation-checklist.md) — closeout checklist for `review → complete`.
- [Changelog](CHANGELOG.md) — framework version history.

### Operational guides (`guides/`)

Day-to-day rules and procedures:
- [Canonical git workflow](guides/contributing.md) — branching, commit format (Conventional Commits), PR checklist.
- [Git quick reference](guides/git_procedures.md) — short CLI cheat sheet.
- [Microservice workflow guide](guides/microservice-workflow-guide.md) — progressive deployment.

### Framework templates (`templates/`)

Canonical framework for agent-assisted projects. Copy into a new project's `docs/`.
- [Framework overview](templates/README.md)
- [Session workflow](templates/WORKFLOW.md) — PM/IC/QA, cadence, compaction, lightweight track.
- [CONTEXT.md template](templates/CONTEXT.md), [PROGRESS.md template](templates/PROGRESS.md), [HANDOFF.md template](templates/HANDOFF.md)
- [General engineering standards](templates/general-engineering-standards.md)
- [Devlog template](templates/Devlog_TEMPLATE.md), [Project plan template](templates/ProjectPlan/)

### Agent library (`agents/`, `skills/`, `rules/`, `profiles/`)

Selectable components for downstream projects. Install via one of three profiles:

- [**Minimal**](profiles/minimal.md) — 2 rules + 1 skill + HANDOFF.md + AGENTS.md template. For single-agent, low-overhead projects.
- [**Standard**](profiles/standard.md) — All common rules + full v3 session suite + 2 agents + 4 skills. For ongoing AI-assisted development.
- [**Full**](profiles/full.md) — Everything + Boresight governance bundle + all 8 agents + all 8 skills. For specification-first, multi-agent projects.

**Catalog:**
- [Rules library](rules/README.md) — Always-apply guidelines. `rules/common/` (6 files) + language stubs (TypeScript, Python, Go).
- [Agents library](agents/README.md) — 8 curated Claude Code subagent definitions: 5 development agents (architect, code-reviewer, security-reviewer, spec-miner, silent-failure-hunter) + 3 governance agents (planner, builder, gatekeeper).
- [Skills library](skills/README.md) — 8 invokable skills: ADR, multi-agent design exploration, project philosophy, work-order intake, deep research, code review, session handoff, PM export.
- [Schemas](schemas/) — `skill.schema.json`, `agent.schema.json` (in addition to `work-order.schema.json`).

### Technical suggestions (`technical-suggestions/`)

- [Tech stack recommendations](technical-suggestions/tech-stack-recommendations.md) — Godot vs. Phaser decision matrix.
- [Phaser project starter](technical-suggestions/phaser-project-template.md).

### Research & memos (`references/`)

Background and proposals, not operational:
- [References index](references/README.md)
- [Claude recommendations](references/claude-recommendations.md)
- [Cursor recommendations](references/cursor-recommendations.md)
- [The case for custom AI tooling](references/the-case-for-custom-ai-tooling.md)
- [Improvement proposal 052526](references/improvement-proposal-052526.md) — partially integrated; see header for status.

### Archive (`archive/`)

Deprecated v01/v02 execution state and earlier framework versions. Not for active use — see [archive README](archive/README.md).

---

## What This Repository Provides

- **A promptware library** — curated rules, agents, skills, and profiles that teams copy into their own repos at the complexity level they need.
- **The Boresight v4 specification system** — a work-order framework for bounded, auditable AI-assisted development.
- **The v3 session framework** — PM/IC/QA loop, context management, handoff protocol (companion layer).
- **Engineering standards** that work across web frameworks.

## Who This Is For

- Solo developers and small teams using AI agents for day-to-day implementation who want a structured, composable starting point.
- Teams building their own promptware practices who want a governed upstream source to fork from.
- Agents themselves — this repo is designed to be cold-started from `AGENTS.md` and navigated load-on-demand.
