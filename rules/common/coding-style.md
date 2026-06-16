> **Artifact class:** Rule (common)
> **Maturity:** canonical
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** All agents at session start; always-apply
> **Origin:** kst_promptware

# Coding Style

Language-agnostic. Language-specific rules extend and override these.

## Core Principles

**KISS** — Prefer the simplest solution that actually works. If you need a comment to explain what the code does, the code is too complex. Simplify first.

**DRY** — Extract repeated logic when you see it the third time, not the second. Premature abstraction is the real enemy; duplication is survivable.

**YAGNI** — Do not build for hypothetical future requirements. Three similar lines is better than a premature abstraction. If the requirement isn't in the acceptance criteria, it doesn't exist.

## Files and Functions

- Prefer many small files over few large files.
- Target file size: 200–400 lines. Hard ceiling: 800 lines. If you're approaching the ceiling, the file has more than one responsibility.
- Function length: 50 lines max. If a function is longer, extract until each function does one thing.
- Nesting depth: 4 levels max. Flatten with early returns, guard clauses, and extracted functions.

## Naming

- Variables and functions: `camelCase`
- Types, classes, components: `PascalCase`
- Constants and env vars: `UPPER_SNAKE_CASE`
- Booleans: prefix with `is`, `has`, `should`, `can` — `isReady`, `hasError`
- Event handlers: prefix with `handle` or `on` — `handleSubmit`, `onError`
- Names describe what, not how. `getUserById` not `fetchUserFromDbWithQuery`.

## Immutability

Prefer creating new objects over mutating existing ones. Mutation is the most common source of unexpected behavior in concurrent and asynchronous code.

## Error Handling

Handle errors at system boundaries — user input, external APIs, file I/O, database calls. Do not re-wrap errors that are already handled. Do not swallow errors silently. An empty catch block requires a comment explaining why.

## Comments

Write no comments by default. Write a comment only when the **why** is non-obvious: a hidden constraint, a subtle invariant, a workaround for a specific bug. If removing the comment wouldn't confuse a future reader, don't write it. Never write comments that describe what the code does — well-named identifiers do that.

## Pre-Completion Checklist

Before marking a task complete, verify:
- [ ] No hardcoded values that belong in config or environment variables
- [ ] No mutation of shared state without explicit intent
- [ ] Error paths return or throw — never fall through silently
- [ ] No `TODO` or `FIXME` comments committed to main
- [ ] Names are accurate to current behavior (rename if you changed the behavior)
