---
name: code-review
description: Run a structured code review on a diff, PR, or set of files. Produces severity-labelled findings organized by concern. Invoke before merging significant changes or when you want a systematic second pass.
profile: standard
maturity: canonical
origin: kst_promptware
---

# Code Review

Structured review with severity-labelled findings. Covers correctness, security, simplification, performance, and test coverage in a consistent, scannable format.

## When to Invoke

- Before merging a non-trivial PR
- After a significant refactor to catch regressions
- When asked "does this look right?" about complex logic
- As a QA-hat step in the PM/IC/QA workflow (WORKFLOW.md §2.3)

## Procedure

**Step 1 — Establish scope**

Confirm:
- What is being reviewed? (specific files, a diff, a PR)
- What changed and why? (context prevents false positives)
- Are there known risks? (e.g., "this touches the payment flow")
- What rules apply? (check for project rule files in `.claude/rules/`)

**Step 2 — Read the code**

Read completely before writing any findings. Do not comment on the first file while the others are unread — patterns that look wrong in isolation may be correct in context.

**Step 3 — Review in concern order**

Work through concerns in this priority order — a critical correctness bug matters more than a style issue:

1. **Correctness** — Does it do what it claims? Edge cases (null, empty, zero, concurrent, large input)?
2. **Security** — Input sanitization, auth checks, secrets, injection. (Escalate to `/security-reviewer` for a full audit if this is a security-sensitive change.)
3. **Simplification** — Is there dead code, over-engineering, or unnecessary abstraction?
4. **Performance** — N+1 queries, blocking I/O, unbounded result sets, missing indexes?
5. **Tests** — Do tests cover the meaningful branches? Is the failure path tested?
6. **Style** — Only flag violations of documented project standards.

**Step 4 — Write findings**

Each finding: location, severity, description, suggested fix (or "discuss").

```markdown
## Code Review: <scope>

### CRITICAL
- [`file.ts:42`] SQL query concatenates user input — use parameterized query. Suggested: `db.query('SELECT * FROM users WHERE id = $1', [userId])`

### HIGH
- [`service.ts:118`] Error from `sendEmail()` is discarded — callers have no way to know the email failed. Suggested: propagate or log.

### MEDIUM
- ...

### LOW / NIT
- ...

---

### Summary
N critical · N high · N medium · N low

**Verdict:** APPROVE | REQUEST_CHANGES | DISCUSS

<If APPROVE with notes: "Approve pending resolution of HIGH items." If DISCUSS: describe the specific question.>
```

**Severity definitions:**

| Severity | Meaning |
|---|---|
| CRITICAL | Will cause data loss, security breach, or production outage. Block merge. |
| HIGH | Likely to cause errors under real conditions. Fix before merge. |
| MEDIUM | Will cause problems eventually. Fix soon. |
| LOW | Improvement opportunity; deferrable. |
| NIT | Optional polish. Include only if the diff is otherwise clean. |

## What Not to Flag

- Working code that could be "more elegant" by your preference
- Missing features not in the acceptance criteria
- Patterns that are unusual but clearly deliberate
- Style choices not documented in the project's rules

If you're unsure whether something is a bug or an intentional choice, ask rather than flag.
