> **Artifact class:** Template (profile — pending charter enumeration; see Ledger L-010)
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** Humans and agents setting up a Standard-profile project
> **Origin:** kst_promptware

# Profile: Standard

**When to use:** Ongoing AI-assisted development across multiple sessions. You want structured session handoffs, the full PM/IC/QA workflow, always-apply rules, specialized review agents, and common skills.

**Automation level:** Agent runs structured sessions (PM/IC/QA loop), maintains session state files, delegates reviews to specialized subagents, and invokes skills on demand.

**Overhead:** Low. Setup ~20 minutes; ongoing maintenance is updating CONTEXT.md and PROGRESS.md per session cadence (see WORKFLOW.md §4).

---

## File Manifest

Includes everything in [Minimal](minimal.md) plus the additions below.

### Rules → `.claude/rules/`

| Source (kst_promptware) | Destination (your project) | Notes |
|---|---|---|
| `rules/common/coding-style.md` | `.claude/rules/coding-style.md` | From Minimal |
| `rules/common/git-workflow.md` | `.claude/rules/git-workflow.md` | From Minimal |
| `rules/common/testing.md` | `.claude/rules/testing.md` | |
| `rules/common/security.md` | `.claude/rules/security.md` | |
| `rules/common/performance.md` | `.claude/rules/performance.md` | |
| `rules/common/patterns.md` | `.claude/rules/patterns.md` | |
| `rules/<your-language>/README.md` | `.claude/rules/<language>.md` | Choose one: typescript, python, go. Populate stubs for your project. |

### Session Templates → `docs/`

| Source | Destination | Notes |
|---|---|---|
| `templates/WORKFLOW.md` | `docs/WORKFLOW.md` | Read-only reference; do not modify |
| `templates/CONTEXT.md` | `docs/CONTEXT.md` | Fill in project name, focus, key files |
| `templates/PROGRESS.md` | `docs/PROGRESS.md` | Fill in sprint goals and first tasks |
| `templates/HANDOFF.md` | `docs/HANDOFF.md` | Overwrite per handoff |
| `templates/general-engineering-standards.md` | `docs/general-engineering-standards.md` | Adapt to your stack |

### Agents → `.claude/agents/`

| Source | Destination | Notes |
|---|---|---|
| `agents/code-reviewer.md` | `.claude/agents/code-reviewer.md` | Add `model: claude-sonnet-4-6` if you want to pin |
| `agents/security-reviewer.md` | `.claude/agents/security-reviewer.md` | |

### Skills → `.claude/skills/`

| Source | Destination | Notes |
|---|---|---|
| `skills/session-handoff/SKILL.md` | `.claude/skills/session-handoff.md` | From Minimal |
| `skills/architecture-decision-records/SKILL.md` | `.claude/skills/architecture-decision-records.md` | Invoke as `/architecture-decision-records` |
| `skills/deep-research/SKILL.md` | `.claude/skills/deep-research.md` | Invoke as `/deep-research` |
| `skills/code-review/SKILL.md` | `.claude/skills/code-review.md` | Invoke as `/code-review` |

### Root file

| Source | Destination | Notes |
|---|---|---|
| *(create from template below)* | `AGENTS.md` | One screen; pointers only |

---

## AGENTS.md Template

```markdown
# AGENTS.md

Cold-start for AI agents. Read this file first; everything else is load-on-demand.

## Project
!!PROJECT_NAME!! — !!ONE_LINE_DESCRIPTION!!

## Session Start
Read `docs/CONTEXT.md` + `docs/PROGRESS.md` + `docs/WORKFLOW.md` at the start of every session.
Rules in `.claude/rules/` are loaded automatically.

## Quick Reference

| When you need to… | Read / Invoke |
|---|---|
| Orient to current work | `docs/CONTEXT.md` |
| Find active tasks | `docs/PROGRESS.md` |
| Hand off | `/session-handoff` |
| Review code | `/code-review` or delegate to `code-reviewer` agent |
| Security audit | Delegate to `security-reviewer` agent |
| Record an architecture decision | `/architecture-decision-records` |
| Research a topic | `/deep-research` |
| Understand the session protocol | `docs/WORKFLOW.md` |
| Check code standards | `docs/general-engineering-standards.md` |
| Git policy | `.claude/rules/git-workflow.md` |

## Agents Available
- `code-reviewer` — second-pass review before merge
- `security-reviewer` — security audit for auth, input handling, secrets

## Conduct
- `main` is read-only. Feature branches, merge via PR.
- Conventional Commits (lowercase). Stage specific files, never `git add -A`.
- Stop and escalate when: schemas change, auth/payment touched, architecture decisions needed, acceptance criteria are ambiguous.
```

---

## Setup Checklist

- [ ] Copied all files in the manifest above
- [ ] Filled in `docs/CONTEXT.md` — project name, current focus, key files, tech stack
- [ ] Filled in `docs/PROGRESS.md` — sprint goal and first 3–5 tasks
- [ ] Replaced all `!!PLACEHOLDER!!` tokens in all files
- [ ] Selected and populated a language rule stub (`rules/<language>/README.md`)
- [ ] Created `AGENTS.md` from the template above
- [ ] Verified the agent can cold-start from `AGENTS.md` → `docs/CONTEXT.md` in two reads
- [ ] Committed `.claude/`, `docs/`, and `AGENTS.md` to version control

## Upgrade Path

To move to Full, install the Boresight governance bundle and governance agents listed in [`full.md`](full.md).
