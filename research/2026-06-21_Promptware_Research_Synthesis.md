# Promptware Research Synthesis

> **Filed:** 2026-06-21
> **Source:** Two research analyses commissioned by Doomien, captured here for reference
> **Context:** kst_promptware — specification-first software factory (charter → constitution → work orders → evidence)

---

## Source 1: Artifact-Driven Development & Promptware (summary)

### Promptware Engineering (arXiv:2503.02400)
Formally defines the "promptware" paradigm — treating natural language rules, constraints, and templates as first-class software artifacts subject to rigorous software engineering principles. Explicitly calls for formalized design patterns mapping intents, roles, and boundaries. Directly mirrors the portable artifact bundle concept.

### Spec-Driven Development with AI Agents
Methodologies (e.g., Xcapit) treating specifications as executable, versioned build artifacts rather than static documentation. Agents parse raw intent into structured formats (state transition tables, OpenAPI specs) → autonomously drive test cases + implementation code. Code is byproduct; spec is authoritative record.

### The Malleable Software Thesis (Andreas Kirsch)
While implementation code is becoming highly malleable, durable artifacts — data models, integration contracts, state machinery — must become the primary focus of engineering.

### Agent Contracts (arXiv:2601.08815)
Formal framework extending the 1980s Contract Net Protocol to LLMs. Unifies input/output specifications, multi-dimensional resource constraints (token budgets, execution time), and explicit success criteria. Ensures sub-task contracts strictly respect parent constraints — mathematical foundation for closed-world work orders.

### The 6 Levels of AI Delegation
Authorization boundaries adapted from organizational management: "Look into it and report alternatives" (L2) through "Take action and let me know" (L5). Frameworks encode these into work orders to prevent unauthorized state changes and scope creep.

### Markdown Runtimes & Agent Harnesses
- **Obsidian/file-based pipelines:** Local directory as execution workspace; agents treat Markdown as charter and work orders. Portable, human-readable, vendor-independent.
- **Flue / Pi Harness (Cloudflare):** "Markdown blueprints" configure agent behavior; append-only logs (Durable Streams) treat execution history as immutable ledger.
- **MCP integration:** Binding MCP servers to specific Markdown work orders creates closed-world tool/data views. Worth exploring ZeroClaw/Hermes for architectural inspiration.

### Reducing Specification Debt
- **Galileo (Spec-First AI Development):** Build detailed contrast tables ("acceptable output" vs. "hallucinated output") and rubrics upfront. Forces explicit boundaries around edge cases.
- **Automated Requirements Summarization:** Pre-work-order agent pass ingests raw product intent → structured summary highlighting unhandled edge cases, missing backward compatibility, implicit assumptions. Forces spec debt resolution before downstream work.

---

## Source 2: Prior Art & Landscape Analysis

### 1. Closest Prior Art: Spec-First AI Coding Frameworks

| Name | What it is | Relevance |
|---|---|---|
| **GitHub Spec Kit** | SDD workflow: Spec → Plan → Tasks → Implement. Markdown artifacts at each stage. Multi-agent compatible. | Closest "portable artifact bundle" ancestor. Each phase produces Markdown context feeding the next. |
| **AWS Kiro** | IDE-native specs: `requirements.md` or `bugfix.md`, `design.md`, `tasks.md`. | Less portable but same "ambiguous intent → approved artifacts before code" thesis. |
| **BMAD Method** | AI-driven dev framework with specialized agents, guided workflows, planning/architecture/implementation phases. | "Software team in a box." Full SDLC operating model with personas. |
| **OpenSpec** | Lightweight, universal, open-source spec layer for AI coding assistants. Slash-command workflows. | Runtime-agnostic bundle approach. Worth watching. |
| **Tessl** | Spec Registry + framework. Prebuilt specs for open-source libraries. Specs with descriptions, capabilities linked to tests, APIs. | "Spec as executable asset" lineage. Martin Fowler places it closer to spec-as-source. |

**Martin Fowler / Thoughtworks SDD framing:** Distinguishes spec-first, spec-anchored, and spec-as-source. Most tools are spec-first for a change request; our factory is closer to spec-anchored across the product lifecycle.

### 2. Bounded Delegation Patterns

**Paper: "Software Delegation Contracts: Measuring Reviewability in AI Coding-Agent Work"**
- Defines delegated coding work around: task, authority, returned work package, acceptance context.
- Key finding: explicit contracts did not improve correctness on small tasks, but **did** improve reviewability, evidence sufficiency, known-limitations sections, residual-risk reporting, and reviewer checklists. Exactly the "bounded work order + acceptance checks + evidence bundle" concern.

**Practitioner convergence (Addy Osmani et al.):**
- Multi-agent coding as a management problem: clarity, delegation, verification loops, async communication.
- Spec-writing guide: plan-first/read-only work, small task decomposition, boundaries, conformance criteria, human checkpoints.

