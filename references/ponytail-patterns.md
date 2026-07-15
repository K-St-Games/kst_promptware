# Ponytail Patterns — Adaptation Notes

> Portable patterns extracted from [ponytail](https://github.com/DietrichGebert/ponytail)
> (DietrichGebert, MIT), mapped onto kst_promptware conventions.
>
> Ponytail makes an AI agent think like the laziest senior dev in the room:
> the best code is the code you never wrote. Measured results on real agentic
> editing: **~54% less code, ~22% fewer tokens, ~20% cheaper, ~27% faster,
> 100% safety preserved** (mean across 12 feature tasks on a FastAPI+React
> repo, Haiku 4.5, n=4).
>
> **Date:** 2026-07-01
> **Origin:** `ponytail` / DietrichGebert
> **Status:** Adapted pattern reference

---

## 1. The Ladder (Decision Tree for Code Size)

The core pattern. Before writing any code, stop at the first rung that holds:

1. **Does this need to exist at all?** (YAGNI — skip it)
2. **Already in this codebase?** Reuse the helper, util, or pattern already here
3. **Standard library does it?** Use it
4. **Native platform feature covers it?** Use it
5. **Already-installed dependency solves it?** Use it
6. **Can this be one line?** Make it one line
7. **Only then:** write the minimum code that works

The critical guard: the ladder runs **after** understanding the problem, not instead of it. Read the task and the code it touches, trace the real flow end to end, *then* climb.

> A small diff you don't understand is just laziness dressed up as efficiency.

### Mapping to kst_promptware

The ladder is a compact reasoning scaffold that your existing skills can reference rather than re-explaining. It's most useful in:

- **`skills/deep-research/SKILL.md`** — before writing any code or recommendations, climb
- **`skills/code-review/SKILL.md`** — evaluate diffs against the ladder
- **`skills/work-order-intake/SKILL.md`** — when parsing a request, ask "does this rung resolve it before we build?"
- **`guides/development-loop.md`** — add as a pre-commit check step

### When not to use the ladder

- Security-critical code — the ladder prioritizes minimality; security needs thoroughness
- Data-loss boundaries — validation that prevents corruption is not on the chopping block
- Public API surfaces — backward compatibility and documentation are not "unnecessary code"

---

## 2. The One-Check Rule

> Non-trivial logic leaves **one runnable check** behind — the smallest thing that fails if the logic breaks. An assert-based demo/self-check or one small test file; no frameworks, no fixtures. Trivial one-liners need no test.

### Rationale

- A single targeted assertion is worth more than a full test suite that never runs
- Framework-free checks don't rot when the test runner changes
- The bar to **writing** a check is low, so it actually gets done
- The bar to **reading** a check is low, so the next agent actually reads it

### Mapping to kst_promptware

Add to `skills/code-review/SKILL.md` as a review criterion: "Does every non-trivial block have at least one runnable check?" And to `guides/development-loop.md` as a closure step: "For any non-trivial change, leave one assert-based check behind."

### What this replaces

The instinct to scaffold a test file with Vitest/Jest, mock the module, write three test cases, and leave it at `console.log('todo')` for six months. A 3-line Python assert or a 5-line bash `[[ $? -eq 0 ]]` sketch gets written, run, and understood.

---

## 3. The Ceiling-Comment Convention

Mark intentional simplifications with a comment that names the ceiling and the upgrade path:

```ts
// ponytail: global lock works at this crate's scale (≤10 concurrent ops).
//         Upgrade path: stripe-level lock + advisory lock on DB row
//         when this crate crosses into multi-region.
const lock = new GlobalLock();
```

### Rules

- Name the ceiling explicitly — don't just say "works for now"
- Name the specific trigger that should upgrade it
- Name the upgrade path concretely ("use X pattern" not "refactor later")
- The comment is a contract: the shortcut is deliberate, not accidental

### Mapping to kst_promptware

This should go in the constitution or a code-style convention doc. It's the kind of pattern that pays off across projects — SPS, WCCC, RPG, any codebase where agents come and go. The next agent encountering a `// TODO: make this faster` has no idea if it was intentional or an oversight. A `// ponytail:` comment with a ceiling and upgrade path removes the ambiguity.

---

## 4. Root-Cause Rule for Bug Fixes

> Bug fix = root cause, not symptom. A report names a symptom. Grep every caller of the function you touch and fix the shared function once — one guard there is a smaller diff than one per caller, and patching only the path the ticket names leaves a sibling caller still broken.

### Mapping to kst_promptware

Add to `skills/work-order-intake/SKILL.md` as part of the diagnosis phase: "Before patching, trace all call sites of the affected function. If the fix belongs in the shared function rather than the caller, do it there."

---

## 5. The Metrics-First Ethic

Ponytail doesn't ship claims, it ships measurements: a reproducible benchmark against a real repo, with task-by-task results, per-model breakdowns, and honest discussion of where it doesn't help (code that's already minimal). The benchmarks directory is the authoritative source, not the README.

### Mapping to kst_promptware

Kst_promptware's skills prescribe development patterns but have no validation that those patterns improve outcomes. Consider:

- Adding a `benchmarks/` directory
- For any skill that makes testable predictions ("following this skill reduces time-to-merge"), capture a baseline and measure against it
- Share results in the skill's README or a linked report

---

## 6. What NOT to Take

Ponytail's setup cost is earned through careful repetition. Some things aren't worth carrying over as-is:

- **The 16-platform delivery model** — kst_promptware doesn't need to ship to every agent runtime. Adapting for your most-used tools (Claude Code, Codex, this OpenClaw session) is enough.
- **The strict "no frameworks" test rule** — your projects already use Vitest and Jest for meaningful test suites. The one-check rule complements those; it doesn't replace CI.
- **The `// ponytail:` naming** — call it `// deliberate:` or `// shortcut:` or just use a richer `// TODO:` with the ceiling pattern. The label matters less than the structure.

---

## 7. Suggested Adoption by Profile

| Pattern | Minimal | Standard | Full |
|---------|---------|----------|------|
| The ladder | ✅ Always-on | ✅ | ✅ |
| Reading-first guard | ✅ Implicit | ✅ | ✅ |
| One-check rule | — | ✅ | ✅ |
| Ceiling comments | — | — | ✅ |
| Root-cause fix rule | — | ✅ | ✅ |
| Metrics validation | — | — | Suggested |

---

## Attribution

The ladder, one-check rule, ceiling-comment convention, and root-cause rule are from [ponytail](https://github.com/DietrichGebert/ponytail) by DietrichGebert, licensed MIT. The mapping analysis and profile adoption table are adaptation notes specific to kst_promptware conventions.