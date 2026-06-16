> **Artifact class:** Rule (common)
> **Maturity:** canonical
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** All agents at session start; always-apply
> **Origin:** kst_promptware

# Performance

## The First Rule

**Measure before optimizing.** A performance fix without a benchmark is a guess. A guess in the wrong place costs more time than it saves. Profile first, then optimize the bottleneck you actually found.

## Async I/O

- Never block the event loop / main thread on I/O. Use async/await, promises, goroutines, threads — whatever the language idiom is.
- Parallelize independent I/O operations. If you're awaiting two things that don't depend on each other, await them simultaneously.
- Add timeouts to all outbound network calls. An external API that hangs will hang your service.

## Database

- Fetch only the columns you need. `SELECT *` in production code is a smell.
- Paginate any query that can return unbounded results. "All users" is unbounded.
- Index fields you filter or sort by. Check the query plan when a query touches a large table.
- Prefer one query that returns what you need over N queries in a loop (the N+1 problem).
- Do not do work in a transaction that doesn't need to be transactional.

## Caching

- Cache explicitly, not accidentally. If a value is cached, document the invalidation strategy next to the cache.
- Cache at the layer where it makes sense: in-memory for computation, CDN for static assets, Redis for shared session state.
- A missing cache invalidation is a correctness bug, not just a performance issue.

## Memory

- Don't hold large datasets in memory when you can stream or page them.
- Release references to large objects when you're done with them.
- Be cautious with closures that capture large parent scopes.

## Premature Optimization

Do not optimize:
- Code that isn't on the critical path
- Code that runs once at startup
- Anything before profiling shows it's a bottleneck

Do optimize:
- Hot loops
- Database queries on high-traffic endpoints
- Anything that blocks the user-facing response

## When to Escalate

Escalate to the human when a performance fix requires a schema change, data migration, infrastructure change, or architectural refactor. Don't optimize yourself into a different system design without a conversation.