**Multi-agent infrastructure pattern:**
- Spec-scoped tasks, worktree isolation, coordinator/specialist/verifier roles, automated quality gates, sequential merges. Operational side of "closed-world scope and bounded delegation."

### 3. Prompt Libraries, Reusable Agent Rules, and Skills

| System | Key Pattern |
|---|---|
| **AGENTS.md** | Cross-tool "README for agents." Tens of thousands of projects. Settling conventions. |
| **OpenAI Codex AGENTS.md** | Layered global, repo-level, directory-specific instruction files + `AGENTS.override.md` + fallback filenames. Scoped rules precedent. |
| **Claude Code CLAUDE.md** | Persistent project/user/org instructions + auto memory + path-scoped rules. Warns: context, not hard enforcement (hooks needed for blocking). |
| **Codex Skills** | `SKILL.md` + optional scripts/references/assets/metadata. Progressive disclosure (name/desc/path first, full skill on demand). Modular context pattern. |
| **Cline Memory Bank** | Community-originated structured Markdown context for session persistence. Detects Cursor rules, Windsurf rules, AGENTS.md — cross-tool convergence evidence. |
| **Roo Code custom modes** | Role definition + allowed tools + file access permissions + project/global config. Maps to bounded agent definitions. |
| **Aider conventions** | Lighter-weight ancestor: Markdown convention files read-only for code style, libraries, testing, docs. |

### 4. Agent Harnesses and Runtimes

| System | What it does |
|---|---|
| **OpenHands** | Strongest open-source agent runtime. Code writing, CLI, browsing, sandboxed execution, multi-agent, evaluation benchmarks. Credible target runtime for the artifact bundle. |
| **SWE-agent / mini-SWE-agent** | Formalized "take an issue, operate in a repo, produce a patch" loop. YAML-configured, documented behavior. Task-bounded coding agent prior art. |
| **MCP** | Tool/data/workflow interface layer. Resources, Prompts, Tools — standards-adjacent way to expose factory artifacts to multiple hosts. |
| **Agent Protocol / A2A / Open Agent Spec / AgentSPEX** | If the factory becomes runtime-portable: runs/threads/memory APIs (Agent Protocol), agent-to-agent comms (A2A), declarative agent/workflow definitions (Open Agent Spec), spec/execution language with control flow/verification/checkpointing/logging (AgentSPEX). |

### 5. Artifact-Driven Development: Older Ancestors

- **BDD / Cucumber / Gherkin** — Executable natural-language-ish behavior specs. Oldest pattern for "specs that run."
- **Contract-first API / OpenAPI** — Spec before implementation. Blueprint for endpoints, schemas, error behavior.
- **Architecture Decision Records (ADRs)** — Durable decision-log layer. Charter/constitution/accepted-changes is ADR/RFC thinking generalized for agentic execution.
- **TLA+ / Alloy** — Formal-spec branch. Rigorous machine-checkable intent. TLC/TLAPS for TLA+; Alloy is open-source language/analyzer for software modeling.

### 6. Specification Debt / Intent Debt

**Margaret-Anne Storey's "Triple Debt Model":**
- Technical debt (code), cognitive debt (people), **intent debt** (externalized knowledge). Intent debt: loss of user goals, constraints, and key decisions that humans and agents need to evolve a system safely.

**Requirements debt in AI-enabled systems:**
- Requirements evolve across data, models, safety, cybersecurity, reliability, compliance. When changes aren't documented, validated, traced → auditability and certification readiness degrade.

**Agent configuration smells (June 2026 paper):**
- Context bloat, skill leakage, lint leakage, blind references, init fossilization, conflicting instructions in AGENTS.md/CLAUDE.md files.
- Stats: lint leakage in 62% of sampled files, context bloat in 42%, skill leakage in 35%.
- Key tension: AGENTS.md can reduce runtime/output tokens while maintaining task behavior — but can also reduce task success and increase cost when adding unnecessary requirements.
- **Lesson:** Artifacts need linting, scoping, expiry, ownership, and acceptance semantics, or the specification layer becomes its own debt source.

### 7. Promptware: Term Collision

Two competing meanings:
1. **Software engineering:** Prompt-enabled systems where natural-language prompts are first-class software artifacts. Dev needs requirements, design, testing, debugging, evolution, deployment, monitoring.
2. **Security:** Malicious prompt-based payloads or multi-step malware-like attacks against LLM systems. Worth noting for public-facing use of the term.

### 8. The Differentiated Layer (synthesis)

Most tools solve one slice. The gap:

| Slice | Who owns it |
|---|---|
| Feature specification flow | Spec Kit / Kiro / OpenSpec |
| Role-based AI SDLC choreography | BMAD |
| Repo-level agent context | AGENTS.md / CLAUDE.md / rules |
| Reusable procedures and modes | Codex Skills / Cline / Roo |
| Execution runtime | OpenHands / SWE-agent |
| Reviewability + bounded work | Delegation-contract research |
| Why durable externalized rationale matters | Intent-debt research |

