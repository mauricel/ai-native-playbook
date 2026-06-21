---
title: Ship, CI & Deploy - AI-native approaches
stage: 7 of 8
status: draft (baseline digest + deep-research run w6cshptdy, verified citations folded in)
---

# Ship, CI & Deploy

## What this stage is

Getting verified code through CI and into production safely. AI now fixes red builds, triages and patches security alerts, scores deployment risk, and guards the supply chain at install and CI time. The recurring tension: AI can turn a build green without actually fixing the underlying problem.

---

## The ways of doing things

Along the autonomy spectrum, from "AI suggests a fix you apply" to "AI guards the gate automatically".

### Approach 1: AI security autofix in the PR/CI loop (assist, human-gated)

- **What it is** - A scanner finds an issue; AI proposes a patch with a plain-language explanation; a human accepts, edits, or dismisses.

- **Tools / assets** - [GitHub Copilot Autofix](https://github.blog/news-insights/product-news/found-means-fixed-introducing-code-scanning-autofix-powered-by-github-copilot-and-codeql/) (CodeQL + Copilot; covers >90% of alert types in JS/TS/Java/Python); [GitLab Duo](https://docs.gitlab.com/ee/user/gitlab_duo/) (root-cause analysis on failed jobs; vulnerability-resolution MRs).

- **Example workflow** - CodeQL flags an injection; Autofix posts a suggested patch and explanation in the PR; the developer reviews and merges.

- **Pros**

  - Remediates more than two-thirds of found vulnerabilities with little or no editing; median time to commit a PR-time fix was 28 minutes vs 1.5 hours manually, about 3x faster (all vendor-reported, May-Jul 2024 beta data; GA Apr 2025, >90% of alert types in JS/TS/Java/Python).

  - Keeps a human in the loop by construction: every suggestion requires explicit accept / edit / dismiss and is never auto-merged, with CI required to pass before merge.

- **Cons / failure modes**

  - **Plausible-but-wrong and non-deterministic patches.** GitHub's own responsible-use guidance warns Autofix uses "a generative model that is non-deterministic" (the same alert can yield a different or no suggestion across attempts), and that fixes can be "syntactically valid but semantically wrong," only partially fix the issue, fail to remediate, or introduce new vulnerabilities. It can also "suggest fabricated dependencies published under statistically probable names," so human dependency review is the prescribed guardrail.

  - **Human review stays mandatory** - roughly a third of autofixes need real edits, and that lands on people.

- **When to adopt** - Broadly, as a suggestion layer; never auto-apply security patches without review.

### Approach 2: Agent-fixes-red-CI and deployment-risk scoring (delegated)

- **What it is** - An agent diagnoses a failing pipeline or a risky deploy and proposes (or opens) a fix; risk detectors watch deploys for regressions.

- **Tools / assets** - [Sentry Seer](https://docs.sentry.io/product/ai-in-sentry/seer/) (configurable autonomy: stop at root-cause, at a plan, or at a drafted PR, with gating conditions); [CircleCI](https://circleci.com/) Smarter Testing ("4x faster", runs only relevant tests); [Graphite Merge Queue](https://graphite.dev/) (parallel CI, batching, skip redundant tests); [Datadog Watchdog Faulty Deployment Detection](https://docs.datadoghq.com/watchdog/faulty_deployment_detection/) (note: needs a prior-deployment baseline, so a first or low-traffic deploy is a blind spot).

- **Example workflow** - A deploy ships; Watchdog flags an error-rate spike correlated with it; Seer does RCA and opens a fix PR for review.

- **Pros**

  - Shortens the red-build-to-green loop; runs only the tests that matter.

  - Catches faulty deploys automatically on the metrics it watches.

- **Cons / failure modes**

  - **Coverage gaps create false confidence.** Watchdog faulty-deploy detection is error-rate-driven only - no latency-regression signal and no rollback recommendation, so a latency-only regression slips by.

  - Plausible-but-wrong CI fixes that turn the build green without fixing the cause.

- **When to adopt** - For fast feedback, with humans owning the merge and an understanding of exactly what each detector does and does not watch.

### Approach 3: Supply-chain behavioural defence (autonomous gate)

- **What it is** - Tooling scans dependencies' behaviour at install/CI time and blocks malicious packages, and scores the risk of dependency updates.

- **Tools / assets** - [Socket](https://socket.dev/) (behavioural supply-chain scanning; caught live 2026 campaigns including Shai-Hulud and a TanStack CI credential-stealer); [Renovate](https://docs.renovatebot.com/) / Mend Merge Confidence; [Aikido](https://www.aikido.dev/) (AutoTriage + AutoFix).

- **Example workflow** - A transitive dependency tries to read CI credentials on install; Socket flags the behaviour and blocks the merge.

- **Pros**

  - Catches real, live attacks at install/CI time (Socket did so in 2026).

  - Behavioural analysis sees what a version-number heuristic cannot.

- **Cons / failure modes**

  - **Confidence scores are heuristics, and the supply-chain threat is current.** In May 2026 the "Mini Shai-Hulud" campaign compromised 84 TanStack npm artifacts with CI-credential-stealing malware, including `@tanstack/react-router` at 12M+ weekly downloads, so any consumer on auto-update could pull a malicious release. Auto-merging "confident" dependency updates is exactly the gap such campaigns exploit.

  - **AI scanners are themselves attackable** - Socket documented an npm package using prompt injection plus token flooding to disrupt AI malware scanners.

- **When to adopt** - As a default gate on the supply chain; do not auto-merge dependency updates on a confidence score alone.

---

## What goes wrong at this stage

- **Failure modes** - green-CI patches that do not fix the cause; detectors with blind spots read as full coverage; auto-merged "confident" updates shipping attacks; scanners targeted by injection.

- **Early warning signs**

  - Recurrence of an "autofixed" issue shortly after merge.

  - Incidents from deploys that passed every automated gate.

  - Dependency updates merging with no human eyes on a confidence score.

- **Mitigations**

  - Human review of every autofix and CI patch before merge.

  - Know each detector's blind spots; add latency and rollback signals where missing.

  - No auto-merge of dependency updates; behavioural scanning as a hard gate.

---

## Who does what

| Task | AI does | Human does | Owning role | Handoff point |
|------|---------|-----------|-------------|---------------|
| Security autofix | Proposes patch + explanation | Accepts / edits / dismisses | IC / reviewer | Before merge (always) |
| Red-CI diagnosis | RCA, drafts fix | Reviews and merges | IC | At the fix PR |
| Deploy-risk watch | Flags regressions it can see | Decides rollback | On-call / release | On a flag (and for blind spots) |
| Dependency gate | Scores + blocks bad behaviour | Approves updates | Reviewer / security | Before any dep merge |

---

## Expectations to set, and with whom

- **Devs / ICs** - An autofix that makes CI green is a suggestion, not a verified fix. Read it; about a third need real edits.

- **On-call / release** - Know what each detector watches. Error-rate-only detection will miss a latency regression; do not treat "no alert" as "safe."

- **Security / leadership** - Confidence scores are heuristics and the scanners themselves are attackable; keep humans on dependency merges and treat supply-chain tooling as load-bearing.

---

## Guardrails

| Guardrail | Protects against | Citation |
|-----------|------------------|----------|
| Human review of autofix / CI patches before merge | Plausible-but-wrong green-CI patches | GitHub Autofix responsible-use guidance |
| No auto-merge of "confident" dep updates; behavioural scan as a gate | CI credential-stealers | Shai-Hulud, TanStack (Socket) |
| Add latency-regression + rollback signals | Faulty-deploy blind spots | Datadog Watchdog is error-rate only |

Pipeline-wide guardrails live in [09-cross-cutting](09-cross-cutting.md).

---

## Likely costs

- **Direct / tooling** - GitHub Advanced Security / Copilot Autofix; Socket (raised a $60M Series C at ~$1B, ~May 2026); CircleCI / Graphite seats and compute.

- **Hidden / operational** - ~1/3 of autofixes need human edits; auto-merge supply-chain exposure; effort to map and cover each detector's blind spots.

- **Metrics to watch** - autofix recurrence rate; incidents from deploys that passed all gates; share of dependency merges with human review.

---

## Roles: 2023 to mid-2026

**2023 baseline.** DevOps, platform, and release engineers owned CI/CD pipelines; appsec triaged scan results; on-call or a release manager gated production deploys.

**Mid-2026 split.** AI autofixes security alerts and red builds and gates the supply chain; platform engineers review and own the pipeline; release stays human-gated for anything risky; a new owner appears for the AI-gate configuration and its blind spots. (Synthesis.)

## Where Claude alone helps

- **GitHub Actions `@claude fix`** - spawn a fix PR on a CI failure. *Pro:* quick red-build turnaround. *Con:* a long loop (CI fails, Claude runs, PR opens, CI re-runs), token cost per run, and secrets setup.

- **`/security-review`** - scan pending changes for vuln classes before push. *Pro:* fast local feedback, no infra. *Con:* not a formal audit; no SAST-tool integration.

- **Hooks (pre-push supply-chain checks)** - verify dependency and lockfile consistency before push. *Pro:* gates risky changes early. *Con:* bash, hard to wire to vuln DBs, must finish fast.

- **Community security plugins** - in-session vuln review on write (verify availability before relying on one). *Pro:* shifts security left. *Con:* surface-level; does not know your threat model or compliance needs.

*Honest gap:* no native SBOM/SLSA attestation or secret-scanning; dependency audits are point-in-time, not continuous.

## Commentary: is this phase still distinct?

CI is where review and shipping merge. AI review ([05](05-code-review.md)) increasingly runs as a pipeline stage, and autofix turns a failing build into a new PR that re-enters review, so the loop closes on itself. The phase stays distinct mainly because deploy carries irreversible, real-world blast radius, which is the one place teams keep a human firmly in the gate.

---

## Sources

*(Baseline digest 2026-06-20, hardened with deep-research run `w6cshptdy`. Tags: [V] adversarially verified, [P] primary, [S] secondary / vendor.)*

- [V] GitHub Copilot Autofix announcement (>90% alert types; >2/3 remediated; 28-min vs 1.5-hr metric). https://github.blog/news-insights/product-news/found-means-fixed-introducing-code-scanning-autofix-powered-by-github-copilot-and-codeql/ · https://github.blog/news-insights/product-news/secure-code-more-than-three-times-faster-with-copilot-autofix/
- [V] GitHub Autofix responsible-use (non-determinism; semantically-wrong fixes; fabricated deps; human dependency review). https://docs.github.com/en/code-security/responsible-use/responsible-use-autofix-code-scanning
- [P] Datadog Watchdog Faulty Deployment Detection (error-rate only; baseline blind spot). https://docs.datadoghq.com/watchdog/faulty_deployment_detection/
- [P] Socket blog (Mini Shai-Hulud / TanStack May 2026; scanner prompt-injection). https://socket.dev/blog
- [S] GitLab Duo; CircleCI; Graphite; Renovate / Mend; Aikido; Sentry Seer (linked inline).
