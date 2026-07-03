# References Index

**Owner:** Docs maintainer  
**Status:** Active  
**Last Updated:** July 3, 2026  
**Source of Truth:** `references/README.md` for reference classification

This folder contains research and non-operational documentation. These files inform decisions but are not required for day-to-day execution workflow.

| File | Type | Topics | Decision Relevance |
|------|------|--------|--------------------|
| [`claude-recommendations.md`](claude-recommendations.md) | Analysis memo | Structure, role design, git policy | High |
| [`cursor-recommendations.md`](cursor-recommendations.md) | Analysis memo | Navigation, organization, cross-linking | High |
| [`the-case-for-custom-ai-tooling.md`](the-case-for-custom-ai-tooling.md) | Concept paper | Tooling philosophy, interfaces | Medium |
| [`improvement-proposal-052526.md`](improvement-proposal-052526.md) | Technical proposal | v3.0 core content enhancements: slicing, testing, commits, browser escalations | High |
| [`agent-orchestrator-short-term-proposal.md`](agent-orchestrator-short-term-proposal.md) | Technical proposal | Lightweight orchestrator layer, PM/Sr Dev/Implementer loop, repo-native artifacts | High |
| [`agent-orchestrator-long-term-proposal.md`](agent-orchestrator-long-term-proposal.md) | Technical proposal | Runtime adapters, control plane, project cartridge, staged automation | High |
| [`agent-orchestrator-spec.md`](agent-orchestrator-spec.md) | Formal spec | Tier 1 orchestrator packet, fixed seam, gates, role contracts, manual-pilot plan | High |
| [`agent-orchestrator-roadmap.md`](agent-orchestrator-roadmap.md) | Roadmap | Tiers 2–4: thin CLI, runtime adapters, GitHub/cartridge, policy autonomy | High |
| [`ponytail-patterns.md`](ponytail-patterns.md) | Adapted pattern reference | Code-size ladder, one-check rule, ceiling comments, root-cause fix — extracted from ponytail (DietrichGebert, MIT) | Medium |
| [`repo-review-070326.md`](repo-review-070326.md) | Review memo | Full-repo review: harness-facing install defects, drift/duplication, token economics, rigidity vs. harness leverage; prioritized punch list | High |
| [`solo-profile-proposal-070326.md`](solo-profile-proposal-070326.md) | Technical proposal | Solo Baseline profile (v1): harness-neutral doc set (AGENTS/STATE/PROJECT/CONVENTIONS/REVIEWS) for solo multi-agent development with a guest-reviewer lane; extended by the planning-cascade proposal | High |
| [`planning-cascade-proposal-070326.md`](planning-cascade-proposal-070326.md) | Technical proposal | Solo workflow standard v2: six-altitude planning cascade (vision → tiers → phase → weekly → spec → state) with Serves-chain alignment headers, compaction-recovery protocol, dated weekly close-outs, and two spec weights; templates included | High |

## Usage

- Consult these docs when revising process, architecture, or documentation structure.
- Do not treat these docs as operational source-of-truth for active sessions.
