---
title: Spec & Design - AI-native approaches
stage: 2 of 8
status: draft (baseline digest + deep-research run wq4rl69wr, verified citations folded in)
---

# Spec & Design

## What this stage is

Turning an intent into a reviewable specification or design before code is written. The newer twist is "spec-driven development": the written spec becomes the durable source of truth that an agent implements from, rather than a document that rots the moment coding starts.

---

## The ways of doing things

Along the autonomy spectrum, from "AI drafts, you author" to "the spec is the program".

### Approach 1: AI-drafted spec, human-authored intent (assist)

- **What it is** - You supply the intent in bullets; AI expands it into a PRD or design doc; you edit and own it.

- **Tools / assets** - General assistants (Claude, Copilot Chat) plus a house template; [AGENTS.md](https://agents.md/) as a lightweight context-spec that travels with the repo.

- **Example workflow** - You paste three bullets of intent; the model returns a structured PRD with goals, non-goals, and open questions; you cut the fluff and answer the questions.

- **Pros**

  - Removes blank-page friction; you start from a structured draft.

  - Surfaces edge cases and open questions you had not framed yet.

- **Cons / failure modes**

  - Plausible-sounding but generic specs that miss the domain-specific constraint that actually matters.

  - Anchoring on the AI's framing of the problem.

- **When to adopt** - Broadly; it is low-risk as long as a human owns the final document.

### Approach 2: Gated spec-driven development (delegated within gates)

- **What it is** - A staged pipeline where the spec, then the design, then the task list are each generated and human-approved before the next stage, and an agent implements from the result.

- **Tools / assets** - [AWS Kiro](https://kiro.dev/docs/specs/feature-specs/) (`requirements.md` in EARS notation, then `design.md`, then a tasks list, with **human approval gates between phases**; note a "Quick Plan" mode runs all three automatically *without* gates - the opt-out that removes the safety); [GitHub Spec Kit](https://github.com/github/spec-kit) (MIT-licensed `specify` CLI; phases `/speckit.constitution`, `/specify`, `/clarify`, `/plan`, `/tasks`, `/implement`, `/converge`; works across 30+ agents; premise: "specifications become executable, directly generating working implementations"); [EARS](https://alistairmavin.com/ears/) notation (Mavin, 2009; each requirement is `WHEN [condition] THE SYSTEM SHALL [behaviour]`).

- **Example workflow** - In Kiro you describe a feature; it writes EARS requirements; you approve; it writes a design; you approve; it generates a task list; an agent implements task by task.

- **Pros**

  - Makes intent explicit, versioned, and traceable from requirement to code.

  - One spec can generate code, tests, and docs, and target multiple languages.

  - Even a noted skeptic concedes the value: Böckeler calls spec-first "definitely valuable in many situations."

- **Cons / failure modes**

  - **Spec bloat.** A trivial feature ballooned to "8 files and 1,300 lines" (Zaninotto, Marmelab). Böckeler's hands-on evaluation (verified) saw Kiro turn a small bug into "4 user stories with a total of 16 acceptance criteria," and Spec-Kit generate many repetitive markdown files, leading her to prefer reviewing code over the artifacts.

  - **Review burden equals or exceeds code review.** "I'd rather review code than all these markdown files"; review time doubling.

  - **The AI ignores or overshoots its own spec** - in Spec-Kit's research step the agent "took [descriptions of existing classes] as a new specification and generated them all over again, creating duplicates" (verified); elsewhere, marking a "verify" step done without writing a test (Böckeler's "false sense of control").

  - **Degrades at scale.** SDD works for greenfield but is mostly unusable on large existing codebases (Böckeler).

- **When to adopt** - For greenfield or well-bounded features where the spec genuinely pays for itself; pair with a spec-size cap and a real review budget.

### Approach 3: Spec-as-source-of-truth / intent-driven (higher autonomy)

- **What it is** - The spec, not the code, is the artifact humans maintain; code is regenerated from it. Sean Grove (OpenAI) frames "code as a lossy projection of the specification" and argues (verified) that hand-written code is only "10 to 20% of the value... the other 80 to 90% is in structured communication," so the spec is the versioned source of truth and discarding it to keep generated code is like shredding source and version-controlling the binary. He predicts the scarce role becomes the "specification engineer."

- **Tools / assets** - [Tessl](https://tessl.io/) (spec registry; pivoting toward skills / agent-enablement by 2026); Grove, "The New Code."

- **Example workflow** - You change the spec; the system regenerates the implementation and tests to match.

- **Pros**

  - Intent becomes the single durable, reviewable source.

  - Regeneration can keep code, tests, and docs in lockstep.

- **Cons / failure modes**

  - **Waterfall reborn** - inflexibility plus non-determinism.

  - **Chicken-and-egg** - any spec detailed enough to guide an agent is "already nearly equivalent to code."

  - **Semantic diffusion** - "spec" quietly degrading into a synonym for "a detailed prompt."

- **When to adopt** - Experimental for most teams in 2026; watch it, pilot small, do not bet a production line on it yet (inference).

---

## What goes wrong at this stage

- **Failure modes** - spec bloat; review burden at or above code-review cost; the AI not honouring its own spec; waterfall rigidity; the spec collapsing into "just a prompt."

- **Early warning signs**

  - Spec files growing faster than the feature; reviewers skimming markdown they used to read.

  - "Verify" or "tests" tasks marked done with no corresponding test in the diff.

  - Specs being written *after* the code, to satisfy process.

- **Mitigations**

  - Cap spec size; set an explicit spec-review budget and treat it as real work.

  - Require that test tasks produce actual tests before they can be closed.

  - Keep specs lightweight and living; do not let them become a gate that recreates waterfall.

---

## Who does what

| Task | AI does | Human does | Owning role | Handoff point |
|------|---------|-----------|-------------|---------------|
| Frame intent / goals | Drafts from bullets | Owns the problem framing | PM + tech lead | Before design starts |
| Write requirements (EARS) | Generates | Reviews, approves | Tech lead / IC | Gate before design |
| Write design | Generates | Reviews architecture | Tech lead | Gate before tasks |
| Decompose into tasks | Generates | Sanity-checks scope | IC | Gate before implement |
| Implement from spec | Codes | Reviews diff + spec adherence | Reviewer | At the PR |

---

## Expectations to set, and with whom

- **Devs / ICs** - AI drafts the spec; you still own correctness and you must confirm the spec matches reality. A "done" checkbox is not evidence; the test in the diff is.

- **Reviewers** - You now review specs as well as code, and that review is real work. Expect spec-review time to rival code-review time; budget for it.

- **PM / product** - A spec-driven flow front-loads thinking; expect slower starts and (ideally) fewer mid-build surprises, not instant features.

- **Leadership** - Spec-driven development is promising but immature at the high-autonomy end; treat it as a pilot, not a mandate.

---

## Guardrails

| Guardrail | Protects against | Citation |
|-----------|------------------|----------|
| Spec-size cap + explicit spec-review budget | 1,300-line spec bloat; review-time doubling | Zaninotto / Marmelab |
| Require real tests, not self-marked "done" | False sense of control | Böckeler (Thoughtworks / martinfowler.com) |
| Keep specs lightweight and living | Waterfall rigidity; semantic diffusion | Practitioner reports (digest) |

Pipeline-wide guardrails (explainability, named accountability, outcomes-over-velocity) live in [09-cross-cutting](09-cross-cutting.md).

---

## Likely costs

- **Direct / tooling** - Kiro and GitHub Spec Kit are largely free or open source; cost is mostly the underlying model usage.

- **Hidden / operational** - spec-review burden ("review time doubles"); the risk of recreating waterfall and its inflexibility; time spent maintaining specs that drift from code.

- **Metrics to watch** - spec-review time vs code-review time; rate of spec-vs-implementation drift; features where the spec was written after the code.

---

## Roles: 2023 to mid-2026

**2023 baseline.** PMs wrote PRDs; architects and tech leads wrote design docs; ICs wrote technical specs; senior engineers ran design reviews in meetings and shared docs.

**Mid-2026 split.** AI drafts the PRD, design doc, and spec; the PM still owns the intent and the problem framing; the tech lead reviews AI-drafted design for soundness; in spec-driven shops, ICs shift toward authoring and reviewing specs rather than translating prose to code by hand. The new load is spec review, which now rivals code review in cost. (Synthesis.)

## Where Claude alone helps

- **Plan Mode** - draft and iterate a spec or design before any code, surfacing tradeoffs. *Pro:* forces explicit reasoning and catches scope creep early. *Con:* spends tokens with no artifact, and nothing enforces that later code follows the plan.

- **Subagents with a domain lens** - delegate spec validation to a focused subagent (security reviewer, API-design reviewer). *Pro:* parallel, domain-specific review without bloating the main thread. *Con:* session-local, with no persistent versioning or audit trail of who vetted what.

- **MCP servers (Notion, Confluence, Figma)** - pull live design docs and comps into context. *Pro:* no stale copies. *Con:* read-only and rate-limited; Claude reads design tools, it does not update them.

- **CLAUDE.md or a REVIEW.md standard** - encode mandatory spec sections and acceptance criteria. *Pro:* consistent spec quality. *Con:* static text that cannot run dynamic checks.

*Honest gap:* no native spec versioning, change-tracking, or sign-off workflow.

## Commentary: is this phase still distinct?

It is splitting two ways. At the high-autonomy end (spec-as-source-of-truth) spec, planning, and implementation collapse into one regenerate-from-intent loop, so the boundary with [03](03-planning-decomposition.md) and [04](04-implementation.md) disappears. At the pragmatic end, spec stays distinct precisely because someone must own intent and correctness, which AI cannot. The phase survives as a thinking step, not a document-production step.

---

## Sources

*(Baseline digest 2026-06-20, hardened with deep-research run `wq4rl69wr`. Tags: [V] adversarially verified, [P] primary, [S] secondary / vendor.)*

- [V] Böckeler (Thoughtworks), "Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl" (Oct 2025) - the source for the 4-user-stories/16-ACs bloat and the duplicate-regeneration failure. https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html · also "How far can we push AI autonomy in code generation?" (Aug 2025).
- [V] GitHub Spec Kit - MIT, the Specify CLI, the constitution→specify→clarify→plan→tasks→implement→converge phases, "specs become executable." https://github.com/github/spec-kit
- [V] AWS Kiro - requirements/design/tasks with approval gates; EARS `WHEN...THE SYSTEM SHALL`; the gate-skipping "Quick Plan" mode. https://kiro.dev/docs/specs/feature-specs/
- [V] Sean Grove (OpenAI), "The New Code" - code is 10-20% of value; the "specification engineer." https://www.youtube.com/watch?v=8rABwKRsec4
- [V] AGENTS.md - "a README for agents." https://agents.md/
- [P] Zaninotto / Marmelab on spec bloat (1,300 lines). (Marmelab blog)
- [S] EARS (Mavin, 2009). https://alistairmavin.com/ears/
