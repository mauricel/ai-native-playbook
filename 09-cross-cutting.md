---
title: Cross-cutting - evidence, pipeline-wide risk, and the adoption charter
stage: cross-cutting
status: draft (seeded from baseline digest; deep-research run pending to enrich)
---

# Cross-cutting: evidence, pipeline-wide risk, and the adoption charter

This chapter holds what does not belong to any single stage: the productivity and quality evidence base, the failure modes that compound *across* the whole intake-to-ship pipeline, the expectations charter that ties the per-stage notes together, and the rollout sequence.

---

## The evidence base (cite these across the stages)

These are the load-bearing studies. Two framing corrections first, because they are widely repeated wrong.

- **DORA 2024 is not "throughput up, stability down".** Both delivery metrics moved negative. A modelled 25% increase in AI adoption was associated with +7.5% documentation quality, +3.4% code quality, +3.1% review speed, but **-1.5% delivery throughput and -7.2% delivery stability**. 39% of devs trusted AI output "a little / not at all". https://dora.dev/research/2024/dora-report/ *(exact decimals are single-source from Google's blog; not PDF-confirmed.)*

- **DORA 2025** ("AI as amplifier"): ~90% now use AI at work; distrust eased to ~30%; the throughput relationship flipped positive, but **AI still shows a negative relationship with delivery stability**. Thesis: "AI doesn't fix a team; it amplifies what's already there." https://dora.dev/research/2025/dora-report/

- **METR 2025 RCT: experienced devs ~19% slower.** 16 experienced open-source devs, 246 tasks on their own mature repos. AI access **increased** completion time 19%. The perception gap is the killer: they forecast 24% faster, and still believed ~20% faster *afterward*. https://arxiv.org/abs/2507.09089 · https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/ *(Caveat: small sample (n=16) and a single early-2025 snapshot; METR posted a Feb-2026 methodology/uplift update at https://metr.org/blog/2026-02-24-uplift-update/. Cite as the early-2025 result and frame cautiously.)*

- **GitHub Copilot "55% faster"** is real but narrow: one synthetic green-field task, n=95 self-selected, wide CI (21-89%), speed-only, 3 of 4 authors vendor-affiliated. https://arxiv.org/abs/2302.06590 Counter-evidence: METR -19%; Uplevel (~800 devs) found +41% bugs and no throughput gain.

- **Stack Overflow 2025: adoption up, trust down.** 84% use/plan AI, but trust in accuracy fell to 33% and distrust rose to 46%; 66% hit "almost right but not quite", 45% say debugging AI code is *more* time-consuming. https://survey.stackoverflow.co/2025/

- **GitClear: code-quality decay.** Churn (lines reverted within 2 weeks) projected to roughly double 2024 vs 2021; copy-pasted code rose 8.3% to 12.3% and overtook "moved" (refactored) code for the first time. https://www.gitclear.com/ai_assistant_code_quality_2025_research

- **Security.** Stanford (CCS 2023, n=47): AI-assisted users wrote "significantly less secure code" yet were "more likely to believe they wrote secure code". https://arxiv.org/abs/2211.03622 · Veracode 2025: AI code introduced flaws in 45% of tests. · Slopsquatting (USENIX Security 2025): package-name hallucination 5.2% (commercial) / 21.7% (open) models, 43% recur identically across reruns, so they are squattable. https://arxiv.org/abs/2406.10279

- **Practitioner voices** (verified phrasings): Simon Willison won't commit code "I couldn't explain"; Thomas Ptacek ("My AI Skeptic Friends Are All Nuts") is bullish; Birgitta Böckeler frames AI as an "eager yet unreliable" donkey and leads on accountability; Addy Osmani's "70% problem" (the last 30% is diminishing returns); Steve Yegge's "Death of the Junior Developer"; Mitchell Hashimoto runs one agent, not a swarm.

---

## Pipeline-wide failure modes (these compound across stages)

- **Compounding error across stages** - a hallucinated spec seeds wrong tasks, which seed wrong code, which a tired reviewer waves through. Each stage's "almost right" multiplies.

