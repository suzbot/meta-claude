# Petri Project: Working Methodology

*Analysis performed 2026-04-18*

Petri is built by a product manager and an AI collaborator, and the **process of building it is itself an engineered artifact** — documented, versioned, and iteratively refined through its own retros. What follows is an attempt to name and generalize the methodology, so we can recognize relevant ideas when reading articles.

## Core principles (the stable layer)

Four commitments recur across every document and skill:

1. **Anchor to intent, not structure.** Every layer of work — plan, spec, test, fix — traces back to a 1-2 sentence narrative of what the player or character experiences. This is the single most load-bearing convention. Tests that assert "returns ActionPickup" validate structure; tests that assert "vessel ends up filled with water" validate intent. When a fix changes what the player experiences, it's a feature change, not a fix.

2. **Discussion before action, evidence before hypothesis.** Almost every skill has a mandatory halt — "Stop here and wait for user confirmation." `/fix-bug` demands reading the save file before forming any hypothesis. `/refine-feature` separates "align on intent" (Step 2a) from "discuss approach" (Step 2b) so the user's vision is surfaced before analysis locks in.

3. **Follow the existing shape.** New work is evaluated first against existing patterns: "Does something existing handle this, or can it be extended?" Divergence is a discussion point, not a silent choice. Sibling flows are explicitly scanned for the same gap when fixing bugs.

4. **Institutional knowledge compounds.** Retros don't just capture lessons — they update the skills that produced them. Values.md is *derived from* observed behavior, not prescribed. The process improves itself.

## The artifact architecture (layered by volatility)

Documents are organized by how often they change, and each has a single audience:

| Layer | Example | Purpose |
|---|---|---|
| **North star** (rarely changes) | VISION.txt | Core conceits and long-term direction |
| **Immutable contract** | Requirements (*-Reqs.txt) | What the user asked for, in narrative form |
| **Emerged principles** | Values.md | Design values surfaced through retros |
| **Collaboration norms** | CLAUDE.md | How we work together |
| **Phase design** | construction-design.md | What we're building this phase + design decisions (numbered DD-N) |
| **Current target** (replaced each step) | step-spec.md | Implementation-ready detail for the step in flight |
| **Reference** | game-mechanics.md (player behavior), architecture.md (code patterns), flow-diagrams.md (visual) |
| **Deferred work** | triggered-enhancements.md | Items postponed with explicit trigger conditions, not timelines |
| **Unsorted** | randomideas.md | Staging area for ideas not yet placed |

Two aspects make this distinctive:

- **Audience discipline.** `/update-docs` enforces a strict audience-per-file rule. Bug fixes don't go in README.md. Code flow descriptions don't go in game-mechanics.md. This keeps each document sharp.
- **Deferrals are contractual.** triggered-enhancements.md doesn't list "stuff to do later." Each entry has a trigger condition ("when ANY is met") — the decision about *when* is encoded explicitly, which prevents both premature work and indefinite drift.

## The skill architecture (workflows-as-code)

Skills are named, parameterized, composable workflows. Each is a `SKILL.md` that encodes an interaction protocol. The graph:

- `/new-phase`: Requirements → phase design doc
- `/refine-feature`: design doc step → step spec
- `/implement-feature`: step spec → code + tests (with TDD, anchor test first)
- `/test-world`: generate save file so a specific behavior becomes observable
- `/fix-bug`: evidence-first debugging with a hard "2nd bug = design review" rule
- `/update-docs`: fan out documentation updates by audience
- `/retro`: reflect, propose process changes, update skills
- `/remind-me`: fast doc-only lookup, no code reading

Skills are compositional: `/implement-feature` invokes `/update-docs` and `/retro`; `/fix-bug` can invoke `/refine-feature`. The cycle — design → refine → implement → test → fix → docs → retro — is the backbone, but it's a graph, not a pipeline.

## Distinctive methodological moves

Worth naming, because these are the parts that differ from common practice:

1. **Anchor stories propagate downward.** The phase design doc's anchor story *is* the handoff artifact to refinement. The step spec's anchor story *is* the basis for the anchor test. The test's assertion *is* the story restated as code. Loss at any stage breaks the chain.

