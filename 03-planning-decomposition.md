---
title: Planning & Decomposition - AI-native approaches
stage: 3 of 8
status: draft (baseline digest + deep-research run wvvplxlfz; plan-mode + spec-pipeline [V]-verified; context-rot / Ralph claims kept from baseline, verify step was rate-limited this run)
---

# Planning & Decomposition

## What this stage is

Turning an approved intent into an ordered, reviewable plan and discrete tasks before edits begin. Agentic tools now formalise this as a "plan, then act" gate: the agent researches and proposes, a human approves, and only then does code change.

---

## The ways of doing things

Along the autonomy spectrum, from "plan and wait for me" to "plan and run unattended".

### Approach 1: In-agent plan mode (read-only, approve before edits)

- **What it is** - The agent reads the codebase, proposes a step-by-step plan, and makes no edits until you approve it.

- **Tools / assets** - [Claude Code Plan Mode](https://code.claude.com/docs/en/common-workflows) (enter via `claude --permission-mode plan` or Shift+Tab mid-session; Read/Grep/Glob work but Edit/Write/Bash are blocked until you approve); [Cursor Plan Mode](https://cursor.com/docs/agent/plan-mode) (researches the codebase, asks clarifying questions, generates a plan you review and edit through chat or markdown, then "click to build").

- **Example workflow** - You ask for a refactor; the agent returns a 6-step plan naming the files it will touch; you delete step 4, reorder two, and approve; it executes.

- **Pros**

  - Catches a wrong approach while it is still cheap, before any code is written.

  - Produces a reviewable artifact you can edit, not just a yes/no.

  - Keeps you in control of scope and blast radius.

- **Cons / failure modes**

  - A confident plan can still be subtly wrong; approving on a skim defeats the point.

  - Plans inherit context rot on large codebases (see below).

- **When to adopt** - Default for any non-trivial agentic change; the cheapest insurance in the pipeline.

### Approach 2: Spec-driven task pipeline (delegated decomposition)

- **What it is** - A spec is mechanically decomposed into dependency-aware tasks an agent can execute, some in parallel.

- **Tools / assets** - [GitHub Spec Kit](https://github.github.com/spec-kit/) - a four-phase pipeline (Spec then Plan then Tasks then Implement) where each phase produces a Markdown artifact that feeds the next (`/speckit.specify`, `/speckit.plan`, `/speckit.tasks`, `/speckit.implement`), giving the agent structured context instead of ad-hoc prompts. Its stance: "specifications don't serve code, code serves specifications."

- **Example workflow** - From an approved spec, Spec Kit emits a task graph; independent tasks are marked parallel; agents pick them up.

- **Pros**

  - Decomposition is explicit and reusable across many agents.

  - Surfaces real task dependencies and parallelism.

- **Cons / failure modes**

  - Only as good as the spec; a vague spec yields vague tasks.

  - Parallel markers can be wrong, creating hidden coupling at merge time.

- **When to adopt** - When you already run spec-driven development (see [02-spec-design](02-spec-design.md)) and want the task graph to fall out of it.

### Approach 3: Autonomous self-planning and loops (high autonomy)

- **What it is** - The agent plans and executes with little or no human gate, sometimes in a continuous loop, one task per iteration.

- **Tools / assets** - [Devin](https://devin.ai/) (works to explicit completion criteria); the ["Ralph" loop](https://ghuntley.com/ralph/) (Geoffrey Huntley): `while :; do cat PROMPT.md | claude-code; done`, one task per pass.

- **Example workflow** - A greenfield prototype: Ralph loops overnight, implementing one task per iteration toward an MVP.

- **Pros**

  - Cheap throughput on greenfield, low-stakes work (Huntley reports a roughly $297 compute spend for a $50k-scope MVP).

  - No human in the inner loop, so it scales while you sleep.

- **Cons / failure modes**

  - **Loop pathologies** - the agent wrongly assumes features are "not implemented", emits placeholder or stub code that compiles but does not work, and quality degrades near the context limit (~147-152k tokens despite a nominal 200k); "you will wake up to a broken codebase." Huntley himself will not run it on legacy code.

  - **Plans inherit context rot** - performance degrades non-uniformly as input grows, and multi-step reasoning degrades more in long contexts ([Chroma "Context Rot"](https://research.trychroma.com/context-rot)).

  - **System-vs-individual gap** - planning-adjacent quality gains did not convert to delivery; throughput and stability still fell (DORA 2024).

- **When to adopt** - Greenfield throwaways and prototypes only; never unattended on legacy or production code.

---

## What goes wrong at this stage

- **Failure modes** - rubber-stamped plans; context rot degrading long-horizon plans; loop pathologies producing stubs and broken state; quality-gains-that-do-not-ship.

- **Early warning signs**

  - Plans approved in seconds without edits.

  - Tasks that "compile and pass" but implement nothing real (stub tells: TODOs, empty bodies).

  - Agent confidently re-implementing something that already exists.

- **Mitigations**

  - Treat plan approval as real review; edit the plan, do not just accept it.

  - Keep contexts small; chunk large work; never run unattended loops on legacy code.

  - Diff every loop iteration; gate merges behind CI and human review.

---

## Who does what

| Task | AI does | Human does | Owning role | Handoff point |
|------|---------|-----------|-------------|---------------|
| Research the codebase | Reads, summarises | Spot-checks assumptions | IC | Before plan is trusted |
| Propose the plan | Drafts steps | Edits, approves | IC / tech lead | The approval gate (always) |
| Decompose into tasks | Generates graph | Validates dependencies/parallelism | Tech lead | Before parallel execution |
| Execute the plan | Codes per step | Reviews each iteration's diff | Reviewer | At every merge |

---

## Expectations to set, and with whom

- **Devs / ICs** - The plan gate is yours to use, not skip. Read and edit the plan; that is where a wrong approach gets caught cheaply.

- **Reviewers** - Autonomous loops produce volume and can produce stubs; expect to verify that "passing" actually means "working."

- **PM / product** - Planning front-loads time; expect a slower start in exchange for fewer wrong turns, not instant delivery.

- **Leadership** - Unattended autonomous loops are a greenfield-prototype tool, not a production strategy; the evidence is that individual gains here do not automatically become shipped, stable software (DORA).

---

## Guardrails

| Guardrail | Protects against | Citation |
|-----------|------------------|----------|
| Plan-mode review gate before any edit | Wrong-approach execution | Claude / Cursor plan mode |
| Context-window hygiene; chunk work | Context rot in long-horizon plans | Chroma "Context Rot" |
| No unattended loops on legacy/production | Broken-codebase loop pathologies | Huntley (ghuntley.com/ralph) |

Pipeline-wide guardrails live in [09-cross-cutting](09-cross-cutting.md).

---

## Likely costs

- **Direct / tooling** - plan modes are free with the agent; loop compute is cheap (Huntley's ~$297 MVP).

- **Hidden / operational** - cleanup cost when an unattended loop corrupts a codebase; time lost to context-rot-degraded plans; the gap between local "done" and shipped-and-stable.

- **Metrics to watch** - plan-edit rate (are humans actually changing plans?); stub/placeholder rate in agent output; rework after autonomous runs.

---

## Roles: 2023 to mid-2026

**2023 baseline.** EMs, tech leads, and ICs broke work down in backlog grooming and sprint planning; the team estimated collectively; the tech lead sequenced dependencies.

**Mid-2026 split.** Agent plan-mode proposes the decomposition and sequencing; ICs review and edit the plan; the tech lead still owns the approach and the architecture call; estimation stays human because AI estimates are unreliable. The new gate is plan review. (Synthesis.)

## Where Claude alone helps

- **Plan Mode** - propose a reviewable, editable plan before edits. *Pro:* catches wrong approaches cheaply and yields an artifact you can edit. *Con:* no persistent plan storage, so rationale is lost if the conversation drifts.

- **Parallel subagents in git worktrees** - run decomposed, independent units concurrently in isolated working copies, each opening its own PR. *Pro:* real parallelism without edit collisions. *Con:* shared types and interdependencies break the isolation; no auto-merge or critical-path analysis.

- **Hooks (pre-execution validation)** - validate a decomposition before it runs (coverage present, no circular deps). *Pro:* catches broken plans early. *Con:* bash/MCP only, no task-graph DSL, cryptic failures.

- **CLAUDE.md** - record preferred decomposition patterns. *Pro:* team muscle memory. *Con:* prose, not enforced.

*Honest gap:* no dependency or critical-path tracking, and no dry-run before parallel execution.

## Commentary: is this phase still distinct?

Planning is the phase most at risk of vanishing. In agentic flows the plan is generated, approved, and executed in one motion, fusing with spec ([02](02-spec-design.md)) upstream and implementation ([04](04-implementation.md)) downstream. It stays distinct only as long as a human insists on the approval gate. Remove the gate and the phase is gone.

---

## Sources

*(Baseline digest 2026-06-20, partially hardened with deep-research run `wvvplxlfz`. The plan-mode and spec-pipeline claims verified cleanly; the context-rot and Ralph-loop claims could not be re-verified this run because the verify step was rate-limited, so they keep their baseline `[P]` tag rather than upgrading to `[V]`. Tags: [V] adversarially verified, [P] primary, [S] secondary.)*

- [V] Claude Code Plan Mode (`--permission-mode plan` / Shift+Tab; Edit/Write/Bash blocked until approval). https://code.claude.com/docs/en/common-workflows
- [V] Cursor Plan Mode (research, clarifying questions, editable plan, click to build). https://cursor.com/docs/agent/plan-mode
- [V] GitHub Spec Kit (Spec to Plan to Tasks to Implement, Markdown artifact per phase). https://github.github.com/spec-kit/
- [P] Chroma, "Context Rot" (not re-verified this run). https://research.trychroma.com/context-rot
- [P] Geoffrey Huntley, the "Ralph" loop (not re-verified this run). https://ghuntley.com/ralph/
- [P] DORA 2024 (system-vs-individual gap). https://dora.dev/research/2024/dora-report/
