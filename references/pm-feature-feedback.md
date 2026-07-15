# PM Export Feature Feedback

**Owner:** Docs maintainer

**Status:** Review feedback — changes recommended before adapter implementation

**Reviewed:** 2026-07-14

**Scope:** `skills/pm-export/SKILL.md`, `schemas/pm-export.schema.json`, `pm/pm-export.json`, and `pm/README.md` at `02f8374`

**Classification:** Non-operational reference memo; the linked skill and schema remain authoritative

## Assessment

The proposal has the right architectural boundary: repository governance artifacts remain canonical, the JSON export is a derived projection, and target-specific behavior belongs in downstream adapters. Canonical ids plus preserved external ids are also the right foundation for repeatable synchronization.

The contract is not yet safe enough for an adapter to rely on, chiefly because it does not retain a durable per-target sync checkpoint and does not represent source deletion unambiguously. Those two issues should be resolved before implementing the first GitHub Issues adapter.

## Findings

### P1 — Drift detection lacks a durable per-target checkpoint

The skill says `source_hash` determines whether an item is dirty, but regeneration replaces the previous hash. The resulting export contains only the current source hash, with no `dirty` field or last-pushed hash for an adapter to compare against. An emitted console diff is useful to a human but is not part of the adapter contract.

This also needs to be target-specific: GitHub, Linear, and Jira may synchronize at different times.

**Recommendation:** replace or augment `external_ids` with explicit per-target sync state:

```json
"sync": {
  "github": {
    "external_id": 42,
    "last_pushed_hash": "sha256:..."
  }
}
```

The adapter should update `last_pushed_hash` only after a successful target write. Regeneration must preserve the entire `sync` object by canonical item id.

### P1 — Vanished-source handling is ambiguous

The reconciliation procedure says an item whose source vanished is retained with "a closing status" when previously synced. No closing status is defined, and choosing `complete`, `aborted`, or `archived` would conflate transport cleanup with canonical repository state. A retained item would also claim a source location that no longer exists.

**Recommendation:** represent deletion as a projection or sync operation rather than a canonical status. For example, add `present: false`, `sync_action: close`, or a tombstone record with the last canonical status and source metadata. Define when the tombstone may be removed after every known target has acknowledged closure.

### P2 — The schema is weaker than the documented item contract

The skill calls `body`, `priority`, `estimate`, `labels`, `parent`, `relations`, `assignee`, `external_ids`, `source_hash`, and `native` core fields, but the schema requires only `id`, `kind`, `source`, `title`, and `status`. An incomplete export can therefore validate while violating the procedure adapters are expected to consume.

The single status union also permits invalid combinations such as a work-order task with ledger status `open`, or a ledger question with work-order status `released`.

**Recommendation:**

1. Require every field that adapters may assume is present, using explicit nulls or empty collections where appropriate.
2. Add conditional `kind` rules for id shape, source class, and allowed statuses.
3. Require `source_hash` to match `^sha256:[0-9a-f]{64}$` when present.
4. Apply `format: date-time` to `generated_from.at`.
5. Add a semantic validation step for unique item ids, existing parents, and non-dangling relation targets. Draft-07 alone cannot express these cross-item constraints cleanly.

### P2 — Feature-spec extraction is not defined completely

The item model includes `features/*.md` as `spec` records, but the extraction step names only work orders, capability-map rows, and ledger entries. "As filed" does not establish a stable feature-spec id or explain where its canonical status comes from.

**Recommendation:** either remove `spec` from the v0.1 contract until feature specs exist, or define their id, source slice, status vocabulary, parentage, and relationship to their capability-map feature.

### P2 — Governance treatment needs one explicit rule

`pm/README.md` identifies itself as a nearest-fit Capability Map artifact but omits the constitution's required `Validated by` governance-header line. The generated JSON has no governance metadata beyond its schema and provenance fields.

**Recommendation:** add `Validated by` to the README. Then state explicitly whether the JSON export is an ungoverned generated output or a governed artifact. If governed, its schema needs to carry the equivalent four governance fields; if generated and ungoverned, the README should say so.

### P3 — Hash normalization should be specified

The committed sample hashes capability-map and ledger rows without their terminal newline, while the work-order hash covers the entire file including its final newline. The values are reproducible, but another implementation could reasonably choose different newline behavior and mark all items dirty.

**Recommendation:** specify UTF-8 encoding, LF normalization, the exact source-slice boundaries, and terminal-newline handling. A small example vector for one row and one file would make independent implementations testable.

### P3 — The idempotency guarantee is too absolute

The body marker plus `external_id` lookup is a sound recovery strategy, but it cannot guarantee "at most one issue, ever" if two pushers race before either records the created id.

**Recommendation:** require serialized pushes per target and canonical id, and describe marker search as duplicate recovery rather than a hard uniqueness guarantee.

## What Works Well