2. **Design decisions are numbered and cross-referenced.** DD-N entries live in the phase design doc. Open questions, when resolved, are struck through with a cross-reference (`~~question~~ → DD-14`). Step specs cite DDs. This creates a traceable decision graph.

3. **Context discipline is explicit.** Skills prohibit reading architecture.md end-to-end, require Grep before Read, and name prior-step artifacts the current step depends on. Context budget is treated as a first-class resource.

4. **Routing rules prevent content bloat.** `/retro` explicitly routes proposals: design values → Values.md; communication norms → CLAUDE.md; workflow guardrails → the relevant skill; technical patterns → architecture.md. Prevents parallel content growth across retros.

5. **The 2nd-bug rule.** A second bug in the same feature triggers a mandatory design review — no more patching. Rate of bug arrival is an escalation signal.

6. **Human testing is epistemological, not ceremonial.** Unit tests passing ≠ feature complete. `/test-world` exists because some behaviors aren't observable without constructed preconditions.

7. **Drift checks.** `/refine-feature` explicitly checks whether implementation details in the plan have drifted from the requirements or DDs they originated from. The plan is a draft; requirements and DDs are ground truth.

8. **Frame before solve.** Every skill that touches a problem (fix-bug, refine-feature, new-phase) has an explicit framing step: *restate current state and desired state in functional terms.* A fix for the wrong problem produces correct-looking code that addresses the wrong cause.

## Skill audit

Petri's eight skills (`fix-bug`, `implement-feature`, `new-phase`, `refine-feature`, `remind-me`, `retro`, `test-world`, `update-docs`) plus one custom agent (`go-code-reviewer`) were designed incrementally during Petri's build. Several predate the current platform feature set, notably the auto-memory system.

This section identifies observed opportunities across three dimensions per skill: (1) overlap with new platform features, (2) outdated patterns or conventions, (3) unleveraged current capabilities. It is observational — specific fixes, rankings, and recommended actions belong in the Phase 3 proposal document.

### `/fix-bug`
Seven-step protocol: evidence → agree on problem → sibling-flow scan → classify → fix → escalation check → close. Core discipline is the evidence-first halt before any hypothesizing.
- **Overlap:** Minimal. Evidence-first discipline is session-local behavior that memory doesn't naturally hold.
- **Outdated patterns:** Context-budget warnings in the preamble were tuned for a tighter-context era.
- **Unleveraged capabilities:** Hooks are not used. The "2nd-bug = mandatory design review" rule relies on model discipline at the point of the second bug.

### `/implement-feature`
Read spec → create task list with dependencies → execute tasks in order. TDD, anchor-test-first.
- **Overlap:** None significant.
- **Outdated patterns:** Extensive "don't read X end-to-end" rules carried from a tighter-context era.
- **Unleveraged capabilities:** The Readiness Criteria pattern-alignment pass is done inline rather than via subagent delegation in isolated context.

### `/new-phase`
Read requirements → align on intent → conversation about approach → write phase design doc with DD-N entries.
- **Overlap:** None significant.
- **Outdated patterns:** None observed.
- **Unleveraged capabilities:** Memory is not used to capture cross-phase user preferences (PM style, which mechanics get deep-planned vs. iterated).

### `/refine-feature`
Build context → discussion-first with mandatory Step 2a halt → document decisions → trace execution path → write step spec. Longest skill in the set.
- **Overlap:** None significant.
- **Outdated patterns:** Extensive preamble on context-building, drift-checks, anchor-story quality checks, and execution tracing. Several context-discipline and pattern-alignment rules are restated across multiple skills.
- **Unleveraged capabilities:** Execution-path tracing in Step 2.7 is done inline rather than via subagent delegation.

### `/remind-me`
Haiku-powered doc-lookup agent. Reads `architecture.md`, `game-mechanics.md`, `config.go`, `triggered-enhancements.md`; prohibits reading implementation code.
- **Overlap:** Memory is not used to cache resolved answers for recurring lookups.
- **Outdated patterns:** None observed.
- **Unleveraged capabilities:** Memory writes for durable facts the PM often re-asks are not part of the current skill.

