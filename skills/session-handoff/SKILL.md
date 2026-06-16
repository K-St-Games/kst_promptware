---
name: session-handoff
description: Write a clean HANDOFF.md to transfer context to the next agent or session. Invoke at the end of a session, before switching roles (PM → IC → QA), or before a long pause in work.
profile: minimal
maturity: canonical
origin: kst_promptware
---

# Session Handoff

Produces a single high-density `HANDOFF.md` that lets the next agent or session cold-start without reading the full conversation history.

`HANDOFF.md` is an **overwrite**, not an append. The previous handoff survives in git history. This is a live transition note, not a log.

## When to Invoke

- Ending a session and handing off to a different agent
- Switching perspectives (e.g., IC work done; handoff to QA hat)
- Pausing work for more than a few hours
- Any time you want the next agent to start from a clean, current context

## Procedure

**Step 1 — Assess current state**

Before writing, read:
- `CONTEXT.md` — current implementation state
- `PROGRESS.md` — active sprint and task status
- Recent commit messages (`git log --oneline -5`)
- Any error messages, test failures, or blockers active right now

**Step 2 — Write the HANDOFF.md**

Overwrite `docs/HANDOFF.md` (or wherever the project keeps it) with:

```markdown
# Handoff — <YYYY-MM-DD HH:MM>

## Status in One Sentence
<What was accomplished this session and where things stand now>

## What's Done
- <Completed item with commit ref if applicable>
- <Completed item>

## What's Next (in order)
1. <Most important next action — specific enough to execute without asking>
2. <Next action>
3. <Next action>

## Active Blockers
- <Blocker: what it is, what's needed to unblock it>
- (None) if unblocked

## Context the Next Agent Must Know
- <Non-obvious fact that would cause the next agent to make a wrong decision if they didn't know it>
- <Any decision made this session that isn't in CONTEXT.md yet>
- <Any workaround or temporary state>

## Files Actively Being Changed
- `path/to/file.ts` — <what state it's in, e.g., "mid-refactor, tests failing">

## Where to Start
<Exact first action: which file to open, which command to run, which task to claim in PROGRESS.md>
```

**Step 3 — Update PROGRESS.md if needed**

If tasks changed status during this session and PROGRESS.md hasn't been updated:
- Mark completed tasks `[x]`
- Mark in-progress tasks `[/]`
- Mark blockers `[B]`

See WORKFLOW.md §4 for update cadence — don't over-update.

**Step 4 — Commit**

```bash
git add docs/HANDOFF.md docs/PROGRESS.md
git commit -m "docs: session handoff <YYYY-MM-DD>"
```

## Quality Bar

The handoff is complete when a new agent can read only `HANDOFF.md` and know:
- Exactly where to start (the first action)
- What not to undo (what's complete and working)
- What's broken or in progress (so they don't build on a broken state)
- Any non-obvious context (so they don't repeat a mistake or re-litigate a settled decision)

If a new agent reading this HANDOFF.md would still need to read the conversation history to know what to do, the handoff is incomplete.