- The one-way `docs -> export -> adapter` flow preserves the charter's boundary: the bundle maps into project-management tools without becoming one.
- Target-specific field and status mappings stay outside the neutral schema.
- Canonical repository ids provide a stable correlation key across regenerations.
- The sample avoids inventing priority, estimate, or assignee values when the source does not declare them.
- Relations in the committed sample point only to exported items.
- Experimental maturity is appropriate while the first adapter proves the contract.

## Validation Evidence

At `02f8374`:

- `pm/pm-export.json` validates against the Draft-07 schema.
- `skills/pm-export/SKILL.md` frontmatter validates against `schemas/skill.schema.json`.
- The sample contains 32 items with no duplicate ids.
- Every sample item contains `external_ids` and a non-null source hash.
- The sample's work-order, capability-map row, and ledger-row hash strategies were independently reproduced.

Schema validity should not be treated as adapter readiness until the semantic and synchronization findings above are addressed.

## Suggested Sequence

1. Decide the per-target sync-state and tombstone shapes.
2. Tighten the schema and document hash normalization.
3. Regenerate the sample export under the revised contract.
4. Implement a minimal GitHub Issues adapter as the proving consumer.
5. Run create, no-op, update, deletion, interrupted-push, and duplicate-recovery scenarios before promoting the skill from `experimental`.

## Resolution Addendum

**Addressed:** 2026-07-15, by direct authorization following this review. Covers steps 1–3 of the sequence above; steps 4–5 (the adapter and its scenario tests) remain future work.

| Finding | Disposition |
|---|---|
| P1 — no durable per-target checkpoint | **Fixed as recommended.** `external_ids` replaced by `sync: {target: {external_id, last_pushed_hash, closed}}`. Dirtiness is now per target (`source_hash != sync[T].last_pushed_hash`), not global. |
| P1 — vanished-source handling ambiguous | **Fixed as recommended.** Added `present: false` as a tombstone flag; a vanished item with prior sync state is retained (frozen title/body/status) until every target's `sync` entry reaches `closed: true`, then dropped. An item never synced anywhere is dropped immediately, no tombstone. |
| P2 — schema weaker than documented contract | **Fixed, with one deliberate deviation.** All fields the skill calls "core" are now `required` (explicit `null`/`[]`/`{}` where the source declares nothing), and `source_hash` / `sync[*].last_pushed_hash` now carry the `^sha256:[0-9a-f]{64}$` pattern; `generated_from.at` carries `format: date-time`. Per-`kind` conditional rules (id shape, allowed status subset) were **not** encoded as draft-07 schema logic — that's more machinery than this docs-only, no-build-step repo should carry as a bundle dependency (Constitution §1.2). They're implemented instead as an explicit semantic-validation checklist in the skill's Step 5, run as a script or by hand rather than baked into the schema. Same coverage, different layer. |
| P2 — feature-spec extraction underspecified | **Resolved by removal, not by specifying.** Took the review's first option: `spec` dropped from the `kind` enum and the item-model table, with an inline note explaining why (no feature-spec artifacts exist anywhere in this repo yet — `features/` is empty). Re-add once a real one exists to model the id/status/parentage scheme against, rather than guessing now. |
| P2 — governance treatment needs an explicit rule | **Fixed as recommended.** `pm/README.md` now carries a `Validated by` line (schema + the Step 5 semantic checklist) and an explicit statement that `pm-export.json` is generated output governed only through the README and skill, not independently — same posture as the `/LICENSE` exception in Constitution §2.2. |
| P3 — hash normalization unspecified | **Fixed as recommended.** One normalization rule now applies uniformly to every source-slice type (UTF-8, CRLF→LF, strip trailing whitespace/newline, no addition) — the previous file-vs-row inconsistency is gone. `skills/pm-export/SKILL.md` Step 3 carries the exact algorithm plus three real worked-example hash vectors (one capability-map row, one ledger bullet, the full WO-0001 file) computed against this repo, reproducible by an independent implementation. |
| P3 — idempotency guarantee overclaimed | **Fixed as recommended.** The GitHub mapping section no longer claims "at most one issue, ever." It now states the marker/id lookup is duplicate *recovery*, names the actual race (two pushers, same uncreated id, before either records `sync.github.external_id`), and recommends serializing pushes per target and canonical id as the way to make the guarantee hard. |

**Schema versioning:** the shape change is breaking (field rename/restructure, `kind` enum narrowed), so per the schema's own versioning note this bumped `pm-export/v0.1` → `v0.2` — not to `v1.0`, since nothing has consumed v0.1 as a stable contract yet (no adapter exists, every `external_ids` in the shipped sample was empty). The schema doc now states the convention explicitly: any shape change stays a minor bump below v1.0; v1.0 is reserved for the first shape a real adapter implements against.

`pm/pm-export.json` was regenerated under the revised contract at the commit introducing these fixes; see that commit and the following one (the regenerated export, stamped with the fix commit's sha) for the applied result.
