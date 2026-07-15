> **Artifact class:** Template (catalog — pending charter enumeration; see Ledger L-010)
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** Humans and agents selecting library content
> **Origin:** kst_promptware

# Skills Library

Invokable workflows for Claude Code. Each skill is a bounded, reusable procedure invoked on demand for a specific task.

## Skill Catalog

| Skill | Profile | Description |
|---|---|---|
| [`architecture-decision-records`](architecture-decision-records/SKILL.md) | Standard | Capture architectural choices as lightweight ADR documents |
| [`multi-agent-design-exploration`](multi-agent-design-exploration/SKILL.md) | Standard | Explore a design decision with parallel stances, then synthesize |
| [`project-philosophy-document`](project-philosophy-document/SKILL.md) | Standard | Draft the "why before specs" philosophy doc for a new project |
| [`work-order-intake`](work-order-intake/SKILL.md) | Full | Convert a feature request into a draft Boresight work order |
| [`deep-research`](deep-research/SKILL.md) | Standard | Multi-source web research synthesized into a cited report |
| [`code-review`](code-review/SKILL.md) | Standard | Structured code review with severity-labelled findings |
| [`session-handoff`](session-handoff/SKILL.md) | Minimal | Write a clean HANDOFF.md for the next agent or session |
| [`pm-export`](pm-export/SKILL.md) | Full | Project governance artifacts into a tool-agnostic PM export (GitHub Issues / Linear / Jira) |

## How Skills Differ from Agents

- **Skills** — invoked for a single discrete task; produce a bounded output; then done
- **Agents** — persistent specialized role that handles a category of work throughout a session

Invoke a skill when the task is bounded and has a clear deliverable. Delegate to an agent when the role needs to persist across multiple exchanges.

## How to Install

1. Copy `SKILL.md` from a skill directory to `.claude/skills/<skill-name>.md` in your project.
2. The skill becomes invokable via `/<skill-name>` in Claude Code.
3. Alternatively, describe the task in natural language — Claude Code matches skills by their `description` field.

**Frontmatter** is validated against [`/schemas/skill.schema.json`](../schemas/skill.schema.json). The `name` field must match the filename stem exactly.

## Adding Skills

New skills follow this structure:
```
skills/
  <skill-name>/
    SKILL.md      # Frontmatter + invocation instructions (this is what gets copied)
    README.md     # Optional: authoring notes, customization guide, version history
```

`name` in frontmatter must be `kebab-case` matching the directory name.
