---
name: deep-research
description: Research a topic using multiple sources, cross-verify claims, and synthesize findings into a cited report. Invoke for competitive analysis, due diligence, market research, or any question requiring thorough, sourced answers.
profile: standard
maturity: canonical
origin: kst_promptware
---

# Deep Research

Produces a multi-source, cited research report. Designed for questions where a quick answer is insufficient and where you need to know what you don't know.

## When to Invoke

- Competitive analysis or technology landscape surveys
- Due diligence on a library, vendor, or approach before adoption
- Market sizing or user research synthesis
- Background research before writing a spec or philosophy document
- Any question where the first answer might be wrong or incomplete

Trigger keywords: "research", "deep dive", "investigate", "survey", "what's the state of", "compare X and Y".

## Procedure

**Step 1 — Clarify scope (ask before searching)**

Confirm:
- What is the specific question? (narrow it: "best state management library for React in 2026" not "state management")
- What will the output be used for? (shapes depth and format)
- What time horizon matters? (last 6 months? last 3 years?)
- What's out of scope?

**Step 2 — Decompose into sub-questions**

Break the topic into 3–5 research sub-questions that together answer the main question. Example for "should we use Postgres or MongoDB for this project?":
- What are the access patterns? (read-heavy vs write-heavy, relational vs document)
- What does each do well / poorly at the scale we need?
- What does the ecosystem look like in 2026? (tooling, managed services, community)
- What have teams with similar workloads reported?

**Step 3 — Research**

For each sub-question:
- Search multiple sources (don't stop at the first result)
- Prioritize: official docs, peer-reviewed papers, reputable engineering blogs, recent conference talks
- Prefer sources from the last 12 months for fast-moving topics
- Note the source and date for every claim you'll include

**Step 4 — Cross-verify**

For every significant claim:
- Find at least one corroborating source
- Flag claims that appear in only one source as "unverified"
- Flag claims where sources disagree — don't silently resolve the conflict

**Step 5 — Synthesize**

Structure the report:

```markdown
# Research Report: <Topic>

**Date:** YYYY-MM-DD
**Question:** <The specific question answered>
**Confidence:** High / Medium / Low (overall)

## Executive Summary
<3–5 bullet points: the most important findings>

## Findings

### <Sub-question 1>
<Findings with inline citations: Author/Source, Year>

### <Sub-question 2>
...

## Conflicting Information
<Any claims where sources disagree — present both sides>

## Gaps
<Questions this research could not answer, and why>

## Recommendation (if applicable)
<Only if the human asked for a recommendation. State it directly.>

## Sources
1. <Full citation>
2. ...
```

**Step 6 — Deliver**

- Short reports (under 500 words): deliver inline
- Long reports: offer to write to a file (`references/<topic-kebab>.md` or `docs/research/<topic-kebab>.md`)

## Quality Bar

- Every claim that drives the recommendation must have at least one cited source
- The Gaps section is required — research without acknowledged limits is not trustworthy
- Do not present inferences as facts; label them clearly ("this suggests...", "one interpretation is...")
