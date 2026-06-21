---
title: The adoption playbook
stage: appendix
status: complete (executable spine + real-world grounding from two research agents; links verified June 2026)
---

# The adoption playbook

The rest of this guide describes the *approaches*. This chapter is the *executable path*: how to actually roll AI-native workflows into a real team, in what order, with what guardrails, measuring what, and aligning whom. It mostly stitches together material already in the other chapters, then grounds it in what other teams have really done (the "What others have done" section).

It is written for the person who has to make this happen: an EM, tech lead, or platform owner.

---

## The one rule that governs everything

**Add the gate before you add the autonomy, and start where reversal is cheap.** Every step below is an application of that rule. The most dangerous configuration in this whole guide is a stage running at high autonomy with low guardrails (see [09](09-cross-cutting.md)).

---

## Step 0 - Locate your team before you change anything

Use the self-assessment in [10](10-ai-native-maturity.md). Score each stage separately; teams are rarely one level overall. Two questions cut through self-flattery:

1. **Can you see the cost?** If you cannot quote your change-fail rate, rework/churn, and reviewer load, you are at most Level 1 no matter how many tools you run.
2. **What happens when the agent is wrong?** If the answer is "we'd probably merge it," your guardrails are behind your autonomy.

Do not start rolling out until you can answer both.

---

## Step 1 - Pick one stage, not all of them

Choose a single stage where **reversal is cheap and current pain is high**. Prove it there before expanding. The per-stage "When to adopt" notes (28 of them across chapters 01-08) tell you which approach within a stage is the safe entry point.

Sensible first bets (cheap to reverse, fast feedback):

- **AI code review as advisory** ([05](05-code-review.md)) - always-on, human keeps the approval gate.
- **Autocomplete plus IDE chat** ([04](04-implementation.md)) - lowest cognitive handoff, you read every line.
- **Suggest-then-confirm intake triage** ([01](01-intake-triage.md)) - one click to reverse a wrong suggestion.

Avoid starting at the autonomous end (background agents, auto-remediation, customer-facing deflection). Those are Step 5 material, not Step 1.

---

## Step 2 - Stand up the guardrail before the autonomy

Before you turn a stage on, put its guardrail in place. Each stage chapter has a Guardrails table (guardrail to what-it-protects-against to citation). The cross-cutting ones from [09](09-cross-cutting.md) apply everywhere:

- Explainability gate: never merge code no human can explain.
- Named human accountability per change.
- Default security scanning on AI output; dependency allowlists against slopsquatting.
- A human approval gate on every merge.

If you cannot staff or enforce the guardrail, you are not ready to turn on the autonomy.

---

## Step 3 - Instrument the outcome before you expand

You cannot manage what you cannot see, and the thing you most need to see is whether quality held while speed rose. Track outcomes, not activity:

- **Delivery stability** (change-fail rate, time-to-restore) and **rework / churn** - the costs AI quietly shifts ([09](09-cross-cutting.md)).
- **Reviewer load** - the constraint moves here ([05](05-code-review.md)).
- **Escaped defects** - did real bugs get through ([06](06-testing-qa.md)).

**Use a balanced framework, not a single metric.** SPACE (Forsgren et al., 2021) established the principle: LOC alone is "incomplete and potentially misleading." **DX Core 4** (Noda and Tacho, Dec 2024) is the current industry-blessed unification of DORA, SPACE, and DevEx into four deliberately oppositional dimensions - Speed, Effectiveness, Quality, Impact - so you cannot game one without another moving. Its authors are explicit that the per-engineer speed metric is "never used at the individual level or tied to performance evaluations." (DX is a vendor, but Core 4 was built with the DORA/SPACE authors.)

**The vanity metrics to refuse:** LOC (discredited), suggestion **acceptance rate** ("widely abandoned" - it misses whether the code is maintainable or gets reverted), and self-reported time saved (unreliable without system data; the METR perception gap is the proof - devs felt ~20% faster while being 19% slower). Gergely Orosz and Laura Tacho found 60% of engineering leaders cite "lack of clear metrics" as their top AI challenge, and warn: **"don't mistake AI activity for AI impact."**

