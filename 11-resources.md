---
title: Further reading and resources
stage: appendix
status: draft (links verified June 2026 by a dedicated research agent; honesty flags inline)
---

# Further reading and resources

The sources behind this guide plus the wider canon worth reading to stay current. Links were verified live in June 2026. Items that could not be fully verified are flagged `⚠️ unverified`. Treat any vendor metric as "verify before you lean on it in public."

The field moves fast enough that this list has a short shelf life. The single best habit is to follow two or three of the newsletters in section 4.

---

## 1. Essays and blog posts

### The canon (cited at each other constantly)

- **Simon Willison, "Vibe coding"** (Mar 2025) - the precise definition and the "won't commit code I can't explain" rule. https://simonwillison.net/2025/Mar/19/vibe-coding/
- **Thomas Ptacek, "My AI Skeptic Friends Are All Nuts"** (fly.io, Jun 2025) - the strongest bull case. https://fly.io/blog/youre-all-nuts/
- **Andrej Karpathy** - the original vibe-coding coinage.
- **Addy Osmani, "The 70% problem"** - why the last 30% is the hard part.
- **Steve Yegge, "The Death of the Junior Developer"** - the apprenticeship-pipeline risk.
- **Sean Grove (OpenAI), "The New Code"** - "code is a lossy projection of the specification."
- **Peter Naur, "Programming as Theory Building"** (1985) - the foundational "code is a lossy artifact of a shared mental theory" essay everyone now re-cites. https://gwern.net/doc/cs/algorithm/1985-naur.pdf

### Agentic coding (hands-on)

- **Armin Ronacher, "Agentic Coding Recommendations"** - the most-cited hands-on playbook. https://lucumr.pocoo.org/2025/6/12/agentic-coding/
- **Mitchell Hashimoto, "Vibing a Non-Trivial Ghostty Feature"** - the canonical 80%-AI / 20%-human-judgement case study. https://mitchellh.com/writing/non-trivial-vibing
- **Anthropic, "Claude Code: Best practices for agentic coding"** - the research → plan → execute → review loop and the CLAUDE.md convention. https://www.anthropic.com/engineering/claude-code-best-practices

### Spec-driven and AI-native process

- **GitHub (Delimarsky and Bryndin), "Spec-driven development with AI"** - the Spec Kit launch; Spec → Plan → Tasks → Implement. https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/
- **Patrick Debois, "The 4 patterns of AI Native Dev"** - a taxonomy for AI-native process and org from the "DevOps godfather." https://ainativedev.io/news/the-4-patterns-of-ai-native-dev-overview
- **Birgitta Böckeler, "Harness Engineering - first thoughts"** - Guides and Sensors for constraining and verifying agents. https://martinfowler.com/articles/exploring-gen-ai/harness-engineering-memo.html

### Building agents and context engineering

- **Anthropic, "Building Effective Agents"** - the foundational workflows-vs-agents vocabulary. https://www.anthropic.com/engineering/building-effective-agents
- **Anthropic, "How we built our multi-agent research system"** - orchestrator-worker patterns. https://www.anthropic.com/engineering/multi-agent-research-system
- **Anthropic, "Effective context engineering for AI agents"** - mainstreamed "context engineering." https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- **Phil Schmid, "The New Skill in AI is Not Prompting, It's Context Engineering"** - the crisp definition. https://www.philschmid.de/context-engineering
- **HumanLayer (Dex Horthy), "12-Factor Agents"** - "good agents are mostly software." https://github.com/humanlayer/12-factor-agents

### The cost side: review burden, burnout, quality