### `/retro`
Solicit impressions → optional history search via Explore subagent over session transcripts → assess friction across six categories → propose routed changes → implement on approval → update `memory/last-retro.txt` timestamp.
- **Overlap:** Significant. The auto-memory system (type: feedback) is designed for the kind of friction signals this skill captures. Current history search is custom-implemented against transcript `.jsonl` files with a hardcoded project-path format.
- **Outdated patterns:** History-search subagent instructions include a full hardcoded path. Transcript scanning reconstructs signals that could have been captured at the moment they occurred.
- **Unleveraged capabilities:** Memory writes for friction patterns as they happen. Memory reads to surface cumulative patterns across sessions.

### `/test-world`
Generate save-file preconditions for specific testable behaviors. Runs as a general-purpose subagent in forked context.
- **Overlap:** None significant.
- **Outdated patterns:** Templates are embedded in the skill body.
- **Unleveraged capabilities:** Hooks are not used for schema validation of generated save files; the skill relies on caller-requirements discipline and `state.go` re-reading.

### `/update-docs`
Read all audience-tagged files → apply changes per audience-discipline table → report what changed. Runs as general-purpose subagent in forked context.
- **Overlap:** None significant.
- **Outdated patterns:** None observed.
- **Unleveraged capabilities:** Post-update consistency checks across files are not performed (e.g., `DATA_MODEL.md` claims an entity that `architecture.md` doesn't mention).

### Custom agent: `go-code-reviewer`
Review pattern: performance antipatterns → error handling gaps → style/maintainability → project-specific context.
- **Overlap:** None significant.
- **Outdated patterns:** Review categories are language-general and still apply.
- **Unleveraged capabilities:** Structured memory is not used to track recurring code-review findings across sessions.

## What this methodology optimizes for

- **Emergent complexity.** Petri simulates culture emerging from simple rules. The methodology mirrors this — values emerge from retros, decisions compound through DD-N entries, the process itself evolves.
- **Continuity across context loss.** Every skill's artifact (phase-design, step-spec, DDs) is written to survive a cleared conversation. Context will be lost; the documents carry.
- **Dual agency.** A product manager and an AI are collaborating. Alignment gates, evidence requirements, and discussion-first protocols exist because the PM has vision that code can't infer, and the AI has implementation knowledge that prose can't capture.
- **Long horizon.** This is a years-long project with compounding institutional knowledge. Artifacts that seem over-engineered for a small feature make sense when the same patterns recur across 10+ phases.

## Is the process serving its goals?

From this analysis — based on the documents and skill definitions, not session-level observation of how the process feels during use — some things can be assessed with reasonable confidence and others cannot.

**What looks well-served:**
- **Features keep landing.** 20+ phases completed through the same cycle (design → refine → implement → test → docs → retro). The cycle is producing shippable slices, not stalling.
- **Compounding knowledge.** Retros actually update skill definitions and Values.md. Skills are not static documents written once — they've visibly evolved through use. That's rare and is the strongest signal that the methodology works as designed.
- **Decision traceability.** The DD-N pattern (numbered design decisions cross-referenced from step specs and open questions) means past reasoning survives context loss. When a new phase revisits an area, prior decisions are findable.
- **Alignment gates hold.** The mandatory-halt steps in /refine-feature and /fix-bug do get respected — evidenced by the detailed problem-framing language in completed step specs.
- **Sibling-flow discipline.** The "check sibling flows when fixing a gap" rule has caught real recurring bugs, visible in Values.md's examples.
- **Skill-set scope holds up against evolving platform defaults.** The per-skill audit shows most skills cover responsibilities not fully subsumed by newer platform features (memory, hooks, etc.) — the skills are solving problems that remain distinct as the platform evolves. Significant overlap is concentrated at a single point (`/retro` with the memory system); the rest of the skill set remains well-scoped.

**Pressure points and opportunities for greater efficiency:**

These are areas where the current approach works but could potentially be sharpened as AI-coding practices and tooling evolve. Specific tool and process choices are out of scope here — this is about naming where leverage exists.

- **Skill prose redundancy and tighter-context-era discipline.** Each SKILL.md is long, and similar guardrails reappear across skills (context discipline, evidence-first, same-pattern checks). Several of these rules — "grep before read," "don't read architecture.md end-to-end," bounded context-budget warnings — were tuned for a tighter-context era and are restated across multiple skills. As context-selection tooling matures and context windows grow, a lighter mechanism for sharing rules across skills could reduce both drift and per-session reading cost.
- **Memory system is largely unleveraged.** Multiple skills have state that is currently rebuilt per-session, reconstructed from transcripts, or not captured at all, where memory (user / feedback / project / reference) could hold it durably. The clearest instance is `/retro`'s transcript-scanning for friction signals — directly overlapping with what the memory system is designed to hold.
- **Enforcement relies on model discipline rather than structural mechanisms.** Rules like "2nd-bug = mandatory design review," schema validation on generated save files, and post-update doc consistency all live as prose instructions in skills. Hooks are not used anywhere in the skill set. As structural enforcement tools mature, some of this could shift from prose rules to enforced defaults.
- **Documentation fan-out.** /update-docs touches 7+ files per completion (README, CLAUDE.md, game-mechanics.md, architecture.md, flow-diagrams.md, design doc, step-spec, triggered-enhancements.md). Keeping all these consistent is a real per-phase cost. Tooling for documentation consistency checks or derivation across docs is a plausible leverage point.
- **Subagent delegation is underused.** Several skills do work inline (`/implement-feature`'s Readiness Criteria pattern-alignment, `/refine-feature`'s execution-path tracing) that could happen in isolated subagent context. Skill composition is also statically encoded — `/fix-bug` may invoke `/refine-feature`, `/implement-feature` invokes `/update-docs` and `/retro` — with the compositions hardcoded in skill prose.
- **Evaluating emergent behavior.** Headless simulation tests cover balance, but the broader question — "do characters form believable preferences?", "does helping feel right?" — is verified by the PM playing the game. Evolving practices for evaluating emergent or simulation-like systems may offer more structured techniques.
- **Cross-session working state.** step-spec.md is replaced each step, and prior context is carried forward via design docs + DD-N. This works, but richer mechanisms for persistent per-phase working state (beyond single-file replacement) may reduce context-reconstruction cost at the start of each session.

**What can't be determined from this analysis:**
- Whether the skill overhead feels worth it to the PM day-to-day, or whether it's quietly creating fatigue.
- Whether retro-driven skill updates are actually changing future outcomes, or mostly documenting past ones.
- How much time the discussion-first alignment gates save vs. how much they slow down simple tasks where the alignment was already clear.
- Whether the methodology's sophistication is proportional to the project's scale, or has grown past the point where it's load-bearing.
- Whether adopting currently-unleveraged platform features (memory system, hooks, additional subagent delegation) would actually improve day-to-day flow or add friction. Depends on factors the skill definitions alone don't reveal — how memory reads play out session-to-session, how hooks integrate with the Go toolchain, whether isolated-context delegation feels lighter or heavier than inline work.

**Overall read.** The process is serving its goals well and has the rare property of evolving itself — retros update the very skills that produced the retros. The main efficiency opportunities sit at the boundary between prose-encoded discipline and tooling: things currently maintained by careful instruction-writing that might transfer to more structural mechanisms as AI-coding practices and tools evolve. A deliberate analysis of which specific tools or practices to adopt is a separate exercise and should be done when the library of available options stabilizes.

## Using this as a lens for the library

When reading methodology articles, these questions should surface whether something is relevant:

1. Does it address **intent-anchoring** (narrative-driven tests, design docs, spec formats)?
2. Does it address **context engineering** (what to put in persistent docs vs conversation, audience discipline, how discipline should evolve as context windows grow)?
3. Does it address **workflow composition** (skills/agents as reusable protocols, subagent delegation patterns, alignment gates, pipeline-to-graph patterns)?
4. Does it address **compounding knowledge** (retros that update process, deferred-work tracking, decision traceability)?
5. Does it address **dual agency** (how humans and AI divide responsibility, evidence/verification protocols, stop-for-confirmation)?
6. Does it address **memory and durable AI-accessible state** (when to put knowledge in memory vs files vs transcripts, what kinds of signals memory is best for, memory-as-running-log patterns)?
7. Does it address **structural enforcement vs prose discipline** (hooks, validators, and mechanisms that enforce rules rather than relying on the model to remember them)?

Articles that only cover feature roundups or tool-level tips will be low-signal. Articles on context anchoring, feedback loops, design-first collaboration, context engineering, memory systems, or structural enforcement patterns should map closely onto this methodology and may sharpen it.
