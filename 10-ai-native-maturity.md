---
title: How AI-native is your team? A maturity model
stage: cross-cutting
status: draft (built from the verified baseline digest + cross-cutting evidence)
---

# How AI-native is your team? A maturity model

"AI-native" is not a binary. A team is somewhere on a spectrum, and usually at *different* points for *different* stages: a team can run autonomous coding agents while still triaging every ticket by hand. This chapter gives a five-level model, a way to self-assess, and per-stage guidance on how to capture the next increment of benefit without inheriting the next increment of risk.

The governing principle from [09-cross-cutting](09-cross-cutting.md) holds at every level: **move right on the autonomy spectrum only where reversal is cheap and detection is easy, and add the gate before you add the autonomy.**

---

## The five levels

### Level 0 - AI-curious (ad-hoc)

- **What it looks like** - Individuals use AI tools privately. No shared practice, no policy, no guardrails. Quality and habits vary person to person.

- **What good looks like here** - Permission to experiment; a channel to share what works.

- **Risk at this level** - Shadow usage with no security or IP guardrails; insecure or unexplained code merged quietly.

### Level 1 - AI-assisted

- **What it looks like** - Autocomplete and IDE chat are standard and sanctioned. Humans still own every process step. Few guardrails beyond "review your own output."

- **What good looks like** - Consistent tool access; a basic policy (explainability, no secrets in prompts); awareness of the failure modes.

- **Risk at this level** - The velocity illusion begins (feels faster, may not be); over-trust of autocomplete; no measurement.

### Level 2 - AI-augmented

- **What it looks like** - AI participates as an advisor across multiple stages: review bots, test generation, triage suggestions. Guardrails exist (human approval gates, security scanning). Metrics are emerging.

- **What good looks like** - Tuned tools (managed review noise); outcome metrics tracked (stability, escaped defects, rework), not just activity; named human accountability per change.

- **Risk at this level** - Review-burden inversion; rubber-stamping; metric gaming if you measure activity not outcomes.

### Level 3 - AI-delegated

- **What it looks like** - Bounded autonomy in production: agent PRs on low-blast-radius work, autofix-with-review, read-only incident investigators, spec-driven task pipelines. Reviewer capacity is explicitly managed as the constraint.

- **What good looks like** - Gates before autonomy everywhere; dependency allowlists; strong CI; the apprenticeship deliberately protected; outcome instrumentation from day one.

- **Risk at this level** - Compounding error across stages; reviewer overload; security blast radius of agent tooling; skill atrophy if juniors are designed out.

### Level 4 - AI-native

- **What it looks like** - The pipeline is *designed around* agents. Humans set intent and own the gates; agents do most of the legwork across stages. Spec-driven where it pays; security, guardrails, and outcome instrumentation are first-class, not bolted on.

- **What good looks like** - The team's edge is its guardrails and judgement, not its typing speed. Stability holds while throughput rises (the DORA 2025 "amplifier" case, for a team with strong foundations).

- **Risk at this level** - The failure modes do not disappear, they concentrate; a weak foundation is amplified, not fixed. Over-reach into customer-facing or unattended autonomy (the Klarna lesson) is the standing temptation.

---

## Self-assessment: where is your team?

Score each stage independently. A team is rarely one level overall.

| Signal | Suggests level |
|--------|----------------|
| AI use is private and unmanaged | 0 |
| Autocomplete/chat sanctioned, no process change, no metrics | 1 |
| AI advises in review/test/triage; human gates exist; metrics emerging | 2 |
| Bounded agent autonomy in production; reviewer capacity managed; gates before autonomy | 3 |
| Pipeline designed around agents; intent + gates owned by humans; outcomes instrumented | 4 |

Two diagnostic questions that cut through self-flattery:

