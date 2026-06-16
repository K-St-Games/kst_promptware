> **Artifact class:** Rule (common)
> **Maturity:** canonical
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** All agents at session start; always-apply
> **Origin:** kst_promptware

# Git Workflow

## Branches

- `main` is read-only for agents. All work happens on feature branches.
- Branch naming: `type/short-description` where type ∈ {`feature`, `fix`, `docs`, `refactor`, `chore`}
- One concern per branch. A branch that does two things is two branches that weren't separated.

## Commits

Use **Conventional Commits** (lowercase):

```
type(scope): short description

optional body explaining why, not what
```

Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `perf`, `style`

- `feat`: new capability visible to a user or caller
- `fix`: corrects a defect
- `docs`: documentation only
- `refactor`: behavior-neutral restructuring
- `chore`: tooling, deps, config — no production code

**Commit rules:**
- Stage specific files. Never `git add -A` or `git add .` — these risk committing unintended files.
- Each commit leaves the tree in a valid state (tests pass, build green, no broken references).
- One logical change per commit. "Fix X and also refactor Y" is two commits.
- Commit message body explains *why*, not *what*. The diff shows what.
- No generated `Co-Authored-By` lines unless the host repo policy requires them.

## Pull Requests

- PR scope matches the branch scope: one feature, fix, or concern.
- PR description includes: what changed, why, and how to verify it.
- Do not force-push a branch that another agent or human has checked out.
- Never force-push `main` under any circumstances.
- Resolve all merge conflicts before requesting review. If unsure how to resolve, escalate.

## Before Every Commit

1. `git diff --staged` — review what you're actually committing
2. No secrets, no debug logging, no `TODO` left unintentionally
3. Tests pass locally if the project has a test suite

## When Things Go Wrong

- Uncommitted mistake: `git restore <file>` (staged) or discard manually
- Wrong branch: create a new branch from the right base, cherry-pick, abandon the wrong one — do not force-reset without explicit human approval
- Committed secret: rotate the secret first, then clean history — ask for human guidance on the cleanup procedure