**What to actually watch - the individual-vs-org gap.** This is the single most cross-confirmed finding, from non-aligned sources. DORA 2024: a 25% increase in AI adoption was associated with roughly -1.5% throughput and -7.2% delivery stability even as individual flow rose. Faros AI (10,000+ devs, 1,255 teams): high-adoption teams shipped +21% tasks and +98% PRs, but PR review time rose +91%, PR size +154%, bugs/dev +9%, with "no significant correlation between AI adoption and improvements at the company level." So measure **org outcomes** (stability, review time, rework), not individual activity.

**Stack the capabilities that make AI pay off (DORA AI Capabilities Model, 2025).** DORA's research (Storer and DeBellis, Sep 2025) found AI value is gated by seven organisational capabilities, not by the tool. They are: (1) a **clear, communicated AI stance** (which tools are permitted, what is expected); (2) **healthy data ecosystems**; (3) **AI-accessible internal data** (connect AI to your docs and codebase for company context); (4) **strong version control** (frequent commits, easy rollback - more important as AI raises code volume); (5) **working in small batches**; (6) **user-centric focus** (without it AI moves fast in the wrong direction); (7) **quality internal platforms**. DORA's thesis: AI is an **amplifier** - it magnifies a high-performing org's strengths and a struggling org's dysfunctions. Adopt these *before* you scale autonomy, or you amplify the dysfunction.

---

## Step 4 - Set expectations, by audience

Adoption fails on misaligned expectations as often as on tooling. Use the cross-stage **expectations charter** in [09](09-cross-cutting.md) and the per-stage "Expectations to set, and with whom" sections. The one-line version per audience:

- **Devs:** AI is a fast, unreliable first draft; you own correctness and must be able to explain what you ship.
- **Reviewers:** you are the constraint now, by design; review is real, budgeted work, not a rubber stamp.
- **PM / product:** expect faster first drafts, not faster ship; the bottleneck moved to review.
- **Leadership:** "AI makes us N% faster" is not a safe planning assumption; AI amplifies the team you already have.

---

## Step 5 - Sequence the expansion

Once one stage is proven, instrumented, and trusted, expand. Use the rollout order in [09](09-cross-cutting.md) and the per-stage level-up table in [10](10-ai-native-maturity.md). The discipline:

- **Do not level up every stage at once.** Move the one or two with the highest cost and cheapest reversal, prove the outcome, then move on.
- **Level the guardrails before the autonomy** at every step.
- **Measure the same things at every level** (stability, rework, escaped defects, reviewer load). If they hold or improve as you move right, you are genuinely getting more AI-native. If they degrade, you are just moving risk around.
- **Protect the apprenticeship.** The higher you climb, the stronger the pull to design juniors out; resist it deliberately.

---

## The credible rollout frameworks

No tier-1 source publishes a literal 30/60/90-day plan; the good ones sequence by **adoption maturity, not calendar**. Three converge on the same shape.

- **GitHub's official "roll out at scale"** orders it: license assignment, then **governance (step 2, mid-rollout, not last)**, then developer enablement, then planning and measurement, then success evaluation. Its only concrete day-count guidance: about 45 days before launch, define success metrics and train champions; 14 days, announce; 7 days, run a workshop; launch day, publish resources; post-launch, shift formal training to on-demand. Pilot first with interested teams, then recruit champions from the pilot.

- **DX's baseline-first loop** (Noda and Tacho): capture adoption and developer-experience baselines *before* AI (you cannot reconstruct them later), then identify use cases, do a gap analysis (the two most common blockers are "unclear usage policies and lack of training"), enable, re-track, expand. Run multi-vendor trials of **8 to 12 weeks** with **assigned, not opt-in, cohorts** ("opt-in only gives you early adopters"). Re-evaluate every 8 to 14 months.

- **Faros's pilot spec** (the most concrete): cap the pilot at **25 to 30 people**, composed **20% champions, 60% representative, 20% deliberate skeptics**; target leading indicators of 60% daily and 80% monthly active use within six months, and lagging indicators of a 20 to 50% lead-time reduction with no defect-rate regression. Launch (weeks 1 to 6), Learn (weeks 7 to 18), Run (week 19 on).