- **Evil Martians, "AI-assisted engineers are burning out, is this fine?"** - the standout "AI shifts cost to review" burnout essay. https://evilmartians.com/chronicles/ai-assisted-engineers-are-burning-out-is-this-fine
- **Cleric, "The Hidden Complexity of Building an AI SRE"** (Sep 2025) - vendor-confessed RCA failure modes. https://cleric.ai/blog
- **Resolve, "AI Tripled Our Code Velocity. Here's What It Did to On-Call"** - the downstream incident-load cost. https://resolve.ai/blog
- **lycheeorg, 30-day CodeRabbit study** (Sep 2025) - the quantified noise of AI review. https://lycheeorg.dev/2025-09-13-code-rabbit/
- **Kudelski Security, "How we exploited CodeRabbit"** (Jan 2025) - AI-reviewer-as-attack-surface. https://kudelskisecurity.com/
- **Daniel Stenberg (curl), "Death by a thousand slops"** (Jul 2025) - AI junk reports overwhelming maintainers. https://daniel.haxx.se/blog/2025/07/14/death-by-a-thousand-slops/

### Roles and evals

- **swyx, "The Rise of the AI Engineer"** - defined the role; seminal for team design. https://www.latent.space/p/ai-engineer
- **Fowler, Joshi and Venkatraman, "Expert Generalists"** - the generalist-with-depth value alongside LLMs. https://martinfowler.com/articles/expert-generalist.html
- **Hamel Husain, "Your AI Product Needs Evals"** and **"LLM-as-a-Judge"** - the practical eval canon. https://hamel.dev/blog/posts/evals/ · https://hamel.dev/blog/posts/llm-judge/

`⚠️ unverified` (real-but-unconfirmed slugs, chase before quoting): Kent Beck "Augmented Coding: Beyond the Vibes" (kentbeck newsletter); martinfowler.com "The VibeSec Reckoning" and "Structured-Prompt-Driven Development"; an Anthropic "long-running agents" harness piece.

---

## 2. Books

The shortlist worth owning, then the wider shelf. (Verified against publisher pages June 2026.)

**Strongest shortlist for this subject:** Huyen, Osmani (*Beyond Vibe Coding*), Alammar/Grootendorst, Berryman/Ziegler.

### AI-native software engineering

