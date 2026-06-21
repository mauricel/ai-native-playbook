---
title: Testing & QA - AI-native approaches
stage: 6 of 8
status: draft (baseline digest + deep-research run w6lym25v5; Meta TestGen-LLM/ACH and the coverage-collapse evidence [V]-verified; browser-QA vendor claims kept from baseline, verify step rate-limited this run)
---

# Testing & QA

## What this stage is

Generating, maintaining, and running tests, and exercising the product as a user would. This is the stage where AI's "looks done" most easily diverges from "is correct", because a passing test is not automatically a meaningful one. The filter that decides what to keep is what separates a production-grade approach from a coverage-vanity trap.

---

## The ways of doing things

Along the autonomy spectrum, from "AI drafts tests you vet" to "AI exercises the app on its own".

### Approach 1: LLM test-gen with an assured filter (delegated, gated)

- **What it is** - The model proposes tests; a deterministic pipeline keeps only those that build, pass reliably, and raise coverage; a human still reviews what survives. Note the verified detail: Meta's TestGen-LLM *improves existing* tests, it does not write them from scratch, and "the filter is what eliminates hallucination, not the LLM itself."

- **Tools / assets** - [Meta TestGen-LLM](https://arxiv.org/abs/2402.09171) (the three assured filters: build, reliable pass, coverage increase); [GitHub Copilot `/tests`](https://github.com/features/copilot).

- **Example workflow** - TestGen-LLM proposes 20 new cases for a class; the filters discard those that do not build or do not add coverage; a reviewer accepts the survivors.

- **Pros**

  - Strong measured yield: on Instagram Reels/Stories, 75% of generated cases built, 57% passed reliably, 25% raised coverage, and 73% of recommendations were accepted into production.

  - The assured filter discards bad output by construction, so reviewers see fewer duds.

- **Cons / failure modes**

  - **Vacuous tests that pass but assert nothing.** Meta's own paper shows a generated test with no assertion (just a `// TODO`) that gained coverage and was rejected by a human. The filter checks compile and coverage, not whether the test asserts anything meaningful.

  - **Review-burden economics** - the funnel implies roughly 1 in 40 generated tests is accepted end to end; humans absorb the rest.

- **When to adopt** - Broadly, but always with human assertion review on top of the assured filter.

### Approach 2: Mutation / fault-guided generation (delegated)

- **What it is** - The system seeds faults, then writes tests specifically able to kill them, targeting test strength rather than raw coverage.

- **Tools / assets** - [Meta ACH](https://arxiv.org/abs/2501.12862) (mutation-guided; seeds privacy faults and hardens against them).

- **Example workflow** - ACH was applied to 10,795 Android Kotlin classes across 7 platforms, generating 9,095 mutants and 571 privacy-hardening tests; engineers accepted 73% and judged 36% privacy-relevant (verified).

- **Pros**

  - Optimises for the thing that matters (catching real faults), not the proxy (coverage). The mutation-guided design "guarantees each accepted test kills at least one specific injected fault," which proves it is fault-revealing rather than merely coverage-improving, and sidesteps the equivalent-mutant problem (verified).

- **Cons / failure modes**

  - Compute-heavy; mutant generation and evaluation are expensive.

  - Targets the fault classes you seed; unseeded classes go unguarded.

- **When to adopt** - For high-stakes domains (privacy, security) where test strength, not coverage, is the goal.

### Approach 3: Deterministic, non-LLM generation (no hallucination)

- **What it is** - Reinforcement learning, not an LLM, writes tests; deterministic and free of fabrication.

- **Tools / assets** - [Diffblue Cover](https://www.diffblue.com/) (Java; "eliminates hallucination").

- **Pros**

  - No hallucinated APIs or packages; reproducible output.

- **Cons / failure modes**

  - **Characterization, not specification** - it records current behaviour, so a bug is "dutifully recorded" and the test passes on the bug while breaking on a legitimate refactor.

  - Language-scoped (Java).

- **When to adopt** - Large legacy Java estates needing a safety net of characterization tests fast.

### Approach 4: Self-healing E2E and autonomous browser QA (higher autonomy)

- **What it is** - Tests whose locators self-repair when the UI shifts, and agents that drive the browser like a user.

- **Tools / assets** - Testim / mabl / Functionize (self-healing locators); [QA Wolf](https://www.qawolf.com/), Momentic, [browser-use](https://github.com/browser-use/browser-use), [Playwright MCP](https://github.com/microsoft/playwright-mcp).

- **Pros**

  - Cuts the maintenance tax of brittle selectors; agents explore flows without scripted steps.

- **Cons / failure modes**

  - **Self-healing masks real regressions** - a locator silently adapts to a genuine regression, so the suite goes green for the wrong reason.

  - **Coverage collapses on real code** - generators strong on toy benchmarks crater on real repos (Codex >80% on HumanEval but <2% on real-world SF110, Siddiq et al.).

- **When to adopt** - Self-healing for stable, high-value flows, with alerting when a locator changes; treat a silent heal as a signal, not a convenience.

---

## What goes wrong at this stage

- **Failure modes** - vacuous no-assertion tests; over-fitting to a buggy implementation; benchmark-to-reality coverage collapse; self-healing hiding regressions.

- **Early warning signs**

  - Coverage climbing while escaped-defect rate does not fall.

  - Tests with no assertions, or that never fail when you intentionally break the code.

  - Self-healing events spiking around a release (a heal may be masking a regression).

- **Mitigations**

  - Human assertion review on top of any assured filter; mutation-test the tests.

  - Prefer specification over characterization tests where the intended behaviour is known.

  - Alert on every self-healing locator change and triage it.

---

## Who does what

| Task | AI does | Human does | Owning role | Handoff point |
|------|---------|-----------|-------------|---------------|
| Generate unit tests | Proposes, self-filters | Reviews assertions for meaning | IC | Before merge |
| Harden against faults | Seeds mutants, writes killers | Confirms fault classes matter | Tech lead / security | On accepted tests |
| Characterization net (legacy) | Generates deterministically | Decides what to pin | IC | Before refactor |
| E2E / browser QA | Drives flows, self-heals | Triages heals + failures | QA | On any self-heal or red run |

---

## Expectations to set, and with whom

- **Devs / ICs** - A passing generated test is not a meaningful test. You own the assertions. Coverage is a proxy, not the goal.

- **QA** - Self-healing reduces maintenance but can hide regressions; treat a heal as something to investigate, not celebrate.

- **PM / leadership** - Test generation yields real coverage but at roughly a 1-in-40 acceptance rate; expect reviewer time, and do not read coverage as correctness.

---

## Guardrails

| Guardrail | Protects against | Citation |
|-----------|------------------|----------|
| Assured filter PLUS human assertion review | Vacuous no-assertion tests | Meta TestGen-LLM (its own rejected example) |
| Human confirms the current passing behaviour is *correct* before landing a generated test (turns a "weak" hardening test into a "strong" one) | Encoding a bug into a test (characterization over specification) | Meta, "Assured LLM-based Software Engineering" (arXiv 2504.16472) |
| Alert on self-healing locator changes | Masked regressions | Self-healing tooling behaviour (digest) |

Pipeline-wide guardrails live in [09-cross-cutting](09-cross-cutting.md).

---

## Likely costs

- **Direct / tooling** - Diffblue / Qodo seats; QA-agent and mutation-testing compute (ACH-style is compute-heavy).

- **Hidden / operational** - ~1-in-40 end-to-end acceptance rate drives review burden; coverage-as-vanity false confidence; triage time for self-healing events.

- **Metrics to watch** - escaped-defect rate vs coverage; assertion density of generated tests; mutation score; self-heal frequency.

---

## Roles: 2023 to mid-2026

**2023 baseline.** QA engineers and SDETs wrote and automated tests; ICs wrote unit tests; manual QA exercised the product; a QA lead owned the test plan.

**Mid-2026 split.** AI generates the bulk of tests; ICs review assertions for meaning; QA shifts from writing to oversight, exploratory testing, and triaging agent output; manual QA shrinks; the SDET role moves toward owning the generation-and-verification harness. (Synthesis.)

## Where Claude alone helps

- **`/verify`** - run the app and observe real behaviour against the spec. *Pro:* catches integration bugs the suite misses. *Con:* needs a project `/run` skill, is not deterministic, and needs extra CI setup.

- **`/run`** - teach Claude the project's build and launch steps as a reusable skill. *Pro:* reusable across sessions. *Con:* must be maintained; complex build matrices are hard to parameterise.

- **Subagents for test generation** - delegate focused test-writing. *Pro:* parallel coverage with small contexts. *Con:* weak run-and-iterate loop; coverage measured manually.

- **Hooks (pre-commit tests)** - block a session if tests fail. *Pro:* prevents committing broken code. *Con:* the suite must be fast or the UX suffers.

*Honest gap:* no native coverage tracking or flaky-test detection; end-to-end generation (async, UI state) is weak.

## Commentary: is this phase still distinct?

Testing is fusing with implementation. When the same agent writes the code and its tests in one pass, the independent-check property that made QA valuable is quietly lost, which is why the assertion-review guardrail matters so much. The phase stays distinct only if a different actor (a human, or at least a separate adversarial agent) owns verification. Same-agent test-gen is co-located, not independent.

---

## Sources

*(Baseline digest 2026-06-20, hardened with deep-research run `w6lym25v5`. The Meta papers and the coverage-collapse study verified cleanly; the browser-QA vendor claims were not re-verified (verify step rate-limited) and keep their `[S]` tag. Tags: [V] adversarially verified, [P] primary, [S] secondary / vendor.)*

- [V] Meta TestGen-LLM, "Automated Unit Test Improvement..." (improves existing tests; 75/57/25 Instagram figures). https://arxiv.org/abs/2402.09171
- [V] Meta ACH, "Mutation-Guided LLM-based Test Generation at Meta" (10,795 classes, 73% acceptance, 36% privacy). https://arxiv.org/abs/2501.12862
- [V] Meta, "Assured LLM-based Software Engineering" (weak vs strong hardening; human-in-the-loop gate; fault-revealing guarantee). https://arxiv.org/abs/2504.16472
- [V] Siddiq et al., "Using LLMs to Generate JUnit Tests" (Codex >80% HumanEval vs <2% SF110). https://arxiv.org/abs/2305.00418
- [S] Diffblue Cover; Testim / mabl / Functionize; QA Wolf; browser-use; Playwright MCP (linked inline; not re-verified this run). Qodo Cover-Agent noted no longer maintained as of Jun 2025.
