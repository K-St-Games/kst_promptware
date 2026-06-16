> **Artifact class:** Template (catalog — pending charter enumeration; see Ledger L-010)
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** Humans and agents selecting library content
> **Origin:** kst_promptware

# Rules Library

Always-apply guidelines that agents load at session start. Rules describe **how to work**, not **what to build**.

## Structure

```
rules/
├── common/            # Language-agnostic; always applicable
│   ├── coding-style.md
│   ├── testing.md
│   ├── security.md
│   ├── git-workflow.md
│   ├── performance.md
│   └── patterns.md
├── typescript/        # Extends common; TypeScript/JS-specific
├── python/            # Extends common; Python-specific
└── go/                # Extends common; Go-specific
```

## How Rules Differ from Skills and Agents

- **Rules** — passive guidelines always in context; the agent reads and applies them throughout a session
- **Skills** — active workflows invoked on demand for a specific task (`/skill-name`)
- **Agents** — scoped subagents delegated a specific role for a bounded task

## How to Install

Claude Code loads rule files from `.claude/rules/` automatically. Copy the rule files you want into your project's `.claude/rules/` directory.

Alternatively, paste rule file contents into your project's `CLAUDE.md` if you prefer a single file.

**Always start with `common/`** — the language-specific rules extend and override the common defaults; don't install language-specific rules without the common foundation.

## Choosing Rules

| Profile | Rules to install |
|---|---|
| Minimal | `common/coding-style.md`, `common/git-workflow.md` |
| Standard | All 6 common rules + one language stack |
| Full | All common + all relevant language stacks |

See [`/profiles/`](../profiles/) for complete manifests.

## Extending Rules

Language stubs (`typescript/`, `python/`, `go/`) are starting points. Open them, fill in your project's actual conventions, and commit them alongside the common rules. The stub sections tell you what to document.

To add a new language: copy a stub README, add a section per concern, and reference `rules/common/coding-style.md` as the base.
