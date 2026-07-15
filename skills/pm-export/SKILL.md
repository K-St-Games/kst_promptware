---
name: pm-export
description: Extract project-management data from the repo's governance artifacts (work orders, capability map, ledger) into a tool-agnostic JSON export, ready to sync into GitHub Issues, Linear, or Jira. Invoke when you need to refresh pm/pm-export.json or prepare repo status for an external tracker.
profile: full
maturity: experimental
origin: kst_promptware
---

# PM Export

Projects the repo's governance artifacts into a single tool-agnostic file, `pm/pm-export.json`, that a downstream adapter can push into GitHub Issues, Linear, Jira, or a custom tracker. The export is **derived**: the governance docs stay canonical, this file is regenerated from them.

The skill's job ends at producing and reconciling the neutral JSON. Actually pushing to a specific tool is a separate concern (a later adapter) — keeping this skill tool-agnostic is the point.

> **Contract revision note (2026-07-15):** this skill and `schemas/pm-export.schema.json` (now `pm-export/v0.2`) were revised against `references/pm-feature-feedback.md`, a review of the v0.1 delivery. The revision replaced flat `external_ids` with per-target `sync` state, defined vanished-source (tombstone) handling, tightened the schema's required fields, specified hash normalization, deferred the `spec` kind, and softened an overclaimed idempotency guarantee. See that doc's resolution addendum for the full mapping of finding → fix.

## Source-of-truth contract (read this first)

- **Docs are canonical.** Work orders, the capability map, and the ledger are the truth. `pm/pm-export.json` is a projection — regenerate it, never hand-author item content.
- **`sync` is sacred.** It is the *one* field the docs do not hold: per-target checkpoints (`{"github": {"external_id": 42, "last_pushed_hash": "sha256:..."}}`). Every regeneration must carry it forward, matched by canonical `id`. Clobber it and you orphan the synced issue; re-syncing then duplicates.
- **`source_hash` drives drift, per target.** Hash each item's source slice (normalization spec in Step 3). An item is dirty *for target T* when `source_hash != sync[T].last_pushed_hash` — dirtiness is not global, because GitHub, Linear, and Jira sync on independent schedules.
- **Vanished sources become tombstones, not canonical statuses.** If an item's source disappears from the docs but it was previously synced (non-empty `sync`), it is retained with `present: false` rather than assigned a status like `complete` or `aborted` that would misrepresent *why* it's closing. See Step 4.
- **No conflict resolution, and no reading state back.** Docs win, always. This skill never pulls state back from a tool. The one exception is bookkeeping: an adapter may write its *own* push outcome into `sync[target].last_pushed_hash` or `sync[target].closed` — that's the adapter recording what it did, not the export reading the tool's state. The docs → export → tool flow stays strictly one-way.

## Prerequisites

Read before running:
- `charter.md` — glossary + artifact classes (the nouns being projected).
- `constitution.md` §8 (work-order state machine — the status vocabulary) and §3 (status canon: frontmatter is truth, the map is derived).
- `capability-map.md` — the features (`F-0xx`) and their statuses.
- `ledger.md` — the entries (`L-0xx`) and their statuses.
- `work-orders/` — the cut orders (`WO-0xxx.md`) and their frontmatter.
- `schemas/pm-export.schema.json` — the output contract.

## The item model

Every source artifact projects to one item with a `kind` discriminator:

| Source artifact | `kind` | `id` |
|---|---|---|
| Capability-map feature row | `epic` | `F-0xx` |
| Work order (`work-orders/*.md`) | `task` | `WO-0xxx` |
| Ledger entry — open choice | `question` | `L-0xx` |
| Ledger entry — recorded/deferred decision | `decision` | `L-0xx` |

**`spec` is deliberately deferred**, not forgotten. Feature specs (`features/*.md`) have no instances anywhere in this repo yet (`features/` is empty; WO-0002+ are uncut) — defining an id scheme, status vocabulary, and parentage rule for an artifact class with zero real examples would be guessing. Add a `spec` kind back to the schema and this table once a real feature spec exists to model it against.

Core fields per item: `id`, `kind`, `source{path, artifact_class, anchor}`, `title`, `body`, `status`, `priority`, `estimate`, `labels`, `parent`, `relations[]`, `assignee`, `sync`, `source_hash`, `native`, and the optional tombstone flag `present`. All are **required** in the schema except `present` (omit when true — the default) — an item missing any of these does not validate, so an adapter can rely on every field being there, with explicit `null`/`[]`/`{}` standing in for "the source declares none." See the schema for exact types.

**Kind heuristic for ledger entries:** an entry posing an unresolved choice → `question`; an entry recording a made-or-deferred decision → `decision`. This is a heuristic — a human may reclassify.