**The adoption multipliers** (Brian Houck, Microsoft research): leadership advocacy is associated with roughly **7x** more daily users, formal training about **20%** more, and local champions about **22%** more. The same research concludes: "mandates don't work; demonstrate value."

**The number-one failure mode** (DX Q4 2025 report, ~135,000 devs across 435 companies): **enabling without training.** Handing out licenses is not adoption. Booking.com went from under 10% to roughly 70% adoption through deliberate rollout and training, not distribution.

## What the experts converge on

- **Gergely Orosz** - "AI is an amplifier, not a fixer"; the teams that benefited "already had guardrails: testing and automation." A 90% adoption rate only matters if it benefits the org; beware token-consumption leaderboards.
- **Addy Osmani** - the "70% problem": AI gets you 70% fast, the last 30% is a "two steps back" debugging loop, and it helps seniors more than juniors (the knowledge paradox). Treat AI code like a junior's PR; run a periodic "AI-free day" to slow skill atrophy.
- **Birgitta Böckeler** - "move review left": build "sensors" (lint limits, layering rules, mutation testing) so the agent self-corrects before a human reads the PR ("harness engineering"); balance enthusiasts and skeptics.
- **Kent Beck** - TDD is a "superpower" with agents: unit tests catch the regressions the agent introduces; stop it when it loops or deletes tests.
- **Simon Willison** - "if you haven't seen it run, it's not a working system"; never commit code you could not explain to someone else.
- **Skill-atrophy evidence (peer-reviewed)** - Microsoft Research and CMU (CHI 2025, n=319) found higher confidence in GenAI associated with *less* critical thinking (the "irony of automation").

## Top-down or bottom-up?

Both routes can reach high adoption. The difference is **framing and guardrails**, and that difference is what determines whether it sticks or detonates.

**The mandate route (top-down).** Shopify's Tobi Lütke memo (Mar 2025) made "reflexive AI usage a baseline expectation," tying it to real levers: teams "must demonstrate why they cannot get what they want done using AI" before new headcount, and AI-usage questions were added to performance reviews. Coinbase's Brian Armstrong told engineers to onboard by Friday or explain themselves at a Saturday meeting, and fired a small number who did not; he later called it "heavy-handed." Microsoft's Julia Liuson emailed managers that "using AI is no longer optional." The cautionary version: Duolingo's "AI-first" memo triggered enough public backlash (users deleted the app) that the CEO walked it back and **dropped AI from performance reviews entirely by Apr 2026**. The lesson across these: a mandate moves the adoption number fast but generates backlash and skips the guardrail and quality conversation. The **"we are replacing you" framing** is what detonated at Duolingo and Klarna, even where headcount actually grew.

**The friction-removal plus champions route (bottom-up, with governance).** Shopify *also* did the other half: "default to yes" from legal, no token quotas, a **central LLM proxy** (one gateway, per-team usage monitoring, an alert when spend exceeds $250/day), MCP servers respecting existing access controls, mandatory human PR review, and **reversion rate as the quality gate** - and hit 80% adoption organically before any memo. Monzo ran "community over mandates": one or two tools trialed at a time, small focused cohorts ("large rollouts dilute feedback"), an "AI Engineering Champions" program, and hard no-go zones (no AI on customer data or the warehouse), with access gated to trained users first. ZoomInfo gated trial access on **completing security training and signing a data-governance acknowledgment before a license was issued.**

**The takeaway:** you can reach high adoption either way, but the friction-removal plus champions plus central-governance route gets there with quality gates intact. Pair any push with "humans in the loop, judged on outcomes" framing. The route that skips the guardrails buys a fast number and pays for it in trust and incidents.

---

## What others have done (case studies)

Real, cited rollouts. Vendor-authored case studies are flagged; treat their numbers as marketing.

