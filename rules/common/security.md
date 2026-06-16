> **Artifact class:** Rule (common)
> **Maturity:** canonical
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** All agents at session start; always-apply
> **Origin:** kst_promptware

# Security

## The First Rule

**Validate at system boundaries.** Everything from outside your process — user input, HTTP requests, file contents, environment variables, database results from untrusted schemas — is untrusted until validated. Trust internal code and framework guarantees; validate external data.

## Injection

- SQL: always use parameterized queries or prepared statements. Never concatenate user input into SQL strings.
- Shell: never pass user input to shell commands. If you must, use an argument array, never a string.
- HTML/JS (XSS): never inject user content as raw HTML. Use `textContent` not `innerHTML`. Use a sanitizer (DOMPurify) when HTML output is genuinely required.
- Template injection: do not use template engines that evaluate user-provided strings.

## Authentication & Authorization

- Verify authentication on every request that requires it — not just at the route level.
- Check authorization at the resource level, not just the route level. "User is logged in" and "user can access this resource" are different checks.
- Expire sessions. Implement logout that actually invalidates the server-side session.
- Use secure, httpOnly, sameSite cookies for session tokens. Do not store tokens in localStorage.

## Secrets

- No secrets in code, ever. No API keys, passwords, or tokens committed to version control.
- Secrets come from environment variables or a secrets manager. Never from config files in the repo.
- If a secret is committed accidentally: rotate it immediately, then clean the history.
- Use `.gitignore` and `.env` discipline. If in doubt, run `git diff --staged` and look before committing.

## Sensitive Data

- Do not log sensitive fields (passwords, tokens, PII, payment data).
- Truncate or hash sensitive data in error messages and stack traces.
- Apply the principle of least privilege: return only the fields the caller needs.

## Dependencies

- Track known vulnerabilities in dependencies (`npm audit`, `pip audit`, `govulncheck`).
- Do not add a dependency to solve a problem you can solve in 10 lines. Every dependency is attack surface.
- Pin dependency versions in production. Review dependency updates before merging.

## OWASP Top 10 Reference

When reviewing new endpoints or data flows, scan mentally against the OWASP Top 10: Injection, Broken Auth, Sensitive Data Exposure, XXE, Broken Access Control, Security Misconfiguration, XSS, Insecure Deserialization, Known Vulnerabilities, Insufficient Logging. Defer to the `security-reviewer` agent for a full audit.

## Escalation

Stop immediately and escalate to the human when:
- A change touches authentication, authorization, or session management
- A change handles payment data or PII
- You find a potential vulnerability in existing code that is out of scope for the current task
- You are unsure whether a dependency is safe to add
