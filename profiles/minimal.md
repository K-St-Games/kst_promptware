> **Artifact class:** Template (profile — pending charter enumeration; see Ledger L-010)
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** Humans and agents setting up a Minimal-profile project
> **Origin:** kst_promptware

# Profile: Minimal

**When to use:** Single-agent or short-lived projects. You want a structured way to hand off context and a small set of always-apply rules. Nothing more.

**Automation level:** Agent executes tasks in a disciplined way but with no formal session loop, no agents roster, and no specification system.

**Overhead:** Very low. Setup takes ~10 minutes; ongoing maintenance is reading and writing one HANDOFF.md per session.

---

## File Manifest

Copy these files from `kst_promptware` to your project. All paths are relative to your project root unless noted.

### Rules → `.claude/rules/`

| Source (kst_promptware) | Destination (your project) | Notes |
|---|---|---|
| `rules/common/coding-style.md` | `.claude/rules/coding-style.md` | Adjust naming conventions for your language |
| `rules/common/git-workflow.md` | `.claude/rules/git-workflow.md` | Adjust branch naming if needed |

### Session Layer → `docs/`

| Source | Destination | Notes |
|---|---|---|
| `templates/HANDOFF.md` | `docs/HANDOFF.md` | Overwrite per handoff — not an append |

### Skills → `.claude/skills/`

| Source | Destination | Notes |
|---|---|---|
| `skills/session-handoff/SKILL.md` | `.claude/skills/session-handoff.md` | Invoke as `/session-handoff` |

### Root file

| Source | Destination | Notes |
|---|---|---|
| *(create from template below)* | `AGENTS.md` | One screen; pointers only |

---

## AGENTS.md Template

Create `AGENTS.md` at your project root. Replace every `!!PLACEHOLDER!!`:

```markdown
# AGENTS.md

Cold-start for AI agents. Read this file first; everything else is load-on-demand.

## Project
!!PROJECT_NAME!! — !!ONE_LINE_DESCRIPTION!!

## Rules (always apply)
Rules in `.claude/rules/` are loaded automatically. Key rules:
- Coding style: `.claude/rules/coding-style.md`
- Git workflow: `.claude/rules/git-workflow.md`

## Quick Reference

| When you need to… | Read / Invoke |
|---|---|
| Hand off to the next agent or session | `/session-handoff` or `docs/HANDOFF.md` |
| Check code standards | `.claude/rules/coding-style.md` |
| Check git policy | `.claude/rules/git-workflow.md` |

## Conduct
- `main` is read-only. Work on feature branches, merge via PR.
- Conventional Commits (lowercase). Stage specific files — never `git add -A`.
- Stop and ask when: schemas change, auth/payment touched, architecture decisions needed.
```

---

## Setup Checklist

- [ ] Copied all files in the manifest above
- [ ] Created `AGENTS.md` from the template and replaced all `!!PLACEHOLDER!!` tokens
- [ ] Adjusted `.claude/rules/coding-style.md` for your language's naming conventions
- [ ] Verified no `!!PLACEHOLDER!!` tokens remain anywhere
- [ ] Committed the `.claude/` directory and `AGENTS.md` to version control

## Upgrade Path

To move to Standard, install the additional files listed in [`standard.md`](standard.md) without removing what's already installed.
