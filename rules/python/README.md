> **Artifact class:** Template (rule stub — pending charter enumeration; see Ledger L-010, L-013)
> **Maturity:** experimental — unpopulated stub; extend before treating as authoritative
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** Agents working on Python projects
> **Origin:** kst_promptware

# Python Rules

Extends [`../common/`](../common/) with Python-specific conventions. Install all common rules first; this file adds and overrides only where Python idiom differs.

---

## Type Hints

- Use type hints on all function signatures. Return types too, not just parameters.
- Use `from __future__ import annotations` for forward references and cleaner hint syntax.
- Prefer `X | None` (Python 3.10+) over `Optional[X]` for optional parameters.
- Run `mypy` or `pyright` in strict mode on new files; address type errors before committing.

**Project-specific:** *(fill in mypy/pyright config, Python version target)*

---

## Naming

- Functions and variables: `snake_case`
- Classes: `PascalCase`
- Module-level constants: `UPPER_SNAKE_CASE`
- Private (module-internal) names: single leading underscore `_private`
- Dunder names (magic methods): `__init__`, `__repr__` — don't invent new ones

**Project-specific:** *(fill in any domain-specific naming conventions)*

---

## Imports

- Stdlib → third-party → local. One blank line between each group. Use `isort` to enforce automatically.
- Absolute imports over relative imports except within tightly coupled sub-packages.
- Never `from module import *`.

---

## Error Handling

- Catch specific exceptions, not bare `except:` or `except Exception:`.
- Use `raise ... from ...` to preserve exception chains: `raise ValueError("...") from original_error`.
- Raise early at precondition failure. Do not return sentinel values (return `None` on error is Python's `null` — it silences failures).

---

## Data Classes and Models

*(Fill in: dataclasses vs Pydantic vs attrs vs namedtuples — which does your project use and when?)*

---

## Testing

*(Fill in: pytest conventions, fixture patterns, conftest location)*

---

## Populate This File

Replace the `*(fill in)*` stubs above with your project's actual conventions. Commit the result.
