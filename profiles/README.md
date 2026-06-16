> **Artifact class:** Template (catalog — pending charter enumeration; see Ledger L-010)
> **Written by:** Builder tier (Claude, 2026-06-16; authorized by Damien)
> **Amended by:** PR + human review
> **Read by:** Humans and agents setting up a new project from this library
> **Origin:** kst_promptware

# Installation Profiles

Three profiles, ordered by complexity. Pick the lightest profile that covers your automation goals — you can always upgrade.

## Comparison

| | [Minimal](minimal.md) | [Standard](standard.md) | [Full](full.md) |
|---|---|---|---|
| **Best for** | Single-agent, short tasks, or getting started | Ongoing AI-assisted development with structured sessions | Multi-agent, specification-first, full audit trail |
| **Session templates** (v3 layer) | Core only | Full suite | Full suite |
| **Common rules** | 2 (style + git) | All 6 | All 6 |
| **Language rules** | — | 1 (your stack) | All relevant stacks |
| **Dev agents** | — | 2 (reviewer + security) | All 5 |
| **Governance agents** | — | — | All 3 (planner, builder, gatekeeper) |
| **Skills** | 1 (handoff) | 4 (ADR + research + review + handoff) | All 7 |
| **Boresight bundle** | — | — | Full (charter, constitution, work orders) |
| **Setup time** | ~10 min | ~20 min | ~45 min |
| **Ongoing overhead** | Very low | Low | Medium |

## How to Install

No installer. Copy files from this library to your project.

1. **Choose a profile** — read the profile doc for the exact file manifest
2. **Copy files** to the destination paths listed in the manifest
3. **Replace placeholders** — every `!!PLACEHOLDER!!` must be replaced before use
4. **Create your project's `AGENTS.md`** — each profile provides a template
5. **Commit** the doc files as part of your project

## How to Sync Updates

kst_promptware is the upstream source. To pick up improvements:

1. Check `CHANGELOG.md` for what changed in the version you're upgrading to
2. Copy only the files that changed AND that exist in your installed manifest
3. Re-apply any project-specific customizations you've made

There is no automatic sync — this is intentional. Downstream projects may diverge deliberately; forced updates would overwrite that work.

## What Not to Copy

| Directory | Reason |
|---|---|
| `/archive/` | Deprecated versions — never copy |
| `/references/` | Background research — read, don't copy |
| `/work-orders/` | Instance-specific — never copy |
| `/ledger.md`, `/capability-map.md` | Boresight instance artifacts — Full profile creates new ones, not copies |

## Upgrading Between Profiles

Minimal → Standard: install the additional rules, skills, agents, and session templates listed in Standard.  
Standard → Full: install the Boresight bundle and governance agents listed in Full.  
There is no downgrade path — removing installed files is a manual decision.
