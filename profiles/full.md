> **Artifact class:** Template (profile — pending charter enumeration; see Ledger L-010)
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** Humans and agents setting up a Full-profile project
> **Origin:** kst_promptware

# Profile: Full

**When to use:** Complex, multi-agent, or long-lived projects where you need a complete audit trail, bounded delegation, and a specification system that survives context resets. You want every decision documented, every task governed by a work order, and every completion verifiable.

**Automation level:** Full Boresight specification-first system. Agents operate within a tier model (planner, builder, gatekeeper); all work is governed by work orders with closed-world reads/writes and machine-checkable acceptance criteria.

**Overhead:** Medium. Setup ~45 minutes; ongoing overhead is the work-order cycle (intake → gate → execute → review → reconcile). The payoff is an auditable artifact trail and bounded delegation that doesn't rely on conversation memory.

---

## File Manifest

Includes everything in [Standard](standard.md) plus the additions below.

### Boresight Governance Bundle → project root

These are **new files** in your project — do not copy the kst_promptware instance files directly (those are for this library, not your project). Instead, create new instances from these templates:

| Source (kst_promptware template) | Action |
|---|---|
| `charter.md` | Create your own `charter.md` from the structure (vision, users, problem, success criteria, non-goals, glossary). Replace all kst_promptware-specific content. |
| `constitution.md` | Copy `constitution.md` as-is. The constraints are universal. Amend via change order if your project needs deviations. |
| `templates/work-order.md` | Copy to `templates/work-order.md` in your project |
| `schemas/work-order.schema.json` | Copy to `schemas/work-order.schema.json` in your project |
| `schemas/skill.schema.json` | Copy to `schemas/skill.schema.json` in your project |
| `schemas/agent.schema.json` | Copy to `schemas/agent.schema.json` in your project |

**Create new (don't copy from kst_promptware):**

| File | Instructions |
|---|---|
| `capability-map.md` | Create fresh; seed with your project's first 3–5 features |
| `ledger.md` | Create fresh; seed with L-001 only (bootstrap paradox: your first WO predates the map) |
| `CHANGELOG.md` | Create fresh; start at v0.1.0 |
| `work-orders/` | Empty directory; work orders created during execution |
| `features/` | Empty directory; feature specs created by the planner agent |
| `decisions/` | Empty directory; ADRs filed here |
| `playbooks/` | Empty directory; operational procedures filed here |

### Rules → `.claude/rules/` (all Standard rules, plus)

All Standard rules apply. Add language stubs for every language in your stack:

| Source | Destination | Notes |
|---|---|---|
| `rules/typescript/README.md` | `.claude/rules/typescript.md` | If using TypeScript/JS |
| `rules/python/README.md` | `.claude/rules/python.md` | If using Python |
| `rules/go/README.md` | `.claude/rules/go.md` | If using Go |

Populate stubs for your project before committing.

### Governance Agents → `.claude/agents/`

| Source | Destination | Notes |
|---|---|---|
| `agents/planner.md` | `.claude/agents/planner.md` | Planner tier |
| `agents/builder.md` | `.claude/agents/builder.md` | Builder tier |
| `agents/gatekeeper.md` | `.claude/agents/gatekeeper.md` | Gatekeeper role |

All Standard agents (code-reviewer, security-reviewer) still apply.

### Development Agents → `.claude/agents/`

| Source | Destination | Notes |
|---|---|---|
| `agents/architect.md` | `.claude/agents/architect.md` | |
| `agents/spec-miner.md` | `.claude/agents/spec-miner.md` | |
| `agents/silent-failure-hunter.md` | `.claude/agents/silent-failure-hunter.md` | |

### Skills → `.claude/skills/`

All Standard skills apply. Add:

| Source | Destination | Notes |
|---|---|---|
| `skills/multi-agent-design-exploration/SKILL.md` | `.claude/skills/multi-agent-design-exploration.md` | |
| `skills/project-philosophy-document/SKILL.md` | `.claude/skills/project-philosophy-document.md` | |
| `skills/work-order-intake/SKILL.md` | `.claude/skills/work-order-intake.md` | Invoke as `/work-order-intake` |
| `skills/pm-export/SKILL.md` | `.claude/skills/pm-export.md` | Invoke as `/pm-export` |

---

## AGENTS.md Template

```markdown
# AGENTS.md

Cold-start for AI agents. Read this file first. Then read `constitution.md`. Then read the active work order.

## Project
!!PROJECT_NAME!! — !!ONE_LINE_DESCRIPTION!!

## The Two Rules That Override Everything
1. `main` is read-only for agents. Work on feature branches; merge via PR.
2. Read `constitution.md` before any governed work. Active work orders are in `work-orders/`; current status surface is `capability-map.md`.

## Session Start
Read `docs/CONTEXT.md` + `docs/PROGRESS.md` + `docs/WORKFLOW.md` at the start of sessions not involving a work order.
For work-order sessions: read `constitution.md` + the assigned work order.

## Quick Reference

| When you need to… | Read / Invoke / Delegate |
|---|---|
| Orient (no active WO) | `docs/CONTEXT.md` + `docs/PROGRESS.md` |
| Find active or queued work | `capability-map.md` + `work-orders/` |
| Convert a feature request to a WO | `/work-order-intake` |
| Execute a work order | Delegate to `builder` agent |
| Validate WO closeout | Delegate to `gatekeeper` agent |
| Plan features / cut WOs | Delegate to `planner` agent |
| Architecture decision | `/architecture-decision-records` or `architect` agent |
| Multi-option design choice | `/multi-agent-design-exploration` |
| Project philosophy | `/project-philosophy-document` |
| Code review | `/code-review` or `code-reviewer` agent |
| Security audit | `security-reviewer` agent |
| Find silent failures | `silent-failure-hunter` agent |
| Extract latent requirements | `spec-miner` agent |
| Research a topic | `/deep-research` |
| Hand off session | `/session-handoff` |
| Check open questions / decisions | `ledger.md` |
| Log an architecture decision | `decisions/` |

## When Docs Conflict
`constitution.md` wins for WO process norms. `charter.md` wins for vision and glossary. `AGENTS.md` wins for top-level rules.

## File Naming
Prefer kebab-case. Exceptions: well-known framework files (README.md, CONTEXT.md, WORKFLOW.md).
```

---

## Setup Checklist

- [ ] Copied all Standard-profile files
- [ ] Created project `charter.md` (from structure, not kst_promptware content)
- [ ] Copied `constitution.md` as-is
- [ ] Created `capability-map.md` (fresh, with project features)
- [ ] Created `ledger.md` (fresh, seeded with L-001)
- [ ] Created `CHANGELOG.md` (fresh, v0.1.0)
- [ ] Created empty directories: `work-orders/`, `features/`, `decisions/`, `playbooks/`
- [ ] Copied all agents and skills per manifest above
- [ ] Populated language rule stubs for your stack
- [ ] Replaced all `!!PLACEHOLDER!!` tokens
- [ ] Created `AGENTS.md` from the template above
- [ ] Verified agent can cold-start: `AGENTS.md` → `constitution.md` → active WO in 3 reads
- [ ] Committed everything to version control
- [ ] Cut WO-0001: your project's bootstrap work order
