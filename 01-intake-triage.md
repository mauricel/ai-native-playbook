---
title: Intake & Triage - AI-native approaches
stage: 1 of 8
status: draft (baseline digest + deep-research run wm7aa2hy4, verified citations folded in)
sources: see Sources at bottom
---

# Intake & Triage

## What this stage is

Everything between "a signal arrives" and "a unit of work is queued for someone": incoming bug reports, feature requests, customer support messages, error events, and alerts get labelled, deduplicated, prioritised, routed, and sometimes resolved before any engineer touches them. It is the highest-volume, lowest-context stage, which is exactly why it is the most tempting to automate and the easiest to over-automate.

---

## The ways of doing things

Arranged along the autonomy spectrum, from "AI suggests, human decides" to "AI resolves without a human".

### Approach 1: Suggest-then-confirm (human-led, AI-assisted)

- **What it is** - AI proposes a label, severity, duplicate-link, or assignee; a human accepts or overrides with one click. The human stays the decision-maker.

- **Tools / assets** - [Linear Triage Intelligence](https://linear.app/docs/triage) (suggests labels, priority, and the right team/assignee on each Triage item); [Sentry AI issue grouping](https://docs.sentry.io/product/issues/grouping-and-fingerprints/) (clusters duplicate error events into one issue).

- **Example workflow** - A bug lands in Linear Triage. Triage Intelligence suggests `area/billing`, priority `High`, and routes to the Payments team. The on-duty triager glances, confirms, and it is in the right backlog in seconds.

- **Pros**

  - Reversible by construction: a wrong suggestion costs one click, not a cleanup.

  - Keeps a human mental model of what is coming in, so the team still notices trends.

  - Low trust barrier, so it is the easy first step for a sceptical team.

- **Cons / failure modes**

  - Suggestion fatigue: if quality is mediocre, triagers start rubber-stamping, and you get auto-apply behaviour without the safety.

  - Throughput ceiling stays human-bound; you save clicks, not headcount.

- **When to adopt** - Day one. This is the lowest-risk entry point and the one to standardise on before going further.

### Approach 2: Bounded auto-apply (AI-delegated within rules)

- **What it is** - AI applies labels, merges duplicates, and routes automatically, but only inside guardrails (confidence thresholds, allow-listed actions). Humans audit a sample, not every item.

- **Tools / assets** - Linear Triage Intelligence in auto-apply mode; [Atlassian Rovo](https://www.atlassian.com/software/rovo) + the Jira Service Management Virtual Agent for auto-routing.

- **Example workflow** - Inbound JSM tickets are auto-categorised and routed; the Virtual Agent answers the known-FAQ ones; anything below a confidence threshold falls through to a human queue.

- **Pros**

  - Real headcount leverage on high-volume, low-variance intake.

  - Consistent taxonomy: the labels stop drifting between triagers.

  - Reported labelling-effort reductions around 70% (vendor-context, verify).

- **Cons / failure modes**

  - **Over-grouping hides real problems.** Sentry's own engineering blog calls over-grouping "arguably the more sinister failure mode" because it buries a distinct issue inside an unrelated cluster; their v1 over-grouped roughly 8% in aggregate and 30-60% on some projects.

  - **Destructive autonomous edits.** Practitioners report Rovo "erased a whole page" - a class of risk that only exists once the AI can act unattended.

  - Un-disableable noise and cost unpredictability when the volume scales.

- **When to adopt** - After Approach 1 is trusted, and only on categories where a wrong action is cheap to reverse and easy to detect.

### Approach 3: Agent-as-teammate (agentic triage and first-fix)

- **What it is** - An AI agent is assigned an issue like a junior engineer would be: it investigates, reproduces, and either proposes a root cause or opens a draft PR.

- **Tools / assets** - [GitHub Copilot coding agent](https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent) (assign an issue with `Copilot` as assignee, get a draft PR from an ephemeral GitHub Actions environment); [Sentry Seer](https://docs.sentry.io/product/issues/issue-details/sentry-seer/) (scans incoming issues for root cause, then hands implementation off to an external coding agent such as Claude Code or Cursor Cloud Agents); [GitHub Copilot Autofix](https://github.blog/news-insights/product-news/found-means-fixed-introducing-code-scanning-autofix-powered-by-github-copilot-and-codeql/) for security-alert triage (covers >90% of CodeQL alert types; remediates >2/3 of found vulnerabilities with little or no editing, vendor-reported; human accepts/edits/dismisses).

- **Example workflow** - A flaky test issue is assigned to Copilot coding agent. It spins up a sandbox, reproduces, and opens a draft PR with the fix and added tests for a human to review.

- **Pros**

  - Clears mechanical, well-scoped work at scale. On `dotnet/runtime`, agent PRs reached real volume, and a large share of the added lines were tests (per the public PR record; exact figures to confirm in the run).

  - Produces an auditable artifact (a PR) rather than a silent change.

- **Cons / failure modes**

  - **Junk-PR / junk-report review burden.** The cost moves to review, it does not disappear. The starkest measured case is the curl project: in 2025 roughly 20% of incoming security reports were AI-generated while only about 5% of all submissions were genuine vulnerabilities, and each report consumed 3-4 maintainers for 30 minutes to 3 hours to triage (Daniel Stenberg, "Death by a thousand slops"). Maintainers of agent-PR streams report the same shape: "fixing the symptom rather than the underlying issue."

  - Confidently wrong root-cause analysis when the agent latches onto a correlation. GitHub's own Autofix guidance warns its AI fixes can fabricate plausible-looking dependencies or be "syntactically valid but semantically wrong."

- **When to adopt** - When you have well-scoped, low-blast-radius issues and reviewer capacity to absorb the new PR stream. Not for ambiguous or architecturally sensitive work.

### Approach 4: Autonomous deflection (customer-facing resolution)

- **What it is** - AI resolves inbound support contacts end to end, with no human in the loop on the happy path.

- **Tools / assets** - [Intercom Fin](https://www.intercom.com/fin) (priced per resolution); [Zendesk Resolution Platform](https://www.zendesk.com/service/ai/).

- **Example workflow** - A customer asks "how do I reset my API key?"; Fin answers from the knowledge base, the customer confirms it is resolved, no ticket is ever created.

- **Pros**

  - Removes whole categories of repetitive contact from the human queue.

  - Pay-per-resolution pricing makes the unit economics legible: Intercom Fin charges $0.99 per resolved outcome (minimum 50/month), with other outcome types priced separately (a handoff or disqualification at $0.99, a qualified prospect at $9.99) (fin.ai/pricing, verified).

  - Vendor-claimed deflection "up to 65%" end-to-end (Intercom, customer testimonial; vendor-claimed, not audited).

- **Cons / failure modes**

  - **Quality collapse when pushed too far.** The canonical case is Klarna: all-in on AI support in Feb 2024, then walked back in May 2025, conceding "lower quality" and that "there will always be a human." Over-automation has a reputational cost that does not show up in the deflection metric.

  - Hallucinated answers presented with full confidence to a customer.

- **When to adopt** - Only on high-volume, low-stakes, well-documented question types, always with a visible human-escalation path. Never as a wholesale replacement.

---

## What goes wrong at this stage

- **Failure modes**

  - Over-grouping / false dedup that hides a distinct problem inside an unrelated cluster (Sentry).

  - Hallucinated triage: invented events, faked solutions, wrong root cause (Rovo reports).

  - Destructive autonomous edits once the AI can act unattended (Rovo "erased a whole page").

  - Review-burden inversion: agent PRs and AI suggestions move cost to reviewers rather than removing it.

  - Over-automation of customer-facing resolution (Klarna).

- **Early warning signs** (leading, catch these before damage)

  - Duplicate-issue count falling while reopened/"actually-different" issues rise - a dedup tell.

  - Triagers accepting nearly 100% of suggestions without edits - rubber-stamping.

  - Reviewer queue growing faster than merged-PR count after enabling agent triage.

  - Customer CSAT or re-contact rate dipping while deflection rate looks great.

- **Mitigations**

  - Keep destructive actions behind suggest-then-confirm; reserve auto-apply for reversible, easy-to-detect actions.

  - Alert on auto-merge of issue groups; sample-audit dedup decisions weekly.

  - Budget reviewer time explicitly before turning on agent triage; cap agent PR volume.

  - Always wire a one-tap human-escalation path on customer-facing deflection.

---

## Who does what

| Task | AI does | Human does | Owning role | Handoff point |
|------|---------|-----------|-------------|---------------|
| Label / categorise | Suggests (or auto-applies in bounds) | Confirms or audits sample | Triager / on-duty IC | On low-confidence or auto-apply audit |
| Deduplicate | Clusters candidates | Confirms merges, watches over-grouping | Triager | Any cross-area merge |
| Prioritise / severity | Proposes severity | Owns final priority call | Tech lead / EM | Anything above "Medium" |
| Route / assign | Suggests team/assignee | Accepts; resolves ambiguous routing | Triager | Cross-team routing |
| First-fix / RCA | Investigates, drafts PR or root cause | Reviews, accepts/rejects | Reviewer / IC | Always at the PR gate |
| Customer resolution | Answers known questions | Handles escalations, edge cases | Support / on-call | On customer dissatisfaction or low confidence |

---

## Expectations to set, and with whom

- **Triagers / ICs** - AI handles the mechanical sort; you still own the judgement calls and you are accountable for what auto-applies. Treat suggestions as a fast first pass, not ground truth.

- **Reviewers** - Agent triage will add a PR stream. This is real review work and it is budgeted, not a rubber stamp. Expect to reject "symptom-fix" PRs.

- **PM / leadership** - Expect faster sorting and routing, not fewer engineers. Deflection and labelling metrics measure activity, not resolved customer value; do not read them as throughput.

- **Customers** (where deflection is on) - Set the expectation that a human is one tap away, always.

---

## Guardrails

| Guardrail | Protects against | Citation |
|-----------|------------------|----------|
| Suggest-then-confirm for any destructive action; human stays primary assignee | Destructive / hallucinated autonomous triage | Rovo reports (HN); [Linear for Agents](https://linear.app/docs) keeps a human assignee |
| Cap aggressiveness of auto-grouping; alert on group merges; sample-audit | Over-grouping that hides a real issue | Sentry engineering blog (issue grouping) |
| Keep a visible human-escalation path; do not go all-in on deflection | Customer-facing quality collapse | Klarna reversal (Feb 2024 to May 2025) |
| Explicitly budget reviewer time before enabling agent triage; cap agent PR volume | Review-burden inversion / junk PRs | Maintainer reports on agent PRs (HN) |

Pipeline-wide guardrails (explainability gate, outcomes-not-velocity metrics, named human accountability) live in [09-cross-cutting](09-cross-cutting.md) and apply here too.

---

## Likely costs

- **Direct / tooling** - Linear Triage Intelligence (Business / Enterprise tiers); Intercom Fin at roughly $0.99 per resolution; Sentry Seer at $40 per contributor per month from Jan 2026. *(All vendor-listed and dated; verify live.)*

- **Hidden / operational** - Junk-PR review burden from agent triage ("20x the slop"); 1-4 minute latency per item on AI triage; un-disableable noise; unpredictable usage-based cost as volume grows.

- **Metrics to watch** - Reviewer queue depth vs merge rate; suggestion-override rate; re-contact / reopened-issue rate; cost-per-resolution trend.

---

## Roles: 2023 to mid-2026

**2023 baseline.** Support and customer-success agents fielded inbound support by hand; PMs triaged feature requests; EMs and tech leads triaged bugs and assigned them; on-call / SRE triaged alerts; QA filed and labelled defects. Dedup, labelling, and routing were manual backlog grooming.

**Mid-2026 split.** AI does the first-pass labelling, dedup, routing, and FAQ deflection. Humans move to exception-handling and judgement: support agents take escalations rather than repetitive questions; the PM still owns prioritisation; the EM or tech lead still owns the severity call. A new responsibility appears: owning the triage-AI configuration and the reviewer-capacity budget it consumes. (Synthesis from the digest and stage evidence.)

## Where Claude alone helps

- **GitHub Actions `@claude` on issues** - mention Claude on an incoming issue to summarise, label, or open a draft fix PR. *Pro:* turns a raw issue into an actionable item with no context-switch. *Con:* single-turn by default, so complex triage still needs human follow-up, and it needs the GitHub App configured.

- **`/deep-research`** - investigate an issue across logs, past issues, and docs. *Pro:* parallel search plus adversarial verification filters false leads and cites sources. *Con:* async and token-heavy, not for high-touch real-time customer contact.

- **MCP servers (GitHub, Slack, Sentry)** - pull live issue and alert data into the session. *Pro:* one source of truth, no copy-paste. *Con:* each server must be configured and alive; tool latency compounds across large batches.

- **CLAUDE.md** - encode severity thresholds and routing rules as project memory. *Pro:* consistent policy, easy onboarding. *Con:* static, so it drifts if unmaintained and will not detect its own violations.

*Honest gap:* Claude has no native alert/escalation integration and no consensus-triage workflow.

## Commentary: is this phase still distinct?

Barely, at the edges. The moment an issue can be assigned to a coding agent that returns a PR, intake bleeds straight into implementation and review ([04](04-implementation.md), [05](05-code-review.md)), skipping the human-planning middle. Triage stays a distinct discipline for the judgement calls (severity, priority, what not to build), but the mechanical sort is dissolving into the stages downstream.

---

## Sources

*(Baseline cross-corpus digest 2026-06-20, hardened with deep-research run `wm7aa2hy4`. Confidence tags: [V] = adversarially verified this run; [P] = primary source, fetched but verify-step rate-limited; [S] = secondary / vendor-claimed.)*

- [V] Intercom Fin pricing: $0.99 per outcome, 50/month minimum; "resolves up to 65% end-to-end" (testimonial). https://fin.ai/pricing/
- [V] GitHub Copilot Autofix: >90% of CodeQL alert types, remediates >2/3 with little/no editing; human accepts/edits/dismisses. https://github.blog/news-insights/product-news/found-means-fixed-introducing-code-scanning-autofix-powered-by-github-copilot-and-codeql/
- [P] GitHub Autofix responsible-use: can fabricate plausible dependencies; can be "semantically wrong" or introduce new vulns. https://docs.github.com/en/code-security/code-scanning/managing-code-scanning-alerts/responsible-use-autofix-code-scanning
- [P] curl, "Death by a thousand slops" (Daniel Stenberg, 14 Jul 2025): ~20% of security reports AI-generated, ~5% genuine; 3-4 maintainers, 30 min to 3 hrs each. https://daniel.haxx.se/blog/2025/07/14/death-by-a-thousand-slops/
- [P] Sentry Seer: scans issues for root cause; hands off to Claude Code / Cursor Cloud Agents. https://docs.sentry.io/product/ai-in-sentry/seer/
- [P] GitHub Copilot coding agent: ephemeral GitHub Actions env; assign an issue to `Copilot`; output is a PR a human reviews. https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent
- [S] Sentry over-grouping failure mode (engineering blog). https://sentry.io/
- [S] Atlassian Rovo + practitioner reports (HN) on hallucinated / destructive behaviour. https://www.atlassian.com/software/rovo
- [S] Linear Triage / Agents docs. https://linear.app/docs
- [S] Klarna AI-support reversal (Feb 2024 to May 2025) - widely reported.
