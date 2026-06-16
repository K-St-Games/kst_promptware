> **Artifact class:** Template (rule stub — pending charter enumeration; see Ledger L-010, L-013)
> **Maturity:** experimental — unpopulated stub; extend before treating as authoritative
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** Agents working on TypeScript/JavaScript projects
> **Origin:** kst_promptware

# TypeScript / JavaScript Rules

Extends [`../common/`](../common/) with TypeScript-specific conventions. Install all common rules first; this file adds and overrides only where TypeScript idiom differs.

---

## Types

- Prefer explicit type annotations on all public API boundaries (function parameters, return types, exported types). Inference is fine internally.
- Avoid `any`. If you're using `any`, you have an unmodeled boundary — introduce a type or use `unknown` with a type guard.
- Use `undefined` rather than `null` for optional values. Only use `null` when interoperating with APIs that require it.
- Use discriminated unions to model states: `type Result<T> = { ok: true; value: T } | { ok: false; error: Error }`.

**Project-specific:** *(fill in your tsconfig.json strict settings, target, and module format)*

---

## Naming

- Files: `kebab-case.ts` for utilities, `PascalCase.tsx` for components
- Type aliases and interfaces: `PascalCase`
- Enums: `PascalCase` with `PascalCase` values (or replace with union literals — prefer unions)
- React hooks: prefix with `use`: `useCurrentUser`, `useOrderList`

**Project-specific:** *(fill in any additional naming conventions for your codebase)*

---

## Modules

- Named exports preferred over default exports. Default exports make refactoring and editor tooling harder.
- Import order: stdlib → third-party → internal (use an import sorter to enforce this automatically)
- Avoid barrel files (`index.ts` that re-exports everything) in large directories — they create circular dependency risk and slow TypeScript compilation.

**Project-specific:** *(fill in your module resolution config: absolute imports, path aliases)*

---

## Async

- `async`/`await` over `.then()` chains. Easier to read, easier to debug, easier to handle errors.
- Do not mix `async`/`await` and `.then()` in the same function.
- Await independent promises in parallel: `const [a, b] = await Promise.all([fetchA(), fetchB()])`.
- Handle rejections explicitly — an unhandled promise rejection is a silent failure.

---

## React (if applicable)

*(Fill in: component conventions, state management approach, hook rules, event handler naming)*

---

## Populate This File

Replace the `*(fill in)*` stubs above with your project's actual conventions. Commit the result. Rules agents can't enforce conventions they don't know.
