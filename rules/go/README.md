> **Artifact class:** Template (rule stub — pending charter enumeration; see Ledger L-010, L-013)
> **Maturity:** experimental — unpopulated stub; extend before treating as authoritative
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** Agents working on Go projects
> **Origin:** kst_promptware

# Go Rules

Extends [`../common/`](../common/) with Go-specific conventions. Install all common rules first; this file adds and overrides only where Go idiom differs.

---

## Error Handling

- Always check returned errors. An unchecked error is a bug, not a style issue.
- Wrap errors with context: `fmt.Errorf("loading config: %w", err)`. Use `%w` (not `%v`) to preserve the chain for `errors.Is` / `errors.As`.
- Return errors as the last return value. Don't return errors as the first value.
- Sentinel errors (`var ErrNotFound = errors.New("not found")`) for callers that switch on error type; wrapped errors for all others.

---

## Naming

- Exported names: `CamelCase`
- Unexported names: `camelCase`
- Acronyms follow Go convention: `userID` not `userId`, `HTTPServer` not `HttpServer`, `urlParser` not `URLParser`
- Interface names: single-method interfaces get a noun + `-er` suffix (`Reader`, `Writer`, `Stringer`)
- Loop variables: single letters acceptable (`i`, `j`, `k` for indices; `v` for values; `k, v` for maps)
- Avoid stuttering: `user.UserID` → `user.ID`

---

## Interfaces

- Accept interfaces, return structs. Callers define the interface they need; packages return concrete types.
- Keep interfaces small. A two-method interface that grows to ten methods is a concrete type waiting to happen.
- Don't define an interface until you have two implementations (or until you need to mock it in tests).

---

## Packages

- One concept per package. A package named `utils` or `helpers` is a sign of failed organization.
- Package names: single lowercase word, no underscores, no `pkg_` prefix.
- Avoid circular imports — they reveal a coupling problem, not a tooling problem.

---

## Goroutines and Channels

- Don't start a goroutine unless you know how it will stop.
- Prefer `context.Context` for cancellation over custom stop channels.
- Use `sync.WaitGroup` when you need to wait for goroutines; use `errgroup` when you need to collect errors from them.

**Project-specific:** *(fill in: concurrency patterns used in this project)*

---

## Populate This File

Replace the `*(fill in)*` stubs above with your project's actual conventions. Commit the result.
