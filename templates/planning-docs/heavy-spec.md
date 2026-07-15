# Heavy Spec Template

Escalate from the [medium spec](medium-spec.md) when any of these are true:

1. Work spans **> 3 sessions** or multiple agents in parallel
2. It touches **contracts, schemas, persisted data, auth/security, or money**
3. It sits on a **known regression seam** (locally-correct changes invisible to narrowly-scoped guards)
4. Exit must be **provable to someone else** (release gates, collaborator handoff)

---

# [Feature / Module Name] — Heavy Spec

**Status:** [draft / in review / live / superseded]
**Relates to:** [phase doc or weekly slice]
**Branch / scope fence:** [e.g. `api/` and `types/` only; no `ui/`]

## Goal

What this feature does, why it justifies the heavy weight. One or two paragraphs.

## Non-goals

- What this spec deliberately does not cover.

## Decided — do not reopen

| Decision | Resolution |
|---|---|
| Example: Use PostgreSQL, not SQLite | Scalability requirements; existing infra runs PG |
| Example: No auth in v1 | Wrapped in the existing VPS auth layer; per-user auth deferred |

## Workstreams / Slices

### W1 — [Name]
**Exit criteria:** [checkable, testable outcome for the whole workstream]

| Slice | Description | Files to touch | Exit criterion |
|---|---|---|---|
| 1.1 | Brief description | [paths] | [checkable] |
| 1.2 | Brief description | [paths] | [checkable] |

**Order / parallelism notes:** [can W1.2 start before W1.1 lands?]

### W2 — [Name]
...

## Open questions

| # | Question | Blocks | Resolved? |
|---|---|---|---|
| | | | |

## Working constraints

- Scope fences, parallel-branch conflicts, commit expectations.
- Environment or config requirements.
- Anything that would cause a wrong decision if unknown.

## Critical review notes

Appended by a reviewing agent before implementation begins. Findings that modify or gate the plan. A spec without this section has not been reviewed.

---

## Done when

- [ ] [Phase-level exit checks — not just slice-level, but the feature as a whole]
- [ ] [Regression gates: existing tests green, no dead code left behind]
- [ ] [Contract boundaries proven: adapters round-trip, schema migrations work, etc.]
- [ ] [Heavy-spec exit provable to someone who hasn't read the spec]