1. **Can you see the cost?** If you cannot quote your change-fail rate, rework/churn, and reviewer load, you are at most Level 1 regardless of how many tools you run. (METR's perception gap is the warning: "felt faster" is not data.)

2. **What happens when the agent is wrong?** If the answer is "we'd probably merge it", your guardrails are behind your autonomy, and you are over-leveled and exposed.

---

## How to capture the next increment, per stage

The pattern is the same everywhere: adopt the cheapest-to-reverse approach first, instrument the outcome, add the gate, then delegate.

| Stage | Cheap first win (L1 to L2) | Earn the next level (L3+) | The gate that must come first |
|-------|----------------------------|---------------------------|-------------------------------|
| [01 Intake](01-intake-triage.md) | Suggest-then-confirm labelling/dedup | Bounded auto-apply; agent first-fix on low-risk issues | Reviewer-capacity budget; suggest-then-confirm on destructive actions |
| [02 Spec](02-spec-design.md) | AI-drafted specs, human-owned | Gated spec-driven development | Spec-size cap + real spec-review budget |
| [03 Planning](03-planning-decomposition.md) | Plan-mode before edits | Dependency-aware task pipelines | Plan approval treated as real review |
| [04 Implementation](04-implementation.md) | Autocomplete + IDE chat, diffs read | Agentic CLI, then bounded background agents | Mandatory diff review; dependency allowlist |
| [05 Review](05-code-review.md) | AI review as advisory | Codebase-aware review on large repos | Human approval gate; sandboxed review bots |
| [06 Testing](06-testing-qa.md) | AI test-gen with assured filter | Mutation-guided hardening; autonomous QA | Human assertion review |
| [07 Ship/CI](07-ship-ci.md) | Autofix suggestions, human-applied | Agent-fixes-CI; behavioural supply-chain gate | No auto-merge on confidence alone |
| [08 Incident](08-monitoring-incident.md) | AIOps noise reduction; AI postmortem drafts | Read-only RCA agents | Read-only default; verify before acting |

Guidance:

- **Do not level up every stage at once.** Pick the one or two stages where the cost is highest and the reversal is cheapest, prove the outcome, then move on.

- **Level the guardrails before the autonomy.** A stage at Level 3 autonomy with Level 1 guardrails is the most dangerous configuration in this whole document.

- **Measure the same things at every level.** Stability, rework, escaped defects, reviewer load. If those hold or improve as you move right, you are genuinely getting more AI-native. If they degrade, you are just moving risk around.

- **Protect the apprenticeship as you climb.** The higher the level, the stronger the pull to design juniors out; resist it deliberately (Yegge's "death of the junior developer" is the failure mode).

---

## Case study: an opinionated AI-native model (Mindset AI)

Most of this guide describes AI-native development of *code*. It helps to look at a productized, opinionated model of AI-native delivery to see where the strong opinions land. [Mindset AI](https://mindset.ai/) is a "frontend agentic platform" that lets B2B SaaS teams embed agents into their products (2,700+ companies). Its tooling bakes in a three-layer loop:

- **Build** - a no-code Agent Management Studio, a developer SDK, and generative-UI widgets.
- **Runtime** - live agents, generative UI, MCP servers for integration.
- **Observe and improve** - observability, performance testing, and agent memory, with the explicit stance that improvement comes from **real production session data, not synthetic evals alone**, "from the first use case to the tenth and beyond."

Three things worth stealing from this opinionated model, whether or not you use the product:

1. **Treat "observe and improve" as a first-class stage, not an afterthought.** The loop does not end at ship; production usage is the evaluation set. This reinforces the outcomes-over-proxies stance in [09](09-cross-cutting.md).

2. **Production data beats synthetic evals.** An opinion the broader evals literature shares (Hamel Husain's error-analysis work, see [11](11-resources.md)). Synthetic benchmarks flatter; real sessions tell the truth (compare the toy-benchmark-vs-real-code collapse in [06](06-testing-qa.md)).

3. **Split the builder and developer roles deliberately.** A no-code studio for product people, an SDK for engineers. That is the role-evolution theme of every stage chapter, made explicit in a product.

## A gap this guide inherits: post-ship AI-feature operations

Mindset's whole product lives in a stage our intake-to-ship map under-weights: the **operations and evaluation loop for AI features after they ship** (agent observability, eval-from-production, agent memory, prompt and version management). Chapter [08](08-monitoring-incident.md) covers monitoring the *system*; it does not cover evaluating an *AI feature's* behaviour over time. For a team shipping agentic features, that loop is a distinct, emerging discipline (often called "AgentOps" or "LLMOps"). Treat it as a ninth stage to watch, even though this guide does not yet give it its own chapter. (Inference, flagged.)

---

## Sources

Built on the cross-cutting evidence in [09-cross-cutting](09-cross-cutting.md): DORA 2024/2025 (the "amplifier" thesis), METR 2025 (the perception gap), and the per-stage failure modes and guardrails in chapters 01-08. The level definitions and progression guidance are my synthesis (inference), grounded in that evidence rather than extracted from a single source.