## Procedure

**Step 1 — Extract.** Read each source. For every work order, feature row, and ledger entry, capture its id, title, status, and the raw source slice (the WO file, the map row, the ledger bullet).

**Step 2 — Normalize.** Map each to an item:
- `status` = the canonical status straight from the source (WO frontmatter `status`; the map's marker for a feature; the ledger entry's status). Do **not** translate to any tool's vocabulary here.
- `parent` = a task's `parent_feature`; null otherwise.
- `relations` = only links whose target is *also an item in this export* (real ids). References to uncut orders or non-item anchors go in `body`, not `relations` (no dangling links) — checked mechanically in Step 5.
- `priority`, `estimate`, `assignee` = null unless the source states them. Never invent.
- `labels`, `relations` = `[]`, not omitted, when none apply. `native` = `{}`, not omitted, when there's nothing source-specific to carry.
- `native` = source-specific fields with no neutral home (e.g. a WO's `assignee_tier`, `gate`, `spawns`, `depends_on`, `conflicts`) under a namespaced key.

**Step 3 — Compute `source_hash` (normalization spec).** All source slices — a full work-order file, a single capability-map row, a single ledger bullet — hash the same way, so independent implementations agree and no slice type gets special-cased:

1. Take the slice as UTF-8 text (the whole file for a work order; the exact single line for a map row or ledger bullet).
2. Normalize line endings: `\r\n` and `\r` → `\n`.
3. Strip *trailing* whitespace and newlines only (no leading-whitespace or internal changes). Do not add anything back — no synthetic trailing newline.
4. Encode the result as UTF-8 and hash with SHA-256.
5. Format as `sha256:<64 lowercase hex chars>` — this is exactly the schema's `source_hash` pattern, `^sha256:[0-9a-f]{64}$`.

**Worked example vectors** (computed against this repo at commit `74b78a6`, reproduce to self-check an implementation):

| Source slice | Exact text hashed (truncated for display) | `source_hash` |
|---|---|---|
| `capability-map.md`, F-001 row | `\| F-001 BOOTSTRAP \| Repo foundation: north-star artifacts + work-order machinery …` (119 chars) | `sha256:f86d1ce0ebe23c8e0fd759ba8f885c523ad04fed9e52283d904aba7e8a8ac339` |
| `ledger.md`, L-001 bullet | `- **L-001** · \`resolved 2026-06-12\` — **Bootstrap paradox.** WO-0001 predates th…` (272 chars) | `sha256:29731dfbed9732c14918a7cce9b2cf6cbd57695a6ca9f3a3b638c53a4b528b5a` |
| `work-orders/WO-0001.md`, full file | `# WO-0001 — Foundation: Make the Bundle Self-Hosting …` (13,529 chars) | `sha256:b64f1f9e52dafee4446e7695ee96af47da0acaa96948bc97bd173e6025ca531b` |

**Step 4 — Reconcile (idempotent, per-target).** If a prior `pm/pm-export.json` exists, match by canonical `id`:

- **Carry `sync` forward untouched.** Copy the prior item's entire `sync` object onto the new item. This skill never writes into `sync` beyond copying it forward — only an adapter writes `last_pushed_hash` or `closed`, and only after a successful push/closure.
- **Dirtiness is per target.** After carrying `sync` forward, an item is dirty for target `T` iff `sync` has a key `T` and `source_hash != sync[T].last_pushed_hash` (or `T` has no `last_pushed_hash` yet — registered but never pushed). An item with no `sync[T]` key at all is simply *new* for `T`, not dirty.
- **Vanished sources → tombstone, don't reassign status.** If a prior item's id is not found in this run's Step 1 extraction:
  - If its prior `sync` is `{}` (never synced anywhere) — **drop it**. There is nothing to notify, so carrying it forward serves no purpose.
  - If its prior `sync` is non-empty — **retain it** with `present: false`, freezing its last-known `title`/`body`/`status` exactly as previously exported, and keep its `sync` object as-is so every target that has it can still find and close it.
  - **Removal condition:** a tombstoned item is dropped from the export once *every* key in its `sync` object has `closed: true`. Each target's adapter sets its own key's `closed` after it successfully processes that closure (e.g. closes the GitHub issue) — this is the adapter recording its own completed action, not this skill polling the target.
- New ids (no prior item at all) get `sync: {}`.

**Step 5 — Validate.** Two passes, both required before writing:

1. **Schema.** The assembled file must validate against `schemas/pm-export.schema.json` (draft-07). Fix any violation before writing.
2. **Semantic checks** (deliberately kept out of the JSON Schema itself — draft-07 conditionals for this would be more machinery than a docs-only, no-build-step repo should carry per Constitution §1.2; run this as a plain checklist or a throwaway script instead):
   - Every `id` is unique across `items`.
   - Every `parent` value is either `null` or an id present in `items`.
   - Every `relations[].id` is present in `items` (no dangling links).
   - Every `source_hash` (when non-null) matches `^sha256:[0-9a-f]{64}$`.
   - Id shape matches `kind`: `epic` → `F-\d+`, `task` → `WO-\d+`, `question`/`decision` → `L-\d+`.
   - `status` is a value that plausibly belongs to the item's `kind` (e.g. a `task` should carry a work-order-lifecycle status, not a bare ledger status like `resolved`) — flag, don't hard-fail, since the canonical status enum is intentionally a single shared union (see the schema's `status` description).

**Step 6 — Emit a diff.** Report **added / changed / closed** counts, broken out **per target** where `sync` has entries (an item can be dirty for GitHub but current for Linear), plus the dirty ids and any newly created tombstones — so a human sees exactly what a push would do to each target before it happens.

## GitHub Issues mapping (first target)

Applied by the *pusher*, not by this skill — the export stays neutral. GitHub Issues is field-poor, so the JSON remains the high-fidelity layer.

**Status → GitHub:**

| Canonical | GitHub state | `state_reason` | marker label |
|---|---|---|---|
| draft | open | — | `status:draft` |
| released | open | — | `status:ready` |
| in-progress | open | — | `status:in-progress` |
| review | open | — | `status:review` |
| blocked | open | — | `status:blocked` |
| queued | open | — | `status:queued` |
| complete | closed | `completed` | — |
| reconciled | closed | `completed` | `reconciled` |
| delivered | closed | `completed` | `delivered-outside-wo` |
| aborted | closed | `not_planned` | — |
| open *(ledger)* | open | — | (kind label) |
| resolved *(ledger)* | closed | `completed` | — |
| archived *(ledger)* | closed | `completed` | `archived` |

A tombstoned item (`present: false`) closes regardless of its frozen `status`, with `state_reason: completed` and label `pm-source-removed` — the closure reason is "removed from the docs," which the frozen canonical status does not itself say.

**Fields → GitHub:**

| Canonical | GitHub home |
|---|---|
| `id` | body marker `<!-- pm:{id} -->` **and** `sync.github.external_id` = issue number |
| `title` | issue title (optionally prefixed `{id}: `) |
| `body` | issue body + a source-path line + rendered relations |
| `kind` | label `kind:{kind}` (or an org-level issue type) |
| `labels` | passed through |
| `priority` | label `priority:{P}` — *no native field* (null → omit) |
| `estimate` | label `estimate:{n}` — *no native field* (null → omit) |
| `parent` | native **sub-issue** link; fallback label `epic:{parentId}` |
| `relations: depends_on` | body line `Depends on #NN` — *no native field* |
| `assignee` | `assignees[]` via a handle map (null → unassigned) |

**Idempotency mechanism.** Before creating, find an existing issue by the `<!-- pm:{id} -->` body marker (search) or by `sync.github.external_id`. Found → update; not found → create, then write the new issue number into `sync.github.external_id` and the current `source_hash` into `sync.github.last_pushed_hash`.

This is **duplicate-recovery, not a hard uniqueness guarantee**: the marker/id lookup reliably finds an already-created issue on any *subsequent* run, but two pushers racing on the *same* uncreated id before either records `sync.github.external_id` can still both create an issue. Serialize pushes per target and canonical id (a lock, a single-writer queue, or simply "one pusher process at a time") to make "at most one issue" a hard guarantee rather than a recovery property.

**Lossy on purpose.** Priority, estimate, dependencies, and the four open-state distinctions have no native Issues field, so they degrade to labels/body. If you later adopt **GitHub Projects v2**, those become native custom fields and the mapping upgrades from labels → project fields — a mapping swap, not a schema change.

## Not in scope

- **Pushing to a tool.** This skill produces and reconciles the neutral JSON; a target adapter consumes it.
- **Pulling state back.** One-way only: docs → JSON → tool. Pulling status back would lose `reconciled` / `aborted` nuance that no target tool can represent. (An adapter writing its own `sync[target].last_pushed_hash` / `closed` after a successful write is bookkeeping about its own action, not an exception to this rule — see the source-of-truth contract above.)

## Quality bar

A good export round-trips: regenerating from an unchanged repo produces an identical file (stable ordering, preserved `sync`), and every item's `source` is precise enough to open the exact doc slice it came from. It also survives the Step 5 semantic checks with zero findings — a schema-valid export that fails a semantic check is not ready to hand to an adapter.
