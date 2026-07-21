# paperclip/ — staging for paperclip-app skill/doc updates

Drafts authored here (origin `kst_promptware`) and migrated into the `paperclip-app`
repo to make Paperclip's agent workflow match the K_St PM/IC review loop
(see `_docs/reference/paperclip-skill-architecture-review.md` §7 in K_St_Engineering).

Files are written in **paperclip-app's native format** and the folder tree
**mirrors the destination paths**, so migration is a straight path-parallel copy.

## Migration mapping

| Source (here) | Destination (`paperclip-app`) | Action |
|---|---|---|
| `skills/paperclip-create-agent/references/agents/coder.md` | same path | overwrite (hardened IC template) |
| `skills/paperclip-create-agent/references/agents/reviewer.md` | same path | new (PM/reviewer template) |
| `docs/guides/pm-ic-review-workflow.md` | same path | new (workflow guide) |

## What each draft does

- **coder.md** — hardens the existing IC template: acceptance-criteria-first, a
  required evidence block at `done`, finding-ID discipline on changes-requested
  cycles, and typed escalation (implementation fix / spec gap / infra block).
- **reviewer.md** — new role that owns the execution-policy **review stage**:
  reviews against plan + acceptance criteria + diff + evidence, records findings
  with stable IDs in `feedback.md`, maps approve/request-changes onto status
  transitions, and never self-implements or self-approves.
- **pm-ic-review-workflow.md** — ties it together: frames execution policy as the
  PM/IC loop, gives a worked IC→PM policy JSON, the shared-state ownership table,
  the `feedback.md` convention, and ceremony-level guidance.

## Follow-up wiring (applied in-place in paperclip-app)

These are edits to **existing** paperclip-app files, so they were made directly in
paperclip-app rather than staged as full copies here (a full copy of the 34KB
`SKILL.md` would be instantly stale). Recorded here for provenance:

1. ✅ Registered `Reviewer` in
   `skills/paperclip-create-agent/references/agent-instruction-templates.md`
   (index row + "when to use each" bullet) so the hiring workflow discovers it.
2. ✅ `skills/paperclip/SKILL.md`: added the reviewer decision format (finding IDs +
   acceptance conditions) to the execution-policy wake block; pointed Step 8 at the
   evidence block; added a "Reference Map (load on demand)" routing table; and
   deleted the duplicated plan-to-tasks line.