- **Addy Osmani, *Beyond Vibe Coding: From Coder to AI-Era Developer*** (O'Reilly, Aug 2025) - the strongest AI-native pick. https://www.oreilly.com/library/view/beyond-vibe-coding/9798341634749/ · companion https://beyond.addy.ie/
- **Tom Taulli, *AI-Assisted Programming*** (O'Reilly, 2024). https://www.oreilly.com/library/view/ai-assisted-programming/9781098164553/
- **Addy Osmani, *The Effective Software Engineer*** (O'Reilly, 2026) - IC-focused AI-leverage angle. https://www.oreilly.com/library/view/the-effective-software/9798341638167/

### Building with LLMs and agents

- **Chip Huyen, *AI Engineering: Building Applications with Foundation Models*** (O'Reilly, 2025) - the highest-signal, most-cited title here. https://www.oreilly.com/library/view/ai-engineering/9781098166298/
- **Jay Alammar and Maarten Grootendorst, *Hands-On Large Language Models*** (O'Reilly, 2024). https://www.oreilly.com/library/view/hands-on-large-language/9781098150952/
- **John Berryman and Albert Ziegler, *Prompt Engineering for LLMs*** (O'Reilly, 2024) - authors were Copilot architects. https://www.oreilly.com/library/view/prompt-engineering-for/9781098156145/
- **Suhas Pai, *Designing Large Language Model Applications*** (O'Reilly, Mar 2025). https://www.oreilly.com/library/view/designing-large-language/9781098150495/
- **Michael Albada, *Building Applications with AI Agents*** (O'Reilly, Oct 2025). https://www.oreilly.com/library/view/building-applications-with/9781098176495/
- **Sebastian Raschka, *Build a Large Language Model (From Scratch)*** (Manning, 2024) - builds the model, not apps. https://www.manning.com/books/build-a-large-language-model-from-scratch
- **Paul Iusztin and Maxime Labonne, *LLM Engineer's Handbook*** (Packt, Oct 2024). https://www.packtpub.com/en-us/product/llm-engineers-handbook-9781836200062
- **Valentina Alto, *Building LLM Powered Applications*** (Packt, May 2024). https://www.packtpub.com/en-us/product/building-llm-powered-applications-9781835462317
- **Mayo Oshin and Nuno Campos, *Learning LangChain*** (O'Reilly, Feb 2025). https://www.amazon.com/dp/1098167287

### Leadership (AI-adjacent, not AI-specific)

- **Addy Osmani, *Leading Effective Engineering Teams*** (O'Reilly, Jun 2024). https://www.oreilly.com/library/view/leading-effective-engineering/9781098148232/

*Honesty notes:* there is no dedicated published "AI for SRE/DevOps" book yet, and no Pragmatic-Bookshelf AI-assisted-engineering title found. Gergely Orosz's *Software Engineer's Guidebook* is excellent but general-career, not AI-native.

---

## 3. Reports and studies

The recurring research to cite, with the caveats that keep you honest.

- **DORA, *2025 State of AI-Assisted Software Development*** (Sep 2025; note the title changed from "State of DevOps"). Introduces the DORA AI Capabilities Model; ~90% use AI, ~30% distrust AI code, AI hurts delivery stability. https://dora.dev/dora-report-2025/ · model: https://cloud.google.com/resources/content/2025-dora-ai-capabilities-model-report
- **METR, "Early-2025 AI on Experienced OS Developer Productivity"** - 19% slower in an RCT (n=16). https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/ · arXiv 2507.09089. ⚠️ Small n; METR posted a **Feb-2026 methodology/uplift update** (https://metr.org/blog/2026-02-24-uplift-update/) - cite as the early-2025 result and frame cautiously.
- **Stack Overflow Developer Survey 2025** - 84% use/plan AI, 46% distrust accuracy (up from 31%), top frustration "almost right but not quite" (66%). https://survey.stackoverflow.co/2025/ai/
- **GitClear, *AI Copilot Code Quality 2025*** (Bill Harding) - 211M-line analysis; clones up ~4-8x. ⚠️ Commercial vendor, correlational. https://www.gitclear.com/ai_assistant_code_quality_2025_research
- **GitHub Octoverse 2025** - 80% of new devs use Copilot in week 1; Copilot agent authored 1M+ PRs in 5 months. https://octoverse.github.com/
- **Peng et al., "The Impact of AI on Developer Productivity: Evidence from GitHub Copilot"** (2023) - 55.8% faster on a lab HTTP-server task. ⚠️ Lab task; pair with METR, never cite alone. https://arxiv.org/abs/2302.06590
- **Spracklen et al., "We Have a Package for You!" (slopsquatting)** - USENIX Security 2025; hallucination 5.2% to 21.7%. 2026 follow-on (arXiv 2605.17062) shows reduced-but-persistent rates. https://arxiv.org/abs/2406.10279
- **Perry et al., "Do Users Write More Insecure Code with AI Assistants?"** (Stanford, CCS 2023). https://arxiv.org/abs/2211.03622
- **Thoughtworks Technology Radar Vol. 34** (Apr 2026) - theme: AI "cognitive debt," return to fundamentals. https://www.thoughtworks.com/radar
- **Atlassian, *State of Developer Experience 2025*** - 99% report time savings but a "productivity paradox"; 63% say leaders don't understand their pain. https://www.atlassian.com/teams/software-development/state-of-developer-experience-2025
- **JetBrains, *State of Developer Ecosystem 2025*** - 85% regularly use AI, only 44% integrated. https://devecosystem-2025.jetbrains.com/
- **Anthropic Economic Index** - software dev is the #1 Claude use case. ⚠️ Usage telemetry, not a productivity RCT. https://www.anthropic.com/economic-index
- **Chroma, "Context Rot"** and **Siddiq et al.** (test-gen coverage collapse) - cited in chapters 03 and 06. https://research.trychroma.com/context-rot · https://arxiv.org/abs/2305.00418

---

## 4. Newsletters and ongoing sources

The best places to stay current after this doc goes stale (and it will).

- **The Pragmatic Engineer** (Gergely Orosz). https://newsletter.pragmaticengineer.com
- **Simon Willison's Weblog** - near-daily, sceptical-but-fair. https://simonwillison.net
- **Birgitta Böckeler** (Thoughtworks AI-assisted delivery lead) - the most credible practitioner source on AI-native SDLC specifically. https://birgitta.info · gen-AI tag on https://martinfowler.com
- **Thoughtworks Technology Radar**. https://www.thoughtworks.com/radar
- **Latent Space** (swyx and Alessio) - newsletter + top technical AI podcast. https://www.latent.space
- **Addy Osmani, "Elevate"** - developer effectiveness, agentic coding, harness engineering. https://addyo.substack.com
- **AI Native Dev** (Tessl / Guy Podjarny) - podcast + newsletter + community squarely on this subject. https://ainativedev.io
- **Applied LLMs** field guide (Yan, Bischof, Frye, Husain, Liu, Shankar). ⚠️ One canonical essay, not a running newsletter. https://applied-llms.org
- **Anthropic Engineering** - best primary source for agent-design patterns. https://www.anthropic.com/engineering
- **GitHub Engineering blog** - GitHub's own use of coding agents at scale. https://github.blog/engineering/
- **Ethan Mollick, "One Useful Thing"** - AI and the future of work (less code-focused). https://www.oneusefulthing.org
- **DORA** and **METR** - the two most rigorous research sources. https://dora.dev · https://metr.org

---

## 5. Skills and toolkits

Concrete things to install or read code from.

- **GitHub Spec Kit** - spec-driven dev toolkit; slash commands across 30+ agents. https://github.com/github/spec-kit
- **AWS Kiro** - agentic IDE/CLI centred on spec-driven development. https://kiro.dev
- **AGENTS.md** - the cross-agent context-spec convention. https://agents.md/
- **anthropics/skills** - the official Agent Skills repo (examples, the SKILL.md spec). https://github.com/anthropics/skills
- **anthropics/claude-plugins-official** and **anthropics/claude-plugins-community** - the curated and community plugin marketplaces. https://github.com/anthropics/claude-plugins-official · https://github.com/anthropics/claude-plugins-community
- **anthropics/claude-cookbooks** - recipes for RAG, tool use, sub-agents, evals. https://github.com/anthropics/claude-cookbooks
- **humanlayer/12-factor-agents** - principles for production-grade LLM software. https://github.com/humanlayer/12-factor-agents
- **awesome-claude-code** (hesreallyhim) - curated skills, hooks, commands, plugins. https://github.com/hesreallyhim/awesome-claude-code
- **awesome-claude-code-subagents** (VoltAgent) - ~154 specialized subagents. https://github.com/VoltAgent/awesome-claude-code-subagents
- **awesome-claude-skills** (Composio; karanb192) - curated Skills lists. https://github.com/ComposioHQ/awesome-claude-skills
- **openai/codex** - OpenAI's terminal coding agent. https://github.com/openai/codex
- **Confirmed Claude Code skills used in this guide** - Plan Mode, subagents, hooks, MCP, CLAUDE.md, headless `-p`, git worktrees, `/code-review`, `/simplify`, `/security-review`, `/verify`, `/run`, `/loop`, `/schedule`, `/deep-research`, GitHub Actions `@claude`. Docs: https://code.claude.com/docs/

*Corrections from verification:* `anthropics/claude-plugins` (exact name) is a 404; use the `-official` / `-community` repos. "Ralph" is a documented technique (https://ghuntley.com/ralph/), not a packaged toolkit.

---

## How this guide was researched

Built from one broad cross-corpus digest plus per-stage `/deep-research` runs (fan-out web search, adversarial verification, cited synthesis). Some runs hit a research session limit mid-verify, so citations carry `[V]/[P]/[S]` tags in chapters 01-09. This resources list was link-verified by a dedicated agent in June 2026; the four `⚠️ unverified` essays in section 1 are the only items that still need a manual check.
