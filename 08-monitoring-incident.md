---
title: Monitoring, Incident & On-call - AI-native approaches
stage: 8 of 8
status: draft (baseline digest + deep-research run wy0yv88oq; read-only-investigator positioning and the AI-raises-on-call-load mechanism [V]-verified; Cleric/PagerDuty/Bits-AI claims kept from baseline, verify step rate-limited this run)
---

# Monitoring, Incident & On-call

## What this stage is

Detecting, diagnosing, and resolving production problems. AI now investigates incidents, proposes root causes, drafts comms and postmortems, and (carefully) executes remediation. The dominant safe pattern is read-only investigation with a human acting, because a wrong autonomous action during an incident amplifies it. Most of the strongest failure modes here are vendor-confessed.

---

## The ways of doing things

Along the autonomy spectrum, from "AI investigates, human acts" to "AI remediates under approval".

### Approach 1: Read-only autonomous investigator (delegated, safe default)

- **What it is** - The agent investigates an incident end to end and proposes a root cause, but takes no action; a human decides and acts.

- **Tools / assets** - [Cleric](https://cleric.ai/) (read-only by default; vendor-claimed ~5-minute time-to-root-cause, "92% actionable"); [incident.io AI SRE](https://incident.io/ai) (correlates alerts, telemetry, and code changes; "first 80%"); [Rootly AI SRE](https://rootly.com/ai-sre) (verified: "starts investigating the moment an alert fires", runs parallel hypothesis checks, correlates alerts, surfaces root cause with confidence scores); [Resolve AI](https://resolve.ai/product/ai-sre) (verified: correlates code, infrastructure, and telemetry, identifies root cause with a confidence score and an evidence-backed timeline plus dependency chain, and generates remediation PRs for human approval rather than auto-applying).

- **Example workflow** - An alert fires; Cleric correlates signals and proposes a root cause with its reasoning; the on-call engineer verifies and applies the fix.

- **Pros**

  - Large stated RCA cuts (minutes vs an hour-plus) and fewer engineers pulled into each incident.

  - Verification loops reduce blind trust (Cleric tests fixes; Rootly shows confidence and reasoning).

  - Surfaces undocumented dependencies and past-incident memory a human might miss.

- **Cons / failure modes**

  - **Correlation-not-causation RCA.** Cleric's own write-up: high CPU "perfectly correlated with a new deployment", but the real cause was a long-running cron job; the AI confidently blamed the obvious correlation.

  - **Hidden dependencies break naive agents** - a Redis instance added months earlier by a departed engineer, absent from diagrams; the agent cannot reason about what it cannot see.

  - **No ground truth to verify against** - Cleric found only 12 clear root causes in 200+ postmortems, so "92% actionable" is not "92% correct."

- **When to adopt** - Broadly; this read-only pattern is the safe default for AI in incidents.

### Approach 2: Gated auto-remediation (higher autonomy)

- **What it is** - The agent can execute remediation, but only pre-approved actions or with explicit human sign-off.

- **Tools / assets** - [PagerDuty Advance](https://www.pagerduty.com/platform/ai/) (SRE Agent with approved auto-remediation; Scribe for postmortems; AIOps with vendor-claimed 91% alert reduction); [Resolve AI](https://resolve.ai/) (agents join on-call rotations; vendor-claimed 5x faster MTTR).

- **Example workflow** - A known, runbook-documented failure recurs; the agent applies the pre-approved remediation and logs it for review.

- **Pros**

  - Fast resolution of well-understood, runbook-covered failures.

  - Keeps humans for the novel cases.

- **Cons / failure modes**

  - **Autonomy risk** - a wrong autonomous action amplifies an incident; nearly every vendor defaults to read-only or explicit sign-off, which is itself an admission.

  - **Telemetry and runbook quality is a hard dependency** - auto-remediation only works where good runbooks already exist; bad telemetry yields garbage RCA.

- **When to adopt** - Only for well-understood failures with trustworthy runbooks, always approval-gated; never as the default response.

### Approach 3: AIOps correlation and postmortem drafting (assist)

- **What it is** - Classic ML event-correlation cuts alert noise underneath, and AI drafts postmortems and comms for humans to finish.

- **Tools / assets** - [Datadog Watchdog / Bits AI](https://www.datadoghq.com/product/platform/watchdog/); [Grafana Sift](https://grafana.com/products/cloud/) (free ML diagnostic checks); PagerDuty Scribe (postmortems).

- **Pros**

  - AIOps genuinely cuts alert volume, reducing fatigue.

  - Auto-drafted postmortems and comms save real time on the busywork.

- **Cons / failure modes**

  - **AI postmortems automate away the learning** - the process, not the artifact, produces organisational learning (Rootly's own blog makes this point).

  - Correlation noise reduction can also suppress a weak-but-real early signal.

- **When to adopt** - AIOps broadly; postmortem drafting as a starting point only, with humans owning the analysis and the learning.

---

## What goes wrong at this stage

- **Failure modes** - confident wrong RCA from correlation; blind spots from undocumented dependencies; no ground truth to score accuracy; auto-remediation amplifying incidents; automating away the learning; AI-accelerated shipping increasing incident load.

- **Early warning signs**

  - RCA confidently citing the most recent/obvious change repeatedly.

  - Agents stumped by anything not on the architecture diagram.

  - Postmortems getting longer and learning getting thinner.

  - On-call load rising as upstream AI coding raises change volume. This is now independently corroborated (verified): teams with higher AI adoption show a higher change-failure rate (DORA 2025 models it rising from roughly 5% to 6% post-adoption), through three mechanisms (LeadDev): AI code passes review by pattern-matching so reviewers skip the hard reasoning on side effects and edge cases; self-graded tests give false coverage confidence; and the model lacks broader-system awareness, so a clean-looking refactor can silently remove something critical. Caveat (the amplifier thesis): teams with strong existing practices saw failure rates fall, so AI amplifies the delivery system you already have.

- **Mitigations**

  - Read-only by default; verify before acting; do not trust correlation as cause.

  - Keep runbooks, telemetry, and dependency maps current; the agent is only as good as these.

  - Humans own postmortem learning; AI drafts only.

---

## Who does what

| Task | AI does | Human does | Owning role | Handoff point |
|------|---------|-----------|-------------|---------------|
| Detect / correlate | Cuts noise, groups alerts | Tunes thresholds | On-call / SRE | On suppressed-but-real signals |
| Root-cause analysis | Investigates, proposes cause | Verifies before trusting | On-call | Before any action |
| Remediation | Executes pre-approved actions | Approves / owns novel cases | On-call / SRE lead | Approval gate (always) |
| Postmortem | Drafts the artifact | Owns the analysis + learning | Incident owner / EM | At the learning step |

---

## Expectations to set, and with whom

- **On-call / SRE** - AI RCA is a hypothesis to verify, not a verdict. It will confidently blame the obvious correlation; check it, especially for anything off the diagram.

- **Engineering leadership** - Vendor MTTR and "actionable" numbers are self-reported and not the same as "correct"; auto-remediation is a runbook-covered-cases tool, not a default. And AI-accelerated shipping raises on-call load, so staff and guardrail for it.

- **The team** - Postmortems exist to make us learn; AI drafts the document, humans do the learning. Do not let the artifact replace the process.

---

## Guardrails

| Guardrail | Protects against | Citation |
|-----------|------------------|----------|
| Read-only by default; human-approved remediation | Wrong autonomous action amplifying an incident | Industry default (Cleric, Resolve, PagerDuty) |
| Verify; do not trust correlation as cause | Mis-RCA | Cleric cron-vs-deploy example |
| Humans own postmortem learning; AI drafts only | "Automating away the learning" | Rootly blog |
| Maintain runbook / telemetry / dependency quality | Blind-spot RCA | Cleric departed-engineer Redis example |

Pipeline-wide guardrails live in [09-cross-cutting](09-cross-cutting.md).

---

## Likely costs

- **Direct / tooling** - Cleric ($4.3M seed); Resolve ($125M Series A); incident.io; PagerDuty Advance; Datadog; Grafana.

- **Hidden / operational** - vendor MTTR/accuracy claims are unaudited; the dependency on current runbooks/telemetry; AI-accelerated shipping raising on-call load.

- **Metrics to watch** - verified-correct RCA rate (not just "actionable"); incidents from undocumented dependencies; on-call load trend vs change volume; postmortem learning quality.

---

## Roles: 2023 to mid-2026

**2023 baseline.** On-call engineers and SREs detected and diagnosed incidents; an incident commander coordinated; the team wrote postmortems; an EM facilitated the learning and follow-ups.

**Mid-2026 split.** AI investigates (read-only) and drafts postmortems; on-call verifies the RCA and acts; the incident-commander role persists for coordination; the EM still owns the learning. An uncomfortable new shift: AI-accelerated shipping upstream raises incident and on-call load downstream. (Synthesis, per Resolve's account.)

## Where Claude alone helps

- **`/loop`** - poll a dashboard or deploy status on an interval. *Pro:* lightweight, foreground or detached. *Con:* it interprets dashboards, but cannot act on metrics or receive alerts.

- **`/schedule` + background / cloud agents** - run scheduled checks as cron routines that survive logout. *Pro:* true background, timezone-aware. *Con:* coarse granularity; no native PagerDuty/Opsgenie hook.

- **MCP servers (Datadog, Sentry)** - query logs, traces, and errors in-session. *Pro:* live data, no context switch. *Con:* large results flush context; platform rate limits apply.

- **`/deep-research`** - synthesise related issues, logs, and past postmortems during diagnosis. *Pro:* speeds MTTR and cross-references history. *Con:* an async snapshot, not live, with no alerting hook.

*Honest gap:* no on-call-rotation awareness, no alerting integration, no runbook execution or auto-remediation.

## Commentary: is this phase still distinct?

Incident response loops back to the start. A verified RCA becomes a fix PR ([04](04-implementation.md)) that re-enters review and CI, so the "last" phase feeds the "first." It stays distinct because production has no undo and the verify-before-acting discipline is non-negotiable, but the line between operating and building is thinner than it was, especially as the same agents work both sides.

---

## Sources

*(Baseline digest 2026-06-20, hardened with deep-research run `wy0yv88oq`. The read-only-investigator positioning and the AI-raises-on-call-load mechanism verified cleanly; the Cleric / PagerDuty / Bits-AI specifics were not re-verified (verify step rate-limited) and keep their baseline tags. All vendor MTTR/accuracy figures are vendor self-reported. Tags: [V] adversarially verified, [P] primary, [S] secondary / vendor.)*

- [V] LeadDev, "AI coding made us faster. Why did incidents increase?" (the three mechanisms). https://leaddev.com/ai/ai-coding-made-us-faster-why-did-incidents-increase
- [V] DORA 2024 / 2025 (AI adoption to higher change-failure rate; the amplifier thesis). https://dora.dev/dora-report-2025/
- [V] Resolve AI SRE product (correlate code/infra/telemetry; confidence score + evidence timeline; remediation PRs for human approval). Note the "73% faster" Coinbase testimonial is vendor-claimed with no published methodology. https://resolve.ai/product/ai-sre
- [V] Rootly AI SRE (auto-trigger on alert; parallel hypotheses; confidence scores). https://rootly.com/ai-sre
- [P] Cleric, "The Hidden Complexity of Building an AI SRE" (cron-vs-deploy; departed-engineer Redis; 12-of-200 ground truth; not re-verified this run). https://cleric.ai/blog
- [P] Rootly blog (postmortems automate away the learning). https://rootly.com/blog
- [S] incident.io AI SRE; PagerDuty Advance; Datadog Watchdog; Grafana Sift (linked inline).
