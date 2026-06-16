> **Artifact class:** Rule (common)
> **Maturity:** canonical
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** All agents at session start; always-apply
> **Origin:** kst_promptware

# Testing

## What to Test

Test **behavior observable at a boundary**, not implementation details. A test that breaks when you rename a private method is a bad test. A test that breaks when you change what the function returns is a good test.

Do not test:
- Private/internal functions directly
- Framework behavior (trust the framework)
- Trivial getters/setters with no logic

Always test:
- Public API contracts
- Business logic with multiple branches
- Error paths and edge cases
- Anything touched by money, auth, or data persistence

## Test Layers

| Layer | What it tests | When to add |
|---|---|---|
| Unit | Single function/module in isolation | Business logic, algorithms, transformations |
| Integration | Two or more modules together | DB queries, service-to-service, API handlers |
| E2E | Full user-facing flow | Critical happy paths only |

Push tests to the lowest layer that gives you confidence. Integration tests that test what a unit test could are slow and brittle.

## TDD Signal

Use TDD when: the logic is algorithmic, the acceptance criteria are clear, or you're not sure how to structure the code. Let the test design force a clean interface.

Skip TDD when: you're spiking an approach you'll likely discard, or the behavior is entirely UI/visual.

## Acceptance Criteria as Tests

Every acceptance check in a work order or feature spec should map to one or more tests. If an acceptance criterion can't be expressed as a test, it's not specific enough — refine the criterion before writing code.

## Test Quality Rules

- One assertion per test is a guideline, not a law. But each test should have one reason to fail.
- Test names describe the scenario and expected outcome: `returns empty list when user has no orders`, not `test getUserOrders`.
- Do not share mutable state between tests. Tests that depend on execution order are not tests.
- Mock at the boundary of your system (external APIs, DB). Do not mock things you own — test the real implementation.
- A flaky test is a failing test. Fix or delete it; do not skip it.

## Coverage

Coverage is a signal, not a target. 100% coverage does not mean 0 bugs. Aim for coverage of all meaningful branches in business logic. Do not write tests purely to hit a coverage number.
