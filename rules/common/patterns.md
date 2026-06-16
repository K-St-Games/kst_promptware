> **Artifact class:** Rule (common)
> **Maturity:** canonical
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** All agents at session start; always-apply
> **Origin:** kst_promptware

# Patterns

Structural principles that hold regardless of language or framework. Apply these as defaults; deviate deliberately with a comment explaining why.

## Composition Over Inheritance

Prefer composing behavior from small, focused units over deep inheritance hierarchies. Inheritance creates tight coupling to a parent's implementation. Composition lets you swap parts.

## Single Responsibility

Every module, class, and function has one reason to change. When you find yourself writing "and also" in a function name or docstring, split it.

## Dependency Injection

Pass dependencies in, don't reach out for them. A function that instantiates its own database connection is untestable and hard to reconfigure. A function that receives a database connection as an argument is testable and flexible.

This applies at every scale: functions receive parameters, classes receive collaborators through the constructor, services receive their dependencies through config or service locators.

## Fail Fast

Validate preconditions at the top of a function, not buried inside it. An error thrown early with a clear message is better than corrupted state discovered late.

Guard clauses > nested conditionals:

```
// Prefer
if (!user) throw new Error('user required')
if (!user.isActive) throw new Error('user must be active')
doTheActualWork(user)

// Over
if (user) {
  if (user.isActive) {
    doTheActualWork(user)
  }
}
```

## Make Invalid State Unrepresentable

Use the type system (or validation at the boundary) to make impossible states impossible to construct, rather than checking for them everywhere. An optional field that is sometimes required based on another field is a design smell — model it as a union or a separate type.

## Pure Functions Where Possible

Functions with no side effects and deterministic output are trivially testable, safely parallelizable, and trivially cacheable. Push side effects to the edges of your system. The core logic should be pure functions; the entry points and adapters handle I/O.

## Strangler Fig for Rewrites

When replacing an existing system, don't rewrite from scratch. Build the new behavior alongside the old, route traffic incrementally, delete the old once the new is verified. Big-bang rewrites fail.

## Do Not Prematurely Extract

A pattern that appears twice is a coincidence. Three times is a pattern worth extracting. Extract the abstraction after you understand the shape of the third use case — not before, because the first two uses won't tell you what varies and what's constant.