**Our differentiated layer:** the thing that binds these together — a portable factory bundle where every agent run begins from a bounded work order, inherits a charter/constitution, loads only relevant skills/rules/templates, writes back an evidence bundle, and exits through one of three doors: accepted, repair loop, or change order. Upstream CI/CD for intent: clarify, constrain, decompose, execute, verify, record.

### 9. Communities & Feeds to Monitor

- GitHub Spec Kit discussions
- BMAD Method GitHub/community channels
- OpenSpec GitHub/Discord
- OpenHands Slack/GitHub
- Cline / Roo / Cursor forums
- r/ClaudeCode, r/Cursor, r/vibecoding, r/SpecDrivenDevelopment
- Tessl's AI Native Dev ecosystem / AI Native DevCon
- Tessl DevCon tracks: context engineering, agent orchestration, agent enablement platforms, MCP/A2A, secure execution, tracing, cost controls, organizational enablement

### Watchlist Terms

spec-driven development · spec-as-source · context engineering · agent skills · AGENTS.md · CLAUDE.md · software delegation contract · intent debt · requirements debt · agent configuration smells · executable specifications · agent harness · worktree isolation · evidence bundle · AI-native SDLC · promptware engineering

---

> **Next steps:** Consider feeding selected sources into the constitution as research references or ledger entries. The configuration smells paper is especially relevant for designing lint/validation into the work-order lifecycle.

---

## Verification Addendum

> **Filed:** 2026-06-21
> **By:** Claude (builder tier), at Damien's request
> **Method:** Targeted web search against the claims most likely to be fabricated — specific arXiv IDs, precise statistics, and unfamiliar tool names. Well-established industry concepts (Spec Kit, Kiro, BMAD, OpenSpec, Tessl, AGENTS.md/CLAUDE.md, Codex Skills, OpenHands, SWE-agent, MCP, BDD/Gherkin, OpenAPI, ADRs, TLA+/Alloy) were not re-checked — independently corroborated as real, current systems already.

| Claim | Verdict | Detail |
|---|---|---|
| arXiv:2503.02400 "Promptware Engineering" | **Confirmed** | Real paper, accepted ACM TOSEM. Authors: Chen, Wang, Sun, Liu, Zhang, Liu. |
| arXiv:2601.08815 "Agent Contracts" | **Confirmed** | Real paper, accepted COINE 2026 (co-located with AAMAS 2026). Authors: Qing Ye, Jing Tan. |
| Config-smells stats — Lint Leakage 62% / Context Bloat 42% / Skill Leakage 35% | **Confirmed accurate** | Real June 2026 paper, "Configuration Smells in AGENTS.md Files." Widely covered (The Register, InfoWorld, dev.ua); 91 of 100 sampled repos had at least one smell. |
| Cloudflare "Pi Harness" / "Durable Streams" | **Confirmed, loosely named** | Flue (Cloudflare's framework) generates markdown blueprints; Pi (from "Project Think") is the harness Cloudflare features; Durable Streams is a real append-only execution-log pattern in Cloudflare Pipelines. "Pi Harness" isn't Cloudflare's own product name for Pi, but the substance holds. |
| ZeroClaw / Hermes | **Confirmed** | Both real, current agent harnesses. ZeroClaw: Rust runtime, 20k+ GitHub stars. Hermes (Nous Research): markdown-based skill memory under `~/.hermes/skills/`, agentskills.io-compatible. |
| "6 Levels of AI Delegation" | **Confirmed** | Real framework (metaltoad.com); correctly described here as a practitioner framework, not an academic paper. |
| Margaret-Anne Storey, Triple Debt Model / intent debt | **Confirmed, attribution included** | arXiv:2603.22106, "From Technical Debt to Cognitive and Intent Debt." Storey is the author (self-published companion post at margaretstorey.com, Feb 2026; also covered in ACM Queue). |
| "Software Delegation Contracts: Measuring Reviewability in AI Coding-Agent Work" | **Not found under this title** | No paper matching this exact title and finding (null result on correctness; positive result on reviewability, evidence sufficiency, residual-risk reporting) turned up. Adjacent real papers exist — "Adaptive Contracts for Cost-Effective AI Delegation" (arXiv:2603.17212, cost-focused), "Agent Behavioral Contracts" (arXiv:2602.22302, compositional-verification-focused), "Delegation and Verification Under AI" (arXiv:2603.02961, unconfirmed abstract) — but none confirmed as an exact match. **Treat this citation as unverified** until re-traced; the underlying claim is plausible and consistent with the rest of the survey, but shouldn't be cited by title without re-confirming. |

**Net assessment:** the synthesis is substantively reliable — 7 of 8 spot-checked claims confirmed, including the ones that read most suspiciously on a first pass (precise percentages, unfamiliar product names). One citation (the delegation-contracts reviewability paper) could not be traced and should not be treated as load-bearing until re-confirmed.