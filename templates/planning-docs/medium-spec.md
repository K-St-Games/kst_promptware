# Medium Spec Template

Default weight for a contained feature or module. Escalate to the [heavy spec](heavy-spec.md) when the criteria in §6 of the toolkit apply: >3 sessions, contracts/schemas/auth/money, known regression seams, or provable-to-others exit.

---

# [Feature / Module Name]

**Status:** [draft / in review / live / superseded]
**Relates to:** [phase doc or weekly slice, if applicable]
**Branch / scope fence:** [e.g. `tools/` only; no `api/` or `src/`]

## Goal

What this feature does, who it's for, what experience it enables. One paragraph.

## Non-goals

- What this spec deliberately does not cover.
- What a reader might expect but should not.

## Approach

Numbered items or a short description of how it gets built. Concrete enough to execute.

### Phase 1 — [Name]
- [ ] [item: problem → file/line → fix or approach]
- [ ] [item]

### Phase 2 — [Name]
- [ ] [item]

## Working constraints

- Scope fences, parallel-branch conflicts, commit expectations.
- Anything another agent working nearby should know.
- Environment or config quirks.

## Critical review notes

Appended by a reviewing agent before implementation begins. Findings that modify or gate the plan. A spec without this section has not been reviewed.

---

## Done when

- [ ] 3–6 observable, testable checks. Each should be verifiable without asking the author.
