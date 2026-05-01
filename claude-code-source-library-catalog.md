# Claude Code Source Library — Card Catalog

The promoted set of entries from the weekly intake scan. Covers Official Sources and Principles and Methodologies. Decisions about candidates (promote / drop / defer / discuss) are logged in [`intake-decisions.md`](intake-decisions.md).

**Purpose of the library:** surface two continuous signals for adoption — (1) new Claude Code and Anthropic platform features as they ship, and (2) emerging community-consensus patterns from practitioners building AI-assisted workflows. The curated entries below are the evaluated and promoted subset of what intake has produced. See the `library-intake-scan` skill ("Library: scope, schema, and evaluation" section) for the catalog schema, evaluation criteria, and scope discipline. The analysis chain (`/analyze-methodology` → `/analyze-structure` → `/opportunity-inventory` → `/map-opportunities` → `/draft-proposals`) is how library entries feed into project-specific improvement proposals.

---

## Official Sources

### [Claude Code: Routines](https://code.claude.com/docs/en/routines)
**Topics:** Saved Claude Code configurations (prompt + repositories + connectors) that run autonomously on Anthropic-managed cloud infrastructure without permission prompts. Three trigger types: scheduled (recurring cadence), API (HTTP endpoint with bearer token — wire into alerting systems or deploy pipelines), and GitHub events (react to pull request open, release, etc.). Scope is controlled by repository selection, environment configuration, and which connectors are included. Research preview introduced April 2026.
**Reach for it when:** Designing autonomous unattended workflows — backlog maintenance, alert triage, PR review, deploy verification, docs drift detection; deciding between cloud Routines vs. local `/loop` scheduling vs. Desktop scheduled tasks; wiring Claude Code into GitHub events or external systems via the API trigger.

