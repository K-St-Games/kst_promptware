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

## Source-of-truth contract (read this first)

- **Docs are canonical.** Work orders, the capability map, and the ledger are the truth. `pm/pm-export.json` is a projection — regenerate it, never hand-author item content.
- **`external_ids` is sacred.** It is the *one* field the docs do not hold: the id each tool assigned on first push (e.g. `{"github": 42}`). Every regeneration must carry it forward, matched by canonical `id`. Clobber it and you orphan the synced issue; re-syncing then duplicates.
- **`source_hash` drives drift.** Hash each item's source slice. If it changed since the last export, the item is *dirty* and the pusher should update the target. Unchanged → skip.
- **No conflict resolution.** Docs win, always. This skill never pulls state back from a tool.

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
| Feature spec (`features/*.md`) | `spec` | as filed |
| Ledger entry — open choice | `question` | `L-0xx` |
| Ledger entry — recorded/deferred decision | `decision` | `L-0xx` |

Core fields per item: `id`, `kind`, `source{path, artifact_class, anchor}`, `title`, `body`, `status`, `priority`, `estimate`, `labels`, `parent`, `relations[]`, `assignee`, `external_ids`, `source_hash`, `native`. See the schema for exact types.

**Kind heuristic for ledger entries:** an entry posing an unresolved choice → `question`; an entry recording a made-or-deferred decision → `decision`. This is a heuristic — a human may reclassify.

## Procedure

**Step 1 — Extract.** Read each source. For every work order, feature row, and ledger entry, capture its id, title, status, and the raw source slice (the WO file, the map row, the ledger bullet).

**Step 2 — Normalize.** Map each to an item:
- `status` = the canonical status straight from the source (WO frontmatter `status`; the map's marker for a feature; the ledger entry's status). Do **not** translate to any tool's vocabulary here.
- `parent` = a task's `parent_feature`; null otherwise.
- `relations` = only links whose target is *also an item in this export* (real ids). References to uncut orders or non-item anchors go in `body`, not `relations` (no dangling links).
- `priority`, `estimate`, `assignee` = null unless the source states them. Never invent.
- `native` = source-specific fields with no neutral home (e.g. a WO's `assignee_tier`, `gate`, `spawns`, `depends_on`, `conflicts`) under a namespaced key.

**Step 3 — Compute `source_hash`.** Hash the raw source slice (sha256 of the WO file / map row / ledger bullet). This is what later runs diff against to detect drift.

**Step 4 — Reconcile (idempotent).** If a prior `pm/pm-export.json` exists, match by canonical `id` and **carry `external_ids` forward**. Mark an item dirty when its new `source_hash` differs from the prior one. New ids are added; ids whose source vanished are retained with a closing status only if they were previously synced (so the tool can close them) — otherwise dropped.

**Step 5 — Validate.** The assembled file must validate against `schemas/pm-export.schema.json`. Fix any violation before writing.

**Step 6 — Emit a diff.** Report **added / changed / closed** counts and the dirty ids, so a human sees what a push would do before it happens.

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

**Fields → GitHub:**

| Canonical | GitHub home |
|---|---|
| `id` | body marker `<!-- pm:{id} -->` **and** `external_ids.github` = issue number |
| `title` | issue title (optionally prefixed `{id}: `) |
| `body` | issue body + a source-path line + rendered relations |
| `kind` | label `kind:{kind}` (or an org-level issue type) |
| `labels` | passed through |
| `priority` | label `priority:{P}` — *no native field* (null → omit) |
| `estimate` | label `estimate:{n}` — *no native field* (null → omit) |
| `parent` | native **sub-issue** link; fallback label `epic:{parentId}` |
| `relations: depends_on` | body line `Depends on #NN` — *no native field* |
| `assignee` | `assignees[]` via a handle map (null → unassigned) |

**Idempotency mechanism.** Before creating, find an existing issue by the `<!-- pm:{id} -->` body marker (search) or by `external_ids.github`. Found → update; not found → create, then write the new issue number back into `external_ids.github`. One canonical id ⇒ at most one issue, ever.

**Lossy on purpose.** Priority, estimate, dependencies, and the four open-state distinctions have no native Issues field, so they degrade to labels/body. If you later adopt **GitHub Projects v2**, those become native custom fields and the mapping upgrades from labels → project fields — a mapping swap, not a schema change.

## Not in scope

- **Pushing to a tool.** This skill produces and reconciles the neutral JSON; a target adapter consumes it.
- **Pulling state back.** One-way only: docs → JSON → tool. Pulling status back would lose `reconciled` / `aborted` nuance that no target tool can represent.

## Quality bar

A good export round-trips: regenerating from an unchanged repo produces an identical file (stable ordering, preserved `external_ids`), and every item's `source` is precise enough to open the exact doc slice it came from.
