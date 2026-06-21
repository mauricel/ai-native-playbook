---
title: Code Review - AI-native approaches
stage: 5 of 8
status: draft (baseline digest + deep-research run w461au5yz, verified citations folded in)
---

# Code Review

## What this stage is

The quality gate between "code written" and "code merged." AI now participates in a large and growing share of reviews, and increasingly reviews code that other AI wrote, which raises the "who reviews the reviewer" question in a sharp form. Adoption is real but trust lags: by November 2025 about **1 in 7 public PRs (14.9%)** involved an AI agent reviewing or commenting, up roughly **14x from 1.1% in Feb 2024** (Pullflow). Three vendors dominate (~72% of AI-participated PRs): CodeRabbit (632k PRs), GitHub Copilot (561k, now #1 by PRs/month), and Google Gemini (175k). Yet in the 2025 Stack Overflow survey **58.7% of developers said they would not use AI for code review/committing**, and trust in AI accuracy fell from 40% to 29% year over year.

---

## The ways of doing things

Along the autonomy spectrum, from "AI comments, human decides" to "AI gates the merge".

### Approach 1: Bot-as-reviewer on every PR (advisory)

- **What it is** - An AI reviewer comments on every PR with line-by-line findings, a walkthrough, and sometimes a one-click fix. It advises; a human still approves.

- **Tools / assets** - [CodeRabbit](https://www.coderabbit.ai/) (walkthroughs, architecture diagrams, line-by-line, "Fix with AI", 40+ linters); [GitHub Copilot code review](https://docs.github.com/en/copilot/using-github-copilot/code-review).

- **Example workflow** - A PR opens; CodeRabbit posts a summary and a dozen inline comments; the author addresses the real ones; a human reviewer approves.

- **Pros**

  - Always-on, fast (~30s), never fatigued; catches the easy stuff before a human looks.

  - Surfaces genuine high-value defects (a 30-day study found real bugs including zip-slip and cross-user data access).

- **Cons / failure modes**

  - **Quantified noise.** The 30-day CodeRabbit study (28 PRs, 290 findings, ~10 per PR) breaks down as 15% useless, 13% wrong assumptions, 21% nitpicking, 13% thoughtful, 35% quality improvements (3% of those security/critical); 72% were relevant but only about half added real value (lycheeorg). A peer-reviewed MSR 2026 study found **60.2% of closed agent-only PRs sat in the 0-30% signal-to-noise band**, and 12 of 13 agents averaged below 60% signal, naming low signal-to-noise a primary driver of PR abandonment.

  - **GitHub Copilot cannot enforce a gate** - it only ever leaves a "Comment" review, so it cannot Approve or Request-changes to block a merge, and it does not auto-re-review on new pushes, so it goes stale silently.

- **When to adopt** - Broadly, as an advisory layer, with rule-tuning to manage noise and a human approval still required.

### Approach 2: Codebase-aware review "beyond the diff" (delegated reasoning)

- **What it is** - The reviewer indexes the whole repo (graph or agent swarm) to catch multi-file and architectural bugs a diff-local linter cannot.

- **Tools / assets** - [Greptile](https://www.greptile.com/) (graph index plus a parallel agent swarm); [Graphite Diamond](https://graphite.dev/); [Qodo Merge](https://www.qodo.ai/) (validates a PR against the linked ticket and flags partial implementations).

- **Example workflow** - A change to a shared utility opens; Greptile traces every caller and flags the one downstream contract it breaks.

- **Pros**

  - Finds cross-file bugs that structurally evade diff-local tools.

  - Ticket-compliance checks catch "implemented half the story" before a human does.

- **Cons / failure modes**

  - More context means more confident-but-wrong findings when the index is stale.

  - Higher cost and latency than a diff-local pass.

- **When to adopt** - On larger codebases where multi-file coupling is the main source of escaped bugs.

### Approach 3: AI-authored-and-AI-reviewed (high autonomy, highest risk)

- **What it is** - AI writes the code and an AI reviewer signs off, with the human reduced to a rubber stamp. This is less a recommended approach than a trap to name.

- **Tools / assets** - any combination of an agentic coder (see [04-implementation](04-implementation.md)) plus an AI reviewer with auto-approve.

- **Pros**

  - Maximal throughput on paper.

- **Cons / failure modes**

  - **"Who reviews the reviewer?"** Two AIs agreeing is not verification; shared blind spots pass straight through.

  - **Review fatigue / rubber-stamping** - ~10 comments per PR across many PRs is unsustainable, so humans stop reading.

  - **The reviewer is an attack surface.** The Kudelski Security CodeRabbit RCE: a malicious `.rubocop.yml` with a `require: ./ext.rb` directive ran attacker Ruby on CodeRabbit's *unsandboxed production* infrastructure during review, leaking its GitHub App private key and granting write access to roughly 1M repositories. Disclosed 24 Jan 2025, published by Kudelski 19 Aug 2025, remediated by disabling Rubocop and sandboxing external tools. https://research.kudelskisecurity.com/2025/08/19/how-we-exploited-coderabbit-from-a-simple-pr-to-rce-and-write-access-on-1m-repositories/

- **When to adopt** - Do not. Keep a human approval gate; this rung exists to be avoided.

---

## What goes wrong at this stage

- **Failure modes** - noise drowning signal; rubber-stamping; comment-only bots that cannot block and go stale; the review bot itself as a supply-chain attack surface.

- **Early warning signs**

  - Comment-to-merge ratio rising while review depth (time-on-PR) falls.

  - Authors closing AI comments en masse without changes.

  - AI "Comment" reviews sitting stale on PRs that changed after the review.

- **Mitigations**

  - Tune rules and track the relevance rate; mute noisy categories.

  - Keep a human as the approving reviewer; AI is advisory.

  - Sandbox and pin permissions for review bots; never let untrusted repo config drive code execution.

---

## Who does what

| Task | AI does | Human does | Owning role | Handoff point |
|------|---------|-----------|-------------|---------------|
| First-pass review | Comments line-by-line | Triages signal vs noise | Author | Before requesting human review |
| Cross-file / architectural check | Traces callers, flags coupling | Judges the call | Reviewer / tech lead | On flagged coupling |
| Ticket-compliance | Flags partial implementation | Confirms scope | Reviewer | Before approval |
| Approve / block merge | Advises only | Owns the decision | Human reviewer | The merge gate (always human) |

---

## Expectations to set, and with whom

- **Authors** - AI review is a fast first pass to clean up before a human looks, not a green light. Address real findings; do not mass-dismiss.

- **Reviewers** - You are the gate, and that is by design. AI cannot (and should not) own approval. Reject symptom-fixes; do not rubber-stamp AI-authored PRs because an AI already commented.

- **Leadership** - Adoption is high and rising, but a measured quarter of AI comments are noise and the bot is a real attack surface; budget for tuning and treat review tooling as security-sensitive.

---

## Guardrails

| Guardrail | Protects against | Citation |
|-----------|------------------|----------|
| Keep a human approval gate; AI review is advisory | Rubber-stamping; "who reviews the reviewer" | Copilot is comment-only by design |
| Tune rules; track relevance rate | 28% noise; ~49% non-improving comments | lycheeorg 30-day study |
| Sandbox / pin permissions for review bots | Config-driven RCE | Kudelski CodeRabbit RCE (Jan 2025) |

Pipeline-wide guardrails live in [09-cross-cutting](09-cross-cutting.md).

---

## Likely costs

- **Direct / tooling** - CodeRabbit seats (raised a $60M Series B, Sep 2025); Graphite / Greptile / Qodo seats.

- **Hidden / operational** - ~10 comments per PR review fatigue; rule-tuning effort to cut noise; the security blast radius of a compromised review bot.

- **Metrics to watch** - comment relevance rate; comment-to-merge ratio; time-on-PR (review depth); escaped-defect rate despite AI review.

---

## Roles: 2023 to mid-2026

**2023 baseline.** Peer ICs reviewed PRs; senior engineers and tech leads gate-kept merges; an appsec team did security review separately.

**Mid-2026 split.** AI bots do the first-pass review on most PRs; humans own approval and the merge gate; review volume is up sharply because agents author more PRs. The reviewer becomes the load-bearing, bottleneck role, and "who reviews the reviewer" is a live question. (Synthesis.)

## Where Claude alone helps

- **`/code-review` (local, multi-agent)** - review the diff for bugs and cleanups before pushing; `--fix` applies, `--comment` posts to the PR; effort runs low through ultra. *Pro:* fast, line-cited, catches classes humans miss. *Con:* ultra runs in the cloud and costs real money; false positives on unfamiliar code.

- **GitHub `@claude` cloud review + REVIEW.md** - auto-review every push, tuned by a repo REVIEW.md. *Pro:* no manual workflow, severity-ranked, deduped. *Con:* needs org/admin setup and a paid tier; limited conditional rules.

- **`/simplify`** - cleanup-only pass (reuse, simplification, efficiency) that auto-applies. *Pro:* narrow scope, fewer false positives. *Con:* does not hunt bugs.

- **`/security-review`** - flags injection, auth, and data-exposure classes in the diff. *Pro:* shifts security left. *Con:* not a formal audit; misses subtle vulns.

*Honest gap:* findings are advisory, not blocking; no native team-approval or "auto-apply safe, ask on risky" human-in-the-loop mode.

## Commentary: is this phase still distinct?

Review is simultaneously the most distinct phase (someone must own the gate) and the most pressured. As AI authors and AI reviews, the human approval step is the last load-bearing checkpoint, and its distinctness is what protects the whole pipeline from the compounding-error failure mode in [09](09-cross-cutting.md). Collapse review into automation and you lose the gate that makes everything upstream safe.

---

## Sources

*(Baseline digest 2026-06-20, hardened with deep-research run `w461au5yz`. Tags: [V] adversarially verified, [P] primary, [S] secondary / vendor.)*

- [V] Pullflow, "State of AI Code Review 2025" - the 14.9% / 1.1% trajectory and vendor breakdown. https://www.pullflow.com/state-of-ai-code-review-2025
- [V] lycheeorg, 30-day CodeRabbit study (full noise breakdown). https://lycheeorg.dev/2025-09-13-code-rabbit/
- [V] Kudelski Security, CodeRabbit RCE write-up (dates, remediation). https://research.kudelskisecurity.com/2025/08/19/how-we-exploited-coderabbit-from-a-simple-pr-to-rce-and-write-access-on-1m-repositories/
- [V] Stack Overflow Developer Survey 2025 - 58.7% won't use AI for review/committing; 40%→29% trust drop; 66% "almost right but not quite." https://survey.stackoverflow.co/2025/ai/
- [V] "From Industry Claims to Empirical Reality: Code Review Agents in PRs" (MSR 2026) - signal-to-noise and PR-abandonment figures. https://arxiv.org/pdf/2604.03196
- [S] CodeRabbit, Greptile, Graphite, Qodo, GitHub Copilot code review docs (linked inline).
