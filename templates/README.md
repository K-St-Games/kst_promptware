# Agent Documentation Framework

**Owner:** Workflow Architect
**Status:** Canonical
**Last Verified:** 2026-05-25

<!-- STALE CHECK: if Last Verified is more than 6 months old, verify file list and links before treating as authoritative. -->

> **Companion-layer notice (2026-06-12):** this v3 session framework now operates as a **companion layer** under the v4 work-order system (codename Boresight) — see [`/charter.md`](../charter.md) and [`/capability-map.md`](../capability-map.md). It remains operational and unchanged for session execution (CONTEXT/PROGRESS/HANDOFF/WORKFLOW). Full integration is deferred — Ledger L-008. The bundle's [`work-order.md`](./work-order.md) template also lives in this directory (Constitution §4.2).

A minimalist, low-overhead documentation system for AI-assisted software development. This was previously labeled "v3.0" — within the session layer it remains the canonical session framework. (Earlier versions live in [`/archive`](../archive/).)

It is opinionated about two things:
1. **Token footprint** — context files have strict size budgets and trigger-based compaction.
2. **Handoff friction** — every session transition has a single high-density document (`HANDOFF.md`).

---

## Core Concepts

### PM → IC → QA Perspective Loop

Three "hats" any agent (or human) cycles through:
- **PM (Strategic Planner):** scope, sequencing, acceptance criteria.
- **IC (Tactical Builder):** implementation matching the plan.
- **QA (Quality Verifier):** validates against criteria, runs tests, logs results.

For trivial tasks, skip the loop — see WORKFLOW.md "Lightweight Track."

### Trigger-Based Compaction

Files have visible size budgets. Compact only when a trigger fires (4th handoff entry, completed sprint, etc.) — not at the end of every session. Full details in [WORKFLOW.md](./WORKFLOW.md) Section 5.

---

## Files in This Folder

| File | Purpose | Budget |
|------|---------|--------|
| [README.md](./README.md) | This file — framework overview | Static |
| [WORKFLOW.md](./WORKFLOW.md) | Session protocol, cadence, compaction, escalation | Static |
| [CONTEXT.md](./CONTEXT.md) | Implementation state + sliding handoff log + decision log | < 100 lines |
| [PROGRESS.md](./PROGRESS.md) | Active sprint + backlog | < 150 lines |
| [HANDOFF.md](./HANDOFF.md) | High-density transition note (overwritten per handoff) | Single page |
| [general-engineering-standards.md](./general-engineering-standards.md) | Code style, naming, commit format | Reference |
| [Devlog_TEMPLATE.md](./Devlog_TEMPLATE.md) | Optional running development log | n/a |
| [ProjectPlan/](./ProjectPlan/) | Optional project-plan template | n/a |

---

## Quick Start

1. Copy the templates above into your project's `docs/` directory.
2. Fill in `CONTEXT.md` (project name, current focus, key files) and `PROGRESS.md` (sprint goals, first tasks `T-001`, `T-002`...).
3. Replace every `!!FILL-IN!!` and `!!YYYY-MM-DD!!` placeholder. The templates fail loudly when uninitialized.
4. Point your agent at `AGENTS.md` (cold-start file) and tell it to read `CONTEXT.md` + `PROGRESS.md` + this `WORKFLOW.md`.

---

## What Changed From v2.0

Earlier framework versions used a two-role model (PM_Agent / IC_Agent) with rolling logs that grew unbounded. They live in [`/archive/templates-v2-framework/`](../archive/templates-v2-framework/) and [`/archive/templates-v02/`](../archive/templates-v02/) — kept for reference only.

Key changes in the current framework:
- PM/IC/QA three-hat model (added QA as explicit verification step).
- Strict size budgets on every state file.
- Trigger-based compaction (not "every session").
- HANDOFF as overwrite, not append.
- Lightweight Track for small tasks — three-hat loop is optional by task size.
