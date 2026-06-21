---
title: AI-native workflows, intake to ship - overview
stage: overview
status: draft (all chapters built from the verified baseline digest; 01 + 04 [V]-hardened)
---

# AI-native workflows, intake to ship

A team-adoption reference. For each stage of the software lifecycle it lays out the competing "ways of doing things" with AI, the concrete pros and cons of each, who does what, what to expect and from whom, what goes wrong (and how to spot it early), the guardrails and what they protect against, and the likely costs.

It is written to be **annotated**: leave your reactions inline as `>` blockquotes, and nest deeper thoughts as `>>`. The prose is spaced to give you room under any point.

---

## The map

| # | Stage | One-line |
|---|-------|----------|
| [01](01-intake-triage.md) | Intake & Triage | Signal in, work queued: label, dedup, route, sometimes resolve |
| [02](02-spec-design.md) | Spec & Design | Intent into a reviewable spec; spec-driven development |
| [03](03-planning-decomposition.md) | Planning & Decomposition | Plan-and-approve before edits; task breakdown |
| [04](04-implementation.md) | Implementation | The coding autonomy spectrum: autocomplete to autonomous agents |
| [05](05-code-review.md) | Code Review | The quality gate, increasingly AI-on-AI |
| [06](06-testing-qa.md) | Testing & QA | "Looks done" vs "is correct" |
| [07](07-ship-ci.md) | Ship, CI & Deploy | Red-build fixes, risk scoring, supply-chain defence |
| [08](08-monitoring-incident.md) | Monitoring, Incident & On-call | Detect, diagnose, remediate |
| [09](09-cross-cutting.md) | Cross-cutting | The evidence, pipeline-wide risk, the adoption charter |
| [10](10-ai-native-maturity.md) | Maturity model | How AI-native is your team, level-up guidance, a Mindset AI case study, and the post-ship "AgentOps" gap |
| [11](11-resources.md) | Resources | Studies, essays, books, newsletters, and skills for further reading |
| [12](12-adoption-playbook.md) | Adoption playbook | The executable rollout path, grounded in real-world experience |

Start with [09-cross-cutting](09-cross-cutting.md) for the big picture (the evidence and the pipeline-wide risks), then read the stages in order, and use [10](10-ai-native-maturity.md) to place your team and plan the next increment.

---

## The spine: the autonomy spectrum

Every stage chapter arranges its approaches along the same axis. It is the single most useful lens in this doc:

```
human-led  ->  AI-assisted  ->  AI-delegated (bounded)  ->  autonomous
(suggest)      (you accept)     (acts within guardrails)    (acts alone)
```

The further right you go, the more leverage and the larger the blast radius of a wrong action. The recurring lesson across every stage: **move right only where reversal is cheap and detection is easy, and add the gate before you add the autonomy.**

---

## Are the phases still distinct? (the short answer)

Each chapter ends with this question for its own stage. The pattern across all of them: **the boundaries are collapsing, and the collapse runs in both directions.**

- **Forward collapse.** Spec, planning, and implementation are fusing into a single intent-to-PR motion. Assign an issue to an agent and it specs, plans, codes, tests, writes the PR, and triggers review and CI without a human touching the middle.

- **Backward collapse.** The pipeline now loops. A failed CI build becomes a fix PR that re-enters review; a verified incident RCA becomes a fix PR that re-enters implementation. The "last" phases feed the "first."

- **What stays distinct, and why.** Three phases resist the collapse because a human must own them: **spec** (someone owns intent and correctness), **code review** (someone owns the gate), and **deploy/incident** (production has no undo). These are exactly the stages where the guardrails in [09](09-cross-cutting.md) concentrate. The phases that are dissolving are the ones AI can do unattended; the ones that survive are the ones that exist to catch AI when it is wrong.

So "are the phases distinct" is really the same question as "where do you keep a human in the loop." The map is less a conveyor belt now and more a loop with a few human-owned gates on it.

**One emerging stage this map under-weights:** the post-ship operations and evaluation loop for AI *features* themselves (agent observability, eval-from-production, agent memory). For teams shipping agentic features it is a distinct discipline ("AgentOps"). See the Mindset AI case study and gap-flag in [10](10-ai-native-maturity.md).

---

## How to read and annotate

- Each stage chapter follows the same shape: *what it is -> the ways of doing things (with pros/cons) -> what goes wrong -> who does what -> expectations -> guardrails -> likely costs -> roles (2023 to mid-2026) -> where Claude alone helps -> commentary on phase distinctness -> sources*.
- Annotate inline: `>` for a reaction, `>>` for a nested or follow-on thought. I will not overwrite your annotations.
- Vendor metrics are labelled "vendor-claimed"; anything I reasoned rather than sourced is labelled "inference". Citations sit in each chapter's Sources.

---

## Status

All ten files are drafted. Chapters 01 (Intake) and 04 (Implementation) are hardened with adversarially-verified citations (tagged `[V]`); the rest are built from the verified baseline cross-corpus digest with primary `[P]` and secondary `[S]` citations. A second pass of per-stage deep-research (to upgrade `[S]` to `[V]`) is deferred until the research session limit resets, then optional.

**Citation tags** used throughout: `[V]` adversarially verified, `[P]` primary source fetched (verify step rate-limited), `[S]` secondary or vendor-claimed.
