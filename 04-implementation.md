---
title: Implementation - AI-native approaches
stage: 4 of 8
status: draft (baseline digest + deep-research run wd3a3bphx, verified citations folded in)
sources: see Sources at bottom
---

# Implementation

## What this stage is

Writing the code. This is the most crowded and fastest-moving stage, and it spans the widest autonomy range of any stage in the pipeline: from a single suggested line you accept with Tab, to an autonomous agent that picks up an issue and opens a PR while you sleep. The choice of where on that spectrum to operate is the single biggest lever a team pulls.

---

## The ways of doing things

Along the autonomy spectrum, from "AI completes your line" to "AI completes your task".

### Approach 1: Inline autocomplete (human types, AI finishes)

- **What it is** - The model predicts the next few lines as you type; you accept, edit, or ignore. You are still the author.

- **Tools / assets** - [GitHub Copilot](https://github.com/features/copilot), Cursor Tab, [Supermaven](https://supermaven.com/), [Windsurf](https://windsurf.com/).

- **Example workflow** - You write a function signature and a comment; Copilot completes the body; you read it, fix the edge case, move on.

- **Pros**

  - Lowest cognitive handoff: you keep the full mental model because you are reading every line as it appears.

  - Genuine speed-up on boilerplate and well-known patterns.

  - Easy to reverse: it is one suggestion, not a sweeping change.

- **Cons / failure modes**

  - Subtle wrong completions that read plausibly and slip past a quick glance.

  - Anchoring: the suggestion biases you toward its approach even when a better one exists.

- **When to adopt** - Universally; this is the safest rung and a sensible default for everyone.

### Approach 2: Chat-in-IDE (you direct, AI drafts in context)

- **What it is** - A conversational assistant inside the editor with repo context; you ask for a change, it proposes edits across one or more files, you apply selectively.

- **Tools / assets** - [Cursor](https://cursor.com/), Windsurf (Cascade), [GitHub Copilot Chat](https://github.com/features/copilot).

- **Example workflow** - "Add pagination to this endpoint and its tests." The assistant drafts the controller change and a test file; you review the diff and apply.

- **Pros**

  - Handles multi-file changes with context the autocomplete tier cannot see.

  - Keeps you in the loop at the diff level: you still approve each change.

- **Cons / failure modes**

  - Diff-review fatigue starts here; bigger changes tempt "apply all" without reading.

  - Context limits mean it can confidently edit against a stale or partial view of the repo.

- **When to adopt** - For day-to-day feature work where you want speed but still want to see every change.

### Approach 3: Agentic CLI (you supervise, AI executes a loop)

- **What it is** - A terminal agent that reads, edits, runs commands, and iterates toward a goal under your supervision, in your working copy.

- **Tools / assets** - [Claude Code](https://github.com/anthropics/claude-code) (subagents, `--worktree` for isolated parallel sessions, headless `-p`); [Aider](https://github.com/Aider-AI/aider) (repo map, auto-commits with messages, architect/editor split); [Amp / Sourcegraph](https://ampcode.com/) (subagents, the "Oracle" second opinion); [OpenAI Codex CLI](https://github.com/openai/codex); [Gemini CLI](https://github.com/google-gemini/gemini-cli).

- **Example workflow** - "Migrate this module off the deprecated client." The agent greps usages, edits each call site, runs the tests, fixes failures, and auto-commits each step so you can roll back any one.

- **Pros**

  - Real task-level leverage: it does the legwork while you steer.

  - Git-native auto-commits make rollback trivial and keep an audit trail.

  - You can interrupt, correct, and redirect mid-task.

- **Cons / failure modes**

  - It is easy to stop reading the loop and lose the mental model.

  - Long sessions hit context limits and quality degrades (context rot).

  - Hallucinated package imports are a live supply-chain risk (slopsquatting).

- **When to adopt** - For well-scoped, mechanical-to-moderate tasks where you will still review the resulting diff as if a junior wrote it.

### Approach 4: Autonomous background / cloud agents (you delegate, AI returns a PR)

- **What it is** - You assign a task; the agent runs in an isolated cloud environment and comes back with a draft PR. No live supervision.

- **Tools / assets** - [GitHub Copilot coding agent](https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent) (ephemeral GitHub Actions env, draft PR; limits: single repo, ~59-min session cap, one PR per task); [OpenAI Codex cloud](https://openai.com/index/introducing-codex/) (a sandboxed container per task, tasks run in parallel); [Google Jules](https://jules.google/) (cloud VM, plan-for-approval, experimental); [Devin](https://devin.ai/) (full terminal + IDE + browser workspace).

- **Example workflow** - You assign three small backlog issues to Codex cloud before lunch; each runs in its own container; you come back to three draft PRs to review.

- **Pros**

  - Parallelises a backlog with a full PR audit trail.

  - Frees your local machine and your attention entirely.

  - DORA 2025 finds throughput positive for teams with strong foundations.

- **Cons / failure modes**

  - **The cost moves to review and concentrates there.** A wave of agent PRs can swamp reviewers ("20x the slop"); see [05-code-review](05-code-review.md).

  - No live correction: a wrong turn early means the whole PR is wrong.

  - Session and scope limits (single repo, time caps) constrain real-world tasks.

- **When to adopt** - For low-blast-radius, well-specified tasks, and only once you have reviewer capacity and a merge gate to absorb the output.

### Approach 5: Multi-agent / parallel (and the "vibe coding" caution)

- **What it is** - Several agents working at once on isolated copies, or one agent spawning subagents. "Vibe coding" is the far end: accepting everything without reading, suitable only for throwaway work.

- **Tools / assets** - [git worktrees](https://git-scm.com/docs/git-worktree) for collision-free parallelism; Amp subagents; Codex parallel containers.

- **Example workflow** - Three worktrees, three agents, three independent features, merged as each lands and passes CI.

- **Pros**

  - True parallelism without edit collisions.

  - Throughput on independent, well-bounded streams of work.

- **Cons / failure modes**

  - **Loss of mental model / over-trust.** Karpathy's coinage: "I 'Accept All' always, I don't read the diffs anymore... the code grows beyond my usual comprehension." Willison (verified quotes) draws the line precisely: vibe coding is "building software with an LLM without reviewing the code it writes", and "if an LLM wrote the code for you, and you then reviewed it, tested it thoroughly and made sure you could explain how it works to someone else that's not vibe coding, it's software development."

  - Even practitioners who could do this choose not to: Mitchell Hashimoto runs one agent, not a swarm.

  - The accountability line, in Willison's words (verified): "I won't commit any code to my repository if I couldn't explain exactly what it does to somebody else."

- **When to adopt** - Parallel worktrees: for genuinely independent work with strong CI gates. Vibe coding: prototypes and throwaways only, never production.

---

## What goes wrong at this stage

- **Failure modes**

  - Net slowdown for experts on familiar code: METR's RCT (verified) found "when developers are allowed to use AI tools, they take 19% longer to complete issues."

  - Delivery stability erosion: DORA 2024 (verified) found AI adoption "significantly increases individual productivity, flow, and job satisfaction. However, it also negatively impacts software delivery stability and throughput." (The modelled stability figure was -7.2%; single-source decimal, label accordingly.)

  - Hallucinated packages / slopsquatting (USENIX Security 2025).

  - Security regression: AI co-authored code carried a higher vulnerability rate (CodeRabbit, Dec 2025; secondary source, confirm).

  - Maintainability decay: rising churn and copy-paste, falling refactoring (GitClear).

  - Cost and latency surprises from usage-based billing and stronger-reasoning models.

- **Early warning signs**

  - "It feels faster" with no movement (or a regression) in cycle time and change-fail rate. The METR perception gap is the canonical tell (verified): devs "expected AI to speed them up by 24%, and even after experiencing the slowdown, they still believed AI had sped them up by 20%."

  - Rising revert/churn within two weeks of merge.

  - PRs growing in size and dropping in review depth.

  - New dependency names appearing that nobody recognises.

- **Mitigations**

  - Mandatory diff review; an explainability gate (do not merge what no human can explain).

  - Dependency allowlists + lockfiles against hallucinated packages.

  - Restrict full-autonomy / vibe coding to low-stakes work.

  - Measure outcomes (stability, rework), not "felt" speed. See [09-cross-cutting](09-cross-cutting.md).

---

## Who does what

| Task | AI does | Human does | Owning role | Handoff point |
|------|---------|-----------|-------------|---------------|
| Write a line / block | Completes | Reads, accepts/edits | IC (author) | Every accept |
| Multi-file change | Drafts the diff | Reviews and applies selectively | IC (author) | At apply |
| Mechanical task (migrate, refactor) | Runs the loop, auto-commits | Steers, reviews final diff | IC | At the diff / PR |
| Backlog task (delegated) | Returns a draft PR | Reviews as if from a junior | Reviewer | Always at the PR gate |
| Architecture / risky change | Assists at most | Owns the design and the code | Tech lead | Stays human-owned |
| Dependency choice | Suggests | Verifies the package exists and is trusted | IC / reviewer | Before merge |

---

## Expectations to set, and with whom

- **Devs / ICs** - AI is a fast, unreliable first draft. You remain the author and you must be able to explain every line you ship. On your own mature code, expect that AI may not actually be faster (METR); use it where it genuinely helps, not reflexively.

- **Reviewers** - Delegated and agentic coding will increase PR volume and PR size. You are the gate. Budget for it; reject slop and symptom-fixes.

- **PM / product** - Expect more first drafts, not proportionally more shipped, reviewed, stable software. The constraint moved to review.

- **Leadership** - Do not bake "AI makes us N% faster" into the plan. The strongest RCT evidence shows a slowdown for experienced devs on familiar code, and a stability cost. AI amplifies the team you already have.

---

## Guardrails

| Guardrail | Protects against | Citation |
|-----------|------------------|----------|
| Mandatory diff review; explainability gate (counter "Accept All") | Loss of mental model / over-trust | Karpathy (vibe coding); Willison |
| Restrict vibe coding / full autonomy to low-stakes, throwaway work | Unmaintainable production slop | Willison's "low-stakes only" rule |
| Dependency allowlist + lockfiles | Slopsquatting / hallucinated packages | Spracklen et al., USENIX Security 2025 (arxiv 2406.10279) |
| Default security scan on AI-written code | Insecure-by-default generation | Stanford CCS 2023 (arxiv 2211.03622); Veracode 2025 |
| Measure stability + rework, not "felt" speed | The velocity illusion | METR 2025 (arxiv 2507.09089); DORA 2024/2025 |

Pipeline-wide guardrails live in [09-cross-cutting](09-cross-cutting.md).

---

## Likely costs

- **Direct / tooling** - Copilot / Cursor per-seat; usage-based agent billing (can surprise); stronger-reasoning models cost more and add latency.

- **Hidden / operational** - ~19% expert slowdown on familiar code (METR); +41% bugs and no throughput gain in one ~800-dev study (Uplevel); -7.2% delivery stability (DORA); rework from churn (GitClear); the review burden pushed downstream onto [05-code-review](05-code-review.md).

- **Metrics to watch** - cycle time vs "felt" speed; change-fail rate; revert/churn within two weeks; PR size and review depth.

---

## Roles: 2023 to mid-2026

**2023 baseline.** ICs wrote the code, often pairing; autocomplete (Copilot) was emerging; senior engineers took the hard parts and reviewed juniors closely.

**Mid-2026 split.** ICs increasingly orchestrate agents and review diffs rather than typing; authoring shifts toward steering and verifying; senior engineers concentrate on design and verification; the junior rung is under pressure (Yegge's "death of the junior developer"). The constraint moves from authoring to review. (Synthesis, grounded in METR / DORA and the digest.)

## Where Claude alone helps

- **LSP code intelligence** - diagnostics, jump-to-definition, and type info in the edit loop. *Pro:* catches type errors the same turn it introduces them. *Con:* heavy on large monorepos, with false positives in complex workspaces.

- **Subagents (parallel backend / frontend)** - split independent work across agents. *Pro:* lean context, no tool-call avalanche. *Con:* no shared intermediate state, so they re-read shared files and have no sync point.

- **Hooks (PostToolUse lint / format / test)** - auto-run linters and formatters after each edit. *Pro:* stays clean without being asked. *Con:* per-hook latency, and a false positive blocks the edit loop.

- **Headless mode (`-p`)** - script repetitive edits across many files. *Pro:* automatable and pipeable. *Con:* non-interactive only.

*Honest gap:* large multi-file refactors (50+ files) can lose coherence; there is no native test-driven loop.

## Commentary: is this phase still distinct?

This phase is absorbing its neighbours rather than dissolving. An agent now writes the code, the tests ([06](06-testing-qa.md)), and the PR description, then triggers review ([05](05-code-review.md)) and CI ([07](07-ship-ci.md)) in one pass. Implementation is becoming the gravitational centre of the pipeline, which is exactly why review capacity, not coding speed, is now the bottleneck.

---

## Sources

*(Baseline cross-corpus digest 2026-06-20, hardened with deep-research run `wd3a3bphx`. Confidence tags: [V] = adversarially verified this run; [P] = primary source, fetched but verify-step rate-limited; [S] = secondary / vendor-claimed.)*

- [V] METR 2025 RCT: "they take 19% longer"; the 24%-forecast / 20%-still-believed perception gap. https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/ · https://arxiv.org/abs/2507.09089
- [V] DORA 2024: AI "negatively impacts software delivery stability and throughput." https://dora.dev/research/2024/dora-report/ · 2025: https://dora.dev/research/2025/dora-report/
- [V] Willison vibe-coding definition + "won't commit code I couldn't explain." https://simonwillison.net/2025/Mar/19/vibe-coding/
- [V] Claude Code: agentic CLI; multi-agent / parallel sessions with a coordinating lead. https://docs.anthropic.com/en/docs/claude-code/overview
- [V] GitHub Copilot coding agent: ephemeral GitHub Actions env; assign an issue to it. https://docs.github.com/en/copilot/using-github-copilot/coding-agent
- [P] Slopsquatting (USENIX Security 2025): 5.2% (commercial) / 21.7% (open) hallucination rate; 205,474 unique fake names across 576k samples. https://arxiv.org/abs/2406.10279
- [S] GitClear code-quality 2025 (churn / copy-paste): https://www.gitclear.com/ai_assistant_code_quality_2025_research
- [P] Stanford security (CCS 2023): https://arxiv.org/abs/2211.03622
- Tool docs: Aider, Amp, Codex, Gemini CLI, Jules, Devin (linked inline above).