- **Context loss between agents/handoffs** - intent decays as it passes spec to plan to code to review; no single agent holds the whole picture (context rot, Chroma).

- **Automation complacency** - the more the pipeline works, the less anyone reads the diffs; over-trust scales with apparent reliability (Karpathy "Accept All").

- **Velocity illusion** - the pipeline *feels* faster while delivery stability falls and rework rises (METR perception gap, DORA stability, GitClear churn).

- **Security and IP** - insecure-by-default generation, slopsquatting, and AI tools as new attack surface (Kudelski RCE, Socket).

- **Skill atrophy / "death of the junior"** - if AI does the first 70% everywhere, where do mid-level engineers come from?

- **Metric gaming** - coverage, deflection rate, and PR count all look great while the thing they proxy for (correctness, customer value, real progress) does not move.

### Early-warning signals for the pipeline as a whole

- Delivery stability (change-fail rate, time-to-restore) trending down while throughput trends up.
- Rework / churn rate rising; refactored-vs-pasted code ratio falling.
- Reviewer queue depth growing faster than merged output.
- "Felt faster" sentiment diverging from cycle-time data.

### Mitigation playbook

- **Explainability gate** - never merge code no human can explain (Willison).
- **Outcomes over velocity** - track stability + escaped defects, not throughput or activity counts.
- **Named human accountability** per change (Böckeler).
- **Default security scanning** on AI output; dependency allowlists + lockfiles against slopsquatting.
- **Protect the apprenticeship** - deliberately route some learnable work to juniors instead of agents.

---

## The expectations charter (cross-stage, by audience)

The per-stage "Expectations" notes ladder up to one message:

- **Devs / ICs** - AI is a fast, unreliable first draft. You own correctness and you must be able to explain what you ship. Velocity is not the goal; working software is.

- **Reviewers** - You are the constraint now, and that is by design. Review is real, budgeted work, not a rubber stamp. Reject symptom-fixes and vacuous tests.

- **QA** - A passing test is not a meaningful test. Guard against coverage-as-vanity and self-healing masking regressions.

- **PM / product** - Expect faster first drafts, not faster ship. The bottleneck moved to review and verification; plan around it.

- **Leadership / execs** - The evidence does not support "AI makes us N% faster" as a planning assumption (METR, DORA). AI amplifies an existing team; it does not fix a broken one. Invest in the guardrails or inherit the instability.

- **Customers** (where AI is customer-facing) - A human is always one tap away.

---

## Rollout sequencing (adoption order)

Inference, drawing on the evidence above (labelled as my recommendation, not a sourced claim):

1. **Start where reversal is cheap** - intake suggestions, autocomplete, AI review as advisory.
2. **Add the gates before the autonomy** - plan-mode approval, human merge gate, security scanning, dependency allowlists.
3. **Then delegate bounded tasks** - agent first-fix on low-blast-radius issues, autofix-with-review.
4. **Instrument outcomes from day one** - stability, rework, escaped defects, reviewer load. If you cannot see the cost, you will not catch it.
5. **Only then consider customer-facing or auto-remediation autonomy**, always read-only-by-default or approval-gated.

---

## Sources

*(Seeded from the cross-corpus research digest, 2026-06-20. To be finalised and confidence-tagged from the cross-cutting deep-research run. All vendor metrics are vendor-claimed unless from a primary study above.)*

- DORA 2024 / 2025: https://dora.dev/research/2024/dora-report/ · https://dora.dev/research/2025/dora-report/
- METR 2025: https://arxiv.org/abs/2507.09089
- GitHub Copilot study: https://arxiv.org/abs/2302.06590
- Stack Overflow 2025: https://survey.stackoverflow.co/2025/
- GitClear: https://www.gitclear.com/ai_assistant_code_quality_2025_research
- Stanford security: https://arxiv.org/abs/2211.03622 · Slopsquatting: https://arxiv.org/abs/2406.10279