### [Claude Code: Monitor Tool](https://code.claude.com/docs/en/whats-new/2026-w15)
**Topics:** Built-in tool that streams background process events into the conversation in real time. Claude can tail logs, watch build output, and react to live system state without manual output piping. Complements hooks (which react to Claude's actions) by letting Claude observe ongoing external state — together they cover both directions of the control loop. Introduced April 2026.
**Reach for it when:** Closing the feedback loop on background processes (builds, test runners, servers); designing workflows where Claude needs to observe and react to live events rather than just act and await results.

### [Claude Code: Ultraplan](https://code.claude.com/docs/en/whats-new/2026-w15)
**Topics:** Cloud-collaborative planning layer for Claude Code. Draft a plan from the CLI, review and annotate it in a web editor, then run it remotely or pull it back for local execution. Auto-creates a cloud environment on first use. Introduced April 2026.
**Reach for it when:** Collaborating on a plan with others before execution; wanting a persistent, reviewable record of a plan outside the session; evaluating whether cloud-based plan review fits your spec/design workflow.

### [Claude Code: What's New](https://code.claude.com/docs/en/whats-new)
**Topics:** Recent feature launches, announcements.
**Reach for it when:** Catching up after time away; confirming whether a capability you want has shipped.

### [Claude Code: Changelog](https://code.claude.com/docs/en/changelog)
**Topics:** Version-by-version record of changes.
**Reach for it when:** Tracing when a behavior changed; diagnosing differences between two versions.

### [Claude Code: Best Practices](https://code.claude.com/docs/en/best-practices)
**Topics:** Official guidance on using Claude Code effectively — CLAUDE.md, context management, tool usage patterns.
**Reach for it when:** Setting up a new project; questioning whether your current approach matches Anthropic's recommended patterns.

### [Claude Code GitHub Releases](https://github.com/anthropics/claude-code/releases)
**Topics:** Release notes with granular detail on what changed per version.
**Reach for it when:** You need version-specific detail beyond the high-level changelog (e.g., bug fixes, flag changes).

### [Claude Platform Release Notes](https://platform.claude.com/docs/en/release-notes/overview)
**Topics:** Changes to the broader Claude API / platform (models, features, cookbook, SDKs).
**Reach for it when:** Something Claude-related changed that isn't specific to the Code CLI; tracking model releases and API capabilities.

### [Claude Code: Memory (How Claude remembers your project)](https://code.claude.com/docs/en/memory)
**Topics:** Auto-memory system, four memory categories (user / feedback / project / reference), MEMORY.md entrypoint, per-project memory directory structure, how Claude decides what to remember, CLAUDE.md vs. memory distinction.
**Reach for it when:** Deciding whether project state belongs in files, memory, or elsewhere; auditing existing skills for overlap with memory-system functionality; planning a memory migration for transcript-scanning skills.

### [Claude Code: Hooks reference](https://code.claude.com/docs/en/hooks)
**Topics:** Hook event types (SessionStart, SessionEnd, UserPromptSubmit, Stop, PreToolUse, PostToolUse, and more), matcher system, JSON stdin / exit-code / stdout / stderr I/O protocol, HTTP hook variant, `/hooks` interactive command, security model (hooks run with full user permissions).
**Reach for it when:** Replacing prose-enforced rules with structural enforcement; blocking unsafe operations; integrating external validation at session or tool-call boundaries.
**Note:** Several new hook events shipped March–April 2026 (PreCompact, PostCompact, conditional `if` field, PermissionDenied, StopFailure, TaskCreated, WorktreeCreate) — re-verify this entry against current docs before using it as a complete reference.

### [Claude Code: Subagents reference](https://code.claude.com/docs/en/sub-agents)
**Topics:** Custom subagent structure (Markdown + YAML frontmatter), project-level (`.claude/agents/`) vs. user-level (`~/.claude/agents/`), `/agents` interactive command, per-subagent model and tool permissions, isolated context window behavior, when subagents preserve main-context budget.
**Reach for it when:** Designing persistent specialist agents; deciding when to delegate work into isolated context; configuring per-subagent tool restrictions or model cost trade-offs.

---

## Principles and Methodologies

### [thedecipherist: Manual-Driven Development](https://thedecipherist.com/articles/mdd/)
**Topics:** The canonical MDD reference (~7,500 words, 12 sections). Names the "confident divergence" problem — Claude making plausibly correct but wrong assumptions about unfamiliar systems, then writing tests against those same assumptions so failures slip through. Proposes a two-zone `.mdd/.startup.md` file that primes Claude with explicit system knowledge every session, plus a two-prompt architecture (separating audit from implementation) to prevent context-compaction failures. Includes real prompts from a networking audit, compression-ratio evidence, ten lessons from production failures, and a prompt library.
**Reach for it when:** Evaluating whether your system-knowledge priming (CLAUDE.md, step-specs, phase design docs) is actually preventing confident-wrong outputs; designing phased workflows that separate audit/document from implement/test; thinking about how to make the AI's system model explicit before it starts coding.

### [Fowler: Reduce Friction with AI (hub)](https://martinfowler.com/articles/reduce-friction-ai/)
**Topics:** Index page for the three-part series (Design-First Collaboration, Context Anchoring, Feedback Flywheel).
**Reach for it when:** Starting a session on AI collaboration improvement; deciding which sub-piece fits the current friction.

### [Fowler: Design-First Collaboration](https://martinfowler.com/articles/reduce-friction-ai/design-first-collaboration.html)
**Topics:** Progressive design discussion before any code is written. Five sequential levels: Capabilities → Components → Interactions → Contracts → Implementation. Core principle: "no code until the design is agreed." Names the "Implementation Trap" — AI embedding design decisions invisibly in generated code.
**Reach for it when:** Evaluating your alignment gates (Petri's `/refine-feature` Step 2a); deciding whether your upfront design is deep enough; catching yourself letting AI embed design decisions during implementation.

### [Fowler: Context Anchoring](https://martinfowler.com/articles/reduce-friction-ai/context-anchoring.html)
**Topics:** Externalizing AI-conversation decisions into a persistent living document *outside* the chat. Addresses fade over long sessions: decisions that live only in chat context are lost. Recommends a lightweight feature doc capturing decisions + rationale, rejected alternatives, constraints, open questions, implementation state.
**Reach for it when:** Justifying or improving Petri's step-spec.md and phase-design docs; deciding what to persist vs. let live in conversation; onboarding a new team member to this discipline. Direct sibling of Petri's existing practice.

### [Fowler: Feedback Flywheel](https://martinfowler.com/articles/reduce-friction-ai/feedback-flywheel.html)
**Topics:** Converting individual AI interactions into collective improvements. Captures four signal types — context gaps, instruction patterns, workflow sequences, failure root causes — and feeds them back into shared artifacts (priming docs, review commands). Practices: brief post-session reflection, stand-up sharing, retro agenda items, periodic artifact reviews.
**Reach for it when:** Designing or auditing `/retro`-style processes; checking whether your retros actually update infrastructure vs. just noting lessons; thinking about friction signals you might be missing.

### [Fowler: Harness Engineering for Coding Agent Users](https://martinfowler.com/articles/harness-engineering.html)
**Topics:** Framing the control system around an AI coding agent as "harness engineering." Distinguishes feedforward guides (anticipating failures before they occur — linters, rules, specs) from feedback sensors (detecting failures after the fact — tests, type checkers). Splits controls into computational (deterministic, fast) and inferential (LLM-based, slower). Names three regulation dimensions: maintainability, architecture fitness, and behavioral correctness — the third being the hardest to build. Closes with a cybernetic governor model: the harness redirects human judgment to where it matters most, not eliminates it.
**Reach for it when:** Designing or auditing the structural controls around your agent workflow — deciding what should be enforced deterministically vs. left to LLM judgment; evaluating whether your setup has adequate feedforward guides, not just after-the-fact feedback; thinking about where behavioral correctness gaps live and why they're hard to close.

### [Fowler: Context Engineering for Coding Agents](https://martinfowler.com/articles/exploring-gen-ai/context-engineering-coding-agents.html)
**Topics:** Context engineering patterns for long-running coding-agent sessions — progressive disclosure, relevance selection, budget management.
**Reach for it when:** Evaluating how context discipline scales (e.g., Petri's "grep-before-read" rules); thinking about what belongs in CLAUDE.md vs. a skill vs. a spec.

### [Fowler: Conversation — What and How](https://martinfowler.com/articles/convo-what-how.html)
**Topics:** Programming's core challenge is building systems that survive change by managing cognitive load through abstraction. Argues the "what" (domain intent) and "how" (implementation) form an *interdependent feedback loop* — not separate concerns to be cleanly separated. LLMs are effective as translation tools *within* that loop, not as replacements for architectural thinking. Covers TDD as loop operationalization, paradigms as change-management hooks, community vocabulary.
**Reach for it when:** Pushing back on "AI will design it for you"; thinking about why Petri's anchor stories work (they keep the what/how loop tight); evaluating whether abstractions in your code are emerging from real iteration or being imposed upfront.

### [Fowler: Conversation — LLMs and Building Abstractions](https://martinfowler.com/articles/convo-llm-abstractions.html)
**Topics:** Essential vs. accidental complexity. LLMs reduce accidental complexity (boilerplate, syntax, frameworks) but cannot replace the essential creative work of discovering and stabilizing abstractions. "The very act of writing code is where design decisions crystallize." Recommends LLMs as brainstorming partners, not primary architects; evolutionary design over upfront specification.
**Reach for it when:** Defending Petri's Isomorphism value when an AI proposes a too-clean-looking abstraction; deciding where to let AI drive (mechanical tasks, alternatives exploration) vs. stay in the driver's seat (abstraction design).

### [Fowler: Structured-Prompt-Driven Development (SPDD)](https://martinfowler.com/articles/structured-prompt-driven/)
**Topics:** Treats the prompt itself as a governed, version-controlled artifact kept in sync with code rather than a throwaway input. Introduces the REASONS Canvas — a seven-part scaffold (Requirements, Entities, Approach, Structure, Operations, Norms, Safeguards) covering abstract design, concrete operations, and governance constraints. Core discipline: "when reality diverges, fix the prompt first — then update the code." Names three developer skills the methodology depends on (abstraction-first design, explicit intent/constraint alignment, iterative review loops) and gives an explicit fitness assessment — strong for scaled standardized delivery and compliance-heavy systems, weak for firefighting and exploratory spikes.
**Reach for it when:** Designing or auditing a spec/prompt artifact that must survive across many sessions and contributors; deciding whether to invest in a sync discipline between prompt and code (vs. treating specs as one-shot); evaluating where governance constraints (compliance, non-negotiables) should live in a workflow. Sibling to Fowler Context Anchoring (durable artifacts), Schleicher (precision before code), and Addy Factory Model (spec quality multiplies) — SPDD is the most explicit on the prompt-as-living-artifact framing.

### [Adam Tornhill: How Long Should a Function Be? (And Why It's the Wrong Question to Ask)](https://adamtornhill.substack.com/p/how-long-should-a-function-be-and)
**Topics:** Argues function design — naming, explicit structure, visible intent — matters more than line count, and matters doubly under AI assistance because LLMs infer semantics from token patterns rather than understanding code the way humans do. Paired with a 5,000-Python-file empirical study (Tornhill et al., "Code for Machines, Not Just Humans") correlating CodeHealth (a quality metric calibrated for human comprehension) with semantic preservation after LLM refactoring — i.e., human-friendly code is also more LLM-compatible. Recasts refactoring as harness work: code structure is feedforward agent context.
**Reach for it when:** Justifying refactor proposals on the grounds of agent comprehension, not just human readability; evaluating whether code structure is leaking semantics the agent then has to guess at; making the case that code-quality investments compound across agent runs.

### [Trensee: Claude Code Advanced Patterns — Skills, Fork, and Subagents](https://www.trensee.com/en/blog/explainer-claude-code-skills-fork-subagents-2026-03-31)
**Topics:** Five-layer Claude Code architecture with a clear division of responsibility: CLAUDE.md handles policy, Skills handle procedures, Hooks handle enforcement, MCP handles external connectivity, Fork/Subagents handle cognitive isolation by role. Emphasizes sequential adoption — establish CLAUDE.md constraints first, convert repeated instructions to Skills second, add Hooks third, introduce subagents only when mixed-intent sessions are causing context drift. Corrects common misuse: Skills are not command shortcuts, subagents are not the default answer to complexity.
**Reach for it when:** Deciding which layer of the stack to reach for next; questioning whether a task actually warrants subagents or whether a Skill would suffice; making the case for restraint before adding orchestration complexity.

### [Simon Willison: Agentic Engineering Patterns (series)](https://simonwillison.net/2026/Feb/23/agentic-engineering-patterns/)
**Topics:** Living guide series documenting best practices for professional software development with coding agents. Distinguishes "agentic engineering" (experienced engineers amplifying expertise with agents) from "vibe coding" (casual, unreviewed LLM use). Covers how cheap code generation changes development economics, why test-first development produces more reliable agent output, and production workflow patterns from real AI-assisted systems. Designed as evergreen, updatable content — new chapters added weekly.
**Reach for it when:** Looking for a maintained, growing reference on agentic development practice from a practitioner who writes from production experience; wanting a grounded counterpoint to hype-driven takes on AI coding; checking whether a practice you're considering has been examined in the series.

### [Anthropic: Contextual Retrieval in AI Systems](https://www.anthropic.com/news/contextual-retrieval)
**Topics:** RAG technique — Contextual Embeddings + Contextual BM25 with benchmarks (49% reduction in failed retrievals; 67% with reranking).
**Reach for it when:** Designing or evaluating a RAG pipeline; looking for a canonical technique to benchmark against.

### [Claude Cookbook: Contextual Embeddings Guide](https://platform.claude.com/cookbook/capabilities-contextual-embeddings-guide)
**Topics:** Implementation companion to Anthropic's Contextual Retrieval.
**Reach for it when:** Actually building a Contextual Retrieval system, not just reading about it.

### [Hamel Husain: LLM Evals FAQ](https://hamel.dev/blog/posts/evals-faq/)
**Topics:** LLM evaluation methodology — LLM-as-Judge patterns, binary classification, domain-expert involvement, RAG eval tiers.
**Reach for it when:** Designing evals for an LLM-based feature; choosing between eval frameworks; adding structure to ad-hoc testing.

### [Hamel Husain: The Revenge of the Data Scientist](https://hamel.dev/blog/posts/revenge/index.html)
**Topics:** Argues that foundational data science disciplines — experimental design, custom metrics, systematic data examination — are now central to LLM product work, not optional. Identifies five failure modes when practitioners skip this discipline: generic off-the-shelf metrics that obscure real problems, LLM evaluators that haven't been validated themselves, test sets built from generic prompts rather than production data, labeling done without domain experts, and over-automation that replaces data examination with dashboards. Core claim: "look at the data" is almost always the highest-leverage activity when LLM-powered features misbehave.
**Reach for it when:** Diagnosing why an LLM feature isn't performing as expected — the five failure modes are a concrete checklist for where to look first; auditing whether your eval setup has the traps that cause invisible failures. Diagnostic companion to the Evals FAQ above.

### [Addy Osmani: How to Write a Good Spec for AI Agents](https://addyosmani.com/blog/good-spec/)
**Topics:** Five principles for effective agent specs: vision-first planning, structured PRD-style documentation covering commands, testing, project structure, code style, git workflow, and boundaries; modular task division to avoid instruction overload; three-tier behavioral boundaries (Always / Ask First / Never); and iterative evolution as understanding improves. Argues conformance testing derived from specs creates objective success criteria.
**Reach for it when:** Writing or auditing a spec for an AI agent task; deciding how to scope agent behavior constraints — the three-tier boundary framework (Always/Ask First/Never) is a concrete decision tool for this; checking whether a spec is structured enough to be testable.

### [Addy Osmani: Stop Using /init for AGENTS.md](https://addyosmani.com/blog/agents-md/)
**Topics:** Counterargument to auto-generated context files. Agents can already discover directory structure, tech stack, and module organization by reading the codebase — including that in AGENTS.md adds noise, not value. Reports +20% token cost and -2-3% performance degradation from redundant context. Recommends minimal, hierarchical context files containing only non-discoverable information (tooling requirements, gotchas, conventions). Core heuristic: "can the agent find this by reading the code? If yes, delete it."
**Reach for it when:** Auditing an existing CLAUDE.md or AGENTS.md for bloat; questioning whether a context file is helping or hurting; deciding what belongs in CLAUDE.md vs. what should be left for the agent to discover.

### [Addy Osmani: The Code Agent Orchestra](https://addyosmani.com/blog/code-agent-orchestra/)
**Topics:** Shifting from guiding a single agent in real time (conductor) to managing an autonomous team (orchestrator). Introduces an 8-levels-of-AI-assisted-coding framework — most developers operate at levels 3–4; true orchestration begins at level 6. Three multi-agent patterns: subagents (hierarchical hub-and-spoke), Agent Teams (parallel execution with shared task lists), and cloud orchestrators (5+ agents across repositories). Central claim: "your spec is the leverage" — strong specifications multiply effectiveness across agent fleets while vague requirements propagate errors exponentially.
**Reach for it when:** Evaluating whether your current workflow sits at the right level of the 8-level hierarchy; deciding which multi-agent pattern fits a task's dependency structure; making the case for spec quality as the primary leverage point in a multi-agent setup.

### [Addy Osmani: The Factory Model](https://addyosmani.com/blog/factory-model/)
**Topics:** Reframes the developer's role with AI agents: not pair programming but building the factory that builds software — orchestrating fleets of autonomous agents working in parallel. Traces three generations of AI coding tools and argues that specification quality now multiplies across agent runs, making vague requirements exponentially costly. Identifies a bottleneck inversion: verification now takes more time than generation. Names high-leverage skills: systems thinking, problem decomposition, specification writing, and orchestration oversight.
**Reach for it when:** Calibrating how much effort should go into specs vs. generation; making the case for spec quality investment; understanding why the test/verification bottleneck now matters more than generation speed. Extends the "spec is the leverage" argument from the Code Agent Orchestra entry into a first-principles framing.

### [Addy Osmani: Agent Harness Engineering](https://addyosmani.com/blog/agent-harness-engineering/)
**Topics:** "A decent model with a great harness beats a great model with a bad harness." Introduces the Harness Equation (agent = model + harness), the Ratchet Principle (treat each agent failure as a permanent design constraint — encode it into rules, hooks, or documentation rather than dismissing it as one-off), and Behavior-First Design (derive harness components from behaviors the model can't achieve independently). Catalogs core harness components: filesystem/git, sandboxed execution, memory, hooks, AGENTS.md, verification loops.
**Reach for it when:** Specifically for the Ratchet Principle as a post-failure discipline — when something goes wrong, ask what permanent constraint should encode that lesson. See Fowler Harness Engineering for the deeper conceptual framework; this entry is the tactical complement.

### [Addy Osmani: Long-running Agents](https://addyosmani.com/blog/long-running-agents/)
**Topics:** Durability and resumption patterns for agents operating across days or weeks. Argues the load-bearing component is persistent state outside the model's context window — checkpoint-and-resume mechanics, paused-in-place execution under delegated approval, memory governance treated like microservice configuration, and verifiable event logs for auditability. Surveys four named architectures: the **Ralph Loop** (task list in `prd.json`, fresh agent invocations reading external state, no model memory required), Anthropic's **Brain/Hands/Session Split** (model loop separated from ephemeral execution containers; append-only event log enables resumption by session ID), Cursor's **Planner/Worker/Judge** (different models for exploration, focused execution, verification — separates evaluator from generator to avoid optimism bias), and Google's **Memory Bank Layering** (curated long-term memory indexed by user/session). Operational discipline: write done-conditions explicitly before execution; use structured handoff files for context resets rather than relying on summarization; choose checkpoint granularity at meaningful work units, not per-step or only at completion.
**Reach for it when:** Designing or auditing an agent workflow that needs to survive failures, context resets, or multi-day timelines; choosing between fresh-context loops and continuous-session approaches; deciding how much to invest in session logs and external state stores. Distinct from Code Agent Orchestra (orchestration patterns) and Factory Model (process structure) — Long-running Agents is the durability/resumption complement to those.

### [Eric Evans: AI Components for a Deterministic System](https://www.domainlanguage.com/articles/ai-components-deterministic-system/)
**Topics:** Wrapping non-deterministic AI inside deterministic software; strategic design around AI components; DDD-adjacent framing.
**Reach for it when:** Thinking about how to constrain AI-generated or AI-driven behavior inside a deterministic product (Petri is deterministic at runtime; this is the inverse case but the framing transfers).

### [Stack Builders: Beyond AGENTS.md — Building Reliable AI Coding Workflows with Context Engineering](https://www.stackbuilders.com/insights/beyond-agentsmd-turning-ai-pair-programming-into-workflows/)
**Topics:** Structured workflow framework that extends beyond basic AGENTS.md. Treats context engineering as foundational infrastructure (structured, versioned context before workflows). Proposes explicit personas (architect, engineer, reviewer) instead of generic agents, deterministic named commands (e.g., `$prepare`, `$start-design`, `$start-feature`, `$commit`), and design-first execution with specs as source of truth.
**Reach for it when:** Designing or auditing skill/command sets (Petri's slash commands, NQ's 8-step cycle); evaluating whether ad-hoc context files are reaching their useful limit; thinking about role-based agent separation.

### [Daniel Schleicher: Removing Ambiguity with Spec-Driven Development](https://www.danielschleicher.com/software/engineering,/ai,/spec-driven/development/2026/01/04/removing-ambiguity-with-spec-driven-development.html)
**Topics:** DDD's Ubiquitous Language applied to AI-assisted development. LLMs as design conversation partners, not code generators. Introduces a Spec Ambiguity Resolver concept — a living glossary where AI proposes definitions for team approval before implementation, ensuring semantic consistency from spec through code. "Precision before code."
**Reach for it when:** Introducing a new domain concept and wanting AI collaboration to stay grounded in shared terminology; thinking about code-domain alignment (Petri's Isomorphism, NQ's DATA_MODEL.md-first debugging); evaluating whether ambiguity in requirements is causing downstream drift.

### [Claudefa.st: Auto Dream — Memory Consolidation](https://claudefa.st/blog/guide/mechanics/auto-dream)
**Topics:** How Claude Code's auto-dream mechanism keeps accumulated memory clean over time. Runs automatically after 24+ hours and 5+ sessions. Four phases: reviewing existing memory structure, searching transcripts for corrections and recurring patterns, consolidating by merging overlaps and removing contradictions, and pruning the MEMORY.md index to stay under 200 lines. Can be triggered manually after major refactors.
**Reach for it when:** Memory has accumulated across many sessions and Claude's recall is degrading; planning a memory migration or restructure; understanding how auto-dream interacts with MEMORY.md so you can write memory files it will consolidate cleanly.

### [Claudefa.st: Hooks Guide](https://claudefa.st/blog/tools/hooks/hooks-guide)
**Topics:** Nine production hook patterns: auto-formatting, context injection, security blocking via PreToolUse, auto-approval gates, transcript backups, task enforcement, skill activation, StatusLine-triggered backups, and setup hooks. All four handler types (command, HTTP, LLM prompt, agent-based). Exit code 2 as the sole blocking mechanism. Covers async hooks (Jan 2026, for non-blocking background operations) and HTTP hooks (Feb 2026, for remote validation services and centralized team policies).
**Reach for it when:** Moving from knowing what hooks can do to actually implementing them in production; choosing between the four handler types for a specific use case; looking for patterns around async hooks or HTTP hooks that aren't in the official reference docs.
**Note:** A fifth handler type shipped April 23, 2026 (v2.1.118): hooks can now invoke MCP tools directly via `type: "mcp_tool"`. Several hook events (PreCompact, PostCompact, conditional `if`, PermissionDenied, StopFailure, TaskCreated, WorktreeCreate) also post-date this entry — re-verify against current docs before using it as a complete reference.

### [Claudefa.st: Sub-Agent Best Practices](https://claudefa.st/blog/guide/agents/sub-agent-best-practices)
**Topics:** How to route sub-agent work across three execution patterns — parallel (independent domains, no file overlap), sequential (dependent tasks), and background (non-blocking research). Emphasizes invocation quality (context, file references, success criteria) over execution. Covers cost-optimized model selection (Sonnet for sub-agents), persistent agent definitions in `.claude/agents/`, routing rules in CLAUDE.md, and permission rules. Flags common mistakes like over-parallelizing trivial tasks.
**Reach for it when:** Designing or tuning sub-agent use; evaluating whether current dispatch choices match task dependencies; auditing whether you're over- or under-parallelizing.

### [Shipyard: Multi-Agent Orchestration for Claude Code](https://shipyard.build/blog/claude-code-multi-agent/)
**Topics:** Counterweight to multi-agent enthusiasm. Covers three approaches (Agent Teams, Gas Town, Multiclaude) but leads with an honest constraint: multi-agent workflows suit roughly 5% of development tasks and carry significant token cost and experimental risk. Practical guardrails: budget substantial compute, invest heavily in precise initial prompts, implement test/validation loops, accept that these tools remain unstable.
**Reach for it when:** Pressure-testing whether a task actually warrants multi-agent coordination; making the case for restraint when a simpler single-session approach would do; calibrating cost and complexity expectations before committing to an orchestration pattern.

### [Claudefa.st: Agent Teams](https://claudefa.st/blog/guide/agents/agent-teams)
**Topics:** Claude Code's experimental Agent Teams feature — multiple parallel sessions with direct inter-agent messaging and shared task lists, vs. hub-and-spoke subagents that only report results. Covers role-based spawning, shared task coordination with dependency tracking, mesh communication, permission presets, and CLAUDE.md as runtime context for teammates. Feature-specific.
**Reach for it when:** Specifically evaluating Claude Code's Agent Teams feature for a project. If the feature changes significantly, this article ages fast — re-evaluate periodically.

### [Claudefa.st: Auto Mode](https://claudefa.st/blog/guide/development/auto-mode)
**Topics:** Documents Auto Mode's layered safety architecture: user permission rules take priority, then read-only operations auto-approve, then a separate classifier model evaluates remaining actions — with Claude's own text stripped from the classifier's input to prevent hostile file content from manipulating decisions. Describes the behavioral contract Auto Mode sends to Claude (execute immediately, minimize interruptions, avoid destructive actions, never exfiltrate secrets). Usage guidance: appropriate for long autonomous tasks; inappropriate for production infrastructure changes or unfamiliar code requiring review. GA April 2026.
**Reach for it when:** Deciding whether to enable Auto Mode for a session type; understanding the security design and where the classifier can be fooled; configuring custom rules alongside the built-in defaults via `"$defaults"`.
**Note:** Feature-specific — will need re-evaluation as Auto Mode evolves.

### [The AI Corner (Ruben Dominguez): Claude Best Practices Power User Guide 2026](https://www.the-ai-corner.com/p/claude-best-practices-power-user-guide-2026)
**Topics:** Systematic personal workflow design. Persistent file system loaded before every session (about-me.md, voice-profile.md, anti-AI-writing-style.md), Socratic prompting (ask clarifying questions before executing), XML-tagged structured prompts with explicit context/tasks/constraints/examples, Skills and Projects as durable infrastructure, fresh sessions for new topics to protect output quality.
**Reach for it when:** Setting up or refining your personal Claude workflow; questioning whether your CLAUDE.md and skill files are carrying enough weight; thinking about how voice, style, and context files compound over time.

### [PopularAiTools (Wayne MacDonald): Claude Code Workflow Patterns — Agentic Guide 2026](https://popularaitools.ai/blog/claude-code-workflow-patterns-agentic-guide-2026)
**Topics:** Five workflow patterns tested across production codebases — Sequential Flow (Explore-Plan-Act), Operator (central orchestrator), Split-and-Merge (parallel worktrees), Agent Teams (autonomous collaboration), Headless (fully autonomous). Provides decision framework with tradeoffs (token costs, human oversight levels). Pushes back on over-engineering: "a well-crafted single prompt with Plan Mode often beats a poorly coordinated team of agents."
**Reach for it when:** Choosing between workflow structures for a task; evaluating whether an agent team is actually the right tool for the job or over-engineered; wanting a ranked taxonomy rather than a list of equally-weighted options.

### [Boris Tane: How I Use Claude Code](https://boristane.com/blog/how-i-use-claude-code/)
**Topics:** First-person daily-use workflow developed over 9 months. Four phases held strictly separate: deep research → detailed plan → iterative annotation → uninterrupted implementation. Specific reusable patterns: mandatory `research.md` and `plan.md` artifacts (custom markdown, not built-in Plan Mode) so the editor stays the control surface; annotation cycles of 1–6 rounds with explicit "don't implement yet" guards as the human-judgment injection point; granular todo lists nested inside the plan as progress trackers; standardized implementation prompt emphasizing continuous execution; terse single-sentence corrections during execution and *revert and re-scope* rather than incrementally patch when something goes wrong; preference for single long sessions to leverage end-to-end context persistence.
**Reach for it when:** Designing or auditing a personal day-to-day Claude Code workflow; deciding what gets a `plan.md` vs. a quick prompt; choosing between revert-and-re-scope and incremental fix when an implementation goes off course; deciding whether to break work across sessions or hold context end-to-end. Sibling to PopularAiTools (workflow taxonomies) and AI Corner (personal workflow design) — Tane is sharper on the annotation cycle as the judgment-injection point and on revert-and-re-scope as a discipline.

### [everything-claude-code](https://github.com/affaan-m/everything-claude-code)
**Topics:** Comprehensive agent harness ecosystem available as a Claude Code plugin. Contains 48 specialized subagents (planning, architecture, code review, security, language-specific), 183 skills covering TDD, frontend/backend, security scanning, and continuous learning, 34 rules across language ecosystems (TypeScript, Python, Go, Swift, and more), and 16+ hook scripts for formatting, secret detection, testing, and documentation. Educational content includes a Longform Guide (token optimization, memory persistence, evaluation strategies) and a Security Guide (attack vectors, sandboxing). Cross-harness support for Cursor, Codex CLI, OpenCode, and Gemini. 161k stars, 170+ contributors.
**Reach for it when:** Looking for a drop-in harness to bootstrap a new project's skills, hooks, and subagent definitions; evaluating language-specific rules (especially Go, Swift, or Kotlin) before writing your own; wanting structured guidance on token optimization or agent security that goes beyond official docs. Resource-type entry — functions as a drop-in library, not a reading article.

### [VoltAgent: awesome-claude-code-subagents](https://github.com/VoltAgent/awesome-claude-code-subagents)
**Topics:** Community-curated collection of 130+ Claude Code subagent definitions across 10 categories (core development, language specialists, infrastructure, quality/security, data/AI, developer experience, specialized domains, business/product, meta-orchestration, research). Each definition includes YAML frontmatter (name, description), tool-permission scoping, model routing across Opus/Sonnet/Haiku, and role-based system prompts. Drop-in usage via Claude plugin marketplace, manual copying, or interactive installer. Includes a `golang-pro` language-specialist subagent among others. Strong community validation (17.7k stars, 2k forks, actively maintained).
**Reach for it when:** Designing a new subagent and wanting reference templates for structure, tool-permission scoping, or model routing; evaluating whether a language-specialist or domain-specialist subagent already exists in the community before building one from scratch. Resource-type entry — functions as a drop-in library, not a reading article.

### [hesreallyhim: awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code)
**Topics:** Curated awesome-list spanning the broader Claude Code ecosystem — skills, hooks, slash commands, agent orchestrators, applications, and plugins. Stated curation criteria emphasize "code quality, security, and originality" rather than exhaustive coverage; entries are reviewed against those filters before inclusion. Repository goes beyond a flat list with `tests/`, `templates/`, executable curation tooling (Makefile, pyproject.toml, pre-commit config), and a `THE_RESOURCES_TABLE.csv` data file. 41.8k stars, 1,100+ commits, actively maintained.
**Reach for it when:** Looking for a wide entry-point directory across the whole Claude Code ecosystem (not just one component); discovering a skill, hook, or orchestrator before building one from scratch. Resource-type entry — navigational reference, not methodology reading. Coexists with VoltAgent's awesome-claude-code-subagents (subagent-specific deep dive); this entry is the broader index.

### [thedecipherist: Claude Code Mastery Project Starter Kit](https://thedecipherist.com/articles/claude-code-mastery-project-starter-kit/)
**Topics:** Template repository companion to the MDD article. Provides 23 slash commands, 9 hooks, 10 rules, and 12+ project profiles (Next.js, Go, Vue, Django, etc.). Includes deterministic hooks that block unsafe actions, a database wrapper enforcing safety patterns, and CLAUDE.md rules covering secrets/TypeScript strictness/API versioning. Tactical scaffolding rather than methodology.
**Reach for it when:** Starting a new project and looking for concrete scaffolding patterns; evaluating which guardrails might apply to your own setup. Template-as-reference, not an article-as-reading.

### [get-shit-done](https://github.com/gsd-build/get-shit-done)
**Topics:** Spec-driven, multi-agent orchestration system for Claude Code that specifically addresses context rot — quality degradation as the context window fills during long sessions. Six phases: Initialize (requirements extraction, roadmap), Discuss (design preferences before planning), Plan (atomic task plans in strict XML format), Execute (wave-based parallel execution with fresh context windows per task), Verify (acceptance testing and automated debugging), Ship (pull requests). Structured markdown files (PROJECT.md, REQUIREMENTS.md, ROADMAP.md) loaded strategically rather than dumped. Independent plans run in parallel waves; dependent plans sequence. 57k stars, actively maintained. Resource-type entry.
**Reach for it when:** Looking for a packaged spec-driven workflow system to adopt or mine for patterns — especially the wave-based parallel execution model and the context rot framing; structuring large multi-session projects with consistent phase discipline.

### [JetBrains: Write Modern Go Code with Junie and Claude Code](https://blog.jetbrains.com/go/2026/02/20/write-modern-go-code-with-junie-and-claude-code/)
**Topics:** Addresses two failure modes in AI-assisted Go development: data cutoff limitations (models can't suggest features from recent releases) and frequency bias (models favor older patterns because training data contains more legacy code). Proposes version-aware guidelines that auto-detect Go version from `go.mod` and apply version-appropriate idioms. Claude Code users activate via `/use-modern-go`. Contrasts obsolete patterns (manual loops) with modern equivalents (`slices.Contains()` in Go 1.21+, `errors.AsType[T]()` in Go 1.26) as concrete examples.
**Reach for it when:** Starting or auditing Go development workflows with Claude Code; diagnosing why Claude is generating older-style Go code; deciding whether to add version-aware context priming to your CLAUDE.md for Go projects. Language-specialist entry.

---

## Quick-lookup by topic

- **Context discipline / what goes where:** Fowler Context Anchoring; Fowler Context Engineering; Claude Code Best Practices; thedecipherist MDD (two-zone docs); AI Corner power user guide (persistent file system).
- **Specs, plans, and alignment gates:** MDD pieces; Fowler Design-First Collaboration (the five-level model); Fowler What and How (why the loop matters); Fowler SPDD (prompt-as-living-artifact, REASONS Canvas); Schleicher (precision before code); Addy Factory Model (spec quality multiplies at fleet scale).
- **Retros and feedback loops:** Fowler Feedback Flywheel (four signal types).
- **Abstractions and code-domain alignment (DDD / Isomorphism):** Fowler LLMs and Building Abstractions; Fowler What and How; Eric Evans; Schleicher (ubiquitous language).
- **Code as agent context:** Tornhill (function design / code legibility for LLM inference, with empirical CodeHealth × refactoring study).
- **Evals and testing:** Hamel Evals FAQ (methodology); Hamel Revenge of the Data Scientist (failure-mode diagnostics).
- **RAG:** Anthropic Contextual Retrieval + Claude Cookbook.
- **Overall workflow synthesis:** AI Corner power user guide.
- **Workflow patterns and decision framework:** PopularAiTools (five patterns, ranked); Stack Builders (personas + deterministic commands); get-shit-done (wave-based parallel execution, context rot mitigation).
- **Harness design:** Fowler Harness Engineering (feedforward/feedback framework); Addy Agent Harness Engineering (Ratchet Principle, tactical).
- **Auto Mode / permissions:** Claudefa.st Auto Mode.
- **Sub-agents and agent teams:** Claudefa.st Sub-Agent Best Practices (dispatch patterns); Claudefa.st Agent Teams (feature-specific); VoltAgent awesome-claude-code-subagents (template collection).
- **Templates and scaffolding:** thedecipherist Starter Kit; VoltAgent awesome-claude-code-subagents (subagent-specific); everything-claude-code (full harness ecosystem).
- **Language specialist (Go):** JetBrains Go + Claude Code (version-aware idioms).
- **Autonomous / unattended workflows:** Claude Code Routines (Official Sources).
- **Product / version tracking:** Official Sources section.