**Adevinta - the pilot template (Mar 2026, independent).** A 4-week pilot: 77 engineers, 14 teams, 165 real tasks logged with estimated-vs-actual cycle time, run as a head-to-head bake-off (Claude Code vs Cursor vs Copilot), then a decision to scale Claude Code to 2,000+ engineers. They label results "directional and internally valid," not an RCT. *Learn:* same tasks, multiple tools, pre/post metrics, honest caveats - the gold-standard pilot design.

**ZoomInfo - rigorous, security-gated rollout (arXiv, Jan 2025, independent).** Four phases: 5-engineer assessment, then a 126-engineer trial (32% of the org), then a 2-week trial, then company-wide, gated through a ServiceNow workflow. The guardrail: trial access required completing security training and a written data-governance acknowledgment *before* a license was issued. Results: 33% suggestion / 20% line acceptance, 72% satisfaction. *Learn:* a real gating mechanism - training before license - and numbers that survive scrutiny.

**Intuit - feed the AI your house context (Jun 2025, independent).** Treats AI codegen as a platform capability, injecting internal context via curated "golden repositories" so output matches house standards; deliberately vendor-agnostic. *Learn:* on a large or regulated codebase the leverage is your own context, not which tool you pick.

**Dropbox - agentic, with a measurement reframe (May 2026, independent).** Its internal "Nova" agent reached ~1 in 12 PRs; mandatory human review before production. They moved off raw throughput to a Fuel to Adoption to Output to Impact model tracking review turnaround, first-run test pass rate, and defect/rework rate. *Learn:* speeding up codegen just moves the constraint to review and CI, so measure the whole pipeline.

**Monzo and Shopify** - see the bottom-up route above (champions and no-go zones; the central LLM proxy and reversion-rate gate). Both reached high adoption with quality gates intact.

**Cautionary tales (cite these to leadership):**

- **Klarna** claimed its AI assistant did the work of ~700 agents (Feb 2024), then reversed in May 2025 citing "lower quality" and re-hired humans: "there will always be a human if you want." Headline AI cost-cutting eroded quality and brand.
- **Replit** - during a "vibe coding" test an autonomous agent deleted a live production database during a code freeze; the vendor responded with dev/prod separation and a planning-only mode. *Autonomous agents need hard prod isolation and approval gates by default, not opt-in.*
- **curl** dropped its bug bounty after ~20% of 2025 submissions were AI slop (vs ~5% genuine), each burning 3-4 maintainers. AI lowers the cost of plausible-looking work faster than humans can triage it.

## The one finding that should shape your rollout

The most cross-confirmed result, from non-aligned sources (DORA 2024, Faros 2025, the METR RCT), is the **individual-vs-organization gap**: AI reliably makes individual developers feel and measure faster, but org-level delivery does not improve and can slightly degrade, with the bottleneck shifting to code review (Monzo, Dropbox, and Faros all independently say this) and risk shifting to larger batches and lower maintainability (GitClear, DORA).

So the rollout that works is not the one that maximises the adoption number. It is the one that **instruments org outcomes, protects review capacity, and gates before autonomy** - and reaches adoption through friction-removal, champions, and central governance rather than a mandate. Coinbase and Shopify both got to high adoption; only one of them did it without backlash and with the guardrails on.

---

## Common adoption pitfalls

The failure modes that recur across the whole guide, collected so you can watch for them from day one:

- **Review-burden inversion** - agent output swamps reviewers; the cost moved, it did not vanish ([05](05-code-review.md)).
- **The velocity illusion** - "feels faster" while stability falls and rework rises ([09](09-cross-cutting.md)).
- **Security blast radius** - AI tooling is a new attack surface (the CodeRabbit RCE; slopsquatting) ([05](05-code-review.md), [07](07-ship-ci.md)).
- **Skill atrophy** - the apprenticeship pipeline quietly closes ([04](04-implementation.md)).
- **Mandate backfire** - top-down "use AI or else" without guardrails or training breeds resentment and shadow workarounds.
- **Metric gaming** - coverage, deflection, and PR counts all look great while the thing they proxy for does not move.

---

## Sources

> Combined source list from the two research agents (real rollouts + adoption frameworks) being added, with `[V]/[P]/[S]` confidence tags. The cross-links above point to the already-sourced material in chapters 01-11.
