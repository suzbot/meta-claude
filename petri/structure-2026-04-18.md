# Petri Project: Structural / Technical Analysis

*Analysis performed 2026-04-18*

Companion to `methodology-2026-04-18.md`. That document describes *how* the project is built; this one describes *what* it is, technically, and how it's set up to be that. The goal is to have a sharp reference for judging which coding and agent practices from the library apply — and which are about a different kind of system entirely.

## What Petri is, in one paragraph

Petri is a **terminal-based, single-player, deterministic simulation** written in Go (~59k lines, ~50% tests) using Bubble Tea's Model-View-Update pattern for its UI. It runs as a single local process with no network, no database, no concurrency primitives, and no LLMs at runtime. State lives entirely in memory and serializes to local JSON save files. Every tick, the simulation advances deterministically: character bodies change (hunger grows, energy drains), each character decides what to do, then each character's decision is executed. The user is an observer and occasional director — not a controller.

## Stack

- **Language:** Go 1.25
- **UI:** `charmbracelet/bubbletea` (MVU) + `charmbracelet/lipgloss` (ANSI styling)
- **Storage:** Local JSON files (`~/.petri/worlds/world-XXXX/state.json`)
- **No**: network, DB, ORM, async runtime, external services, LLM integration, vector store, message queue

This stack is notably minimal. The architectural complexity lives in the simulation, not the infrastructure.

## What it's technically trying to accomplish

Four technical commitments drive the architecture:

1. **Emergent behavior from simple rules.** Characters aren't scripted; their behavior falls out of stat urgency, scoring functions, and per-character state (preferences, knowledge, memory). The code must make it possible to tune the rules and watch the outcomes, rather than to specify outcomes directly.

2. **Code structure isomorphic to game concepts.** ([Values.md: Isomorphism](../petri/docs/Values.md).) If the game has a concept, there's one place in the code where that concept lives. Someone asking "how does a character decide what to eat?" finds the answer where decisions live (`intent.go`), not buried in pathfinding.

3. **No omniscient state.** Knowledge, memory, and experience live per-character, not in a global log. A character's action log is *their* working memory, bounded. When a character dies, their knowledge dies with them. The data model mirrors the fiction.

4. **Deterministic, rewindable, observable.** Every tick is reproducible given the state. Save/load must capture everything that matters. Emergent behavior can only be studied if runs are rewindable.

## The architectural spine: the cognitive loop

The central design pattern is that code boundaries map to steps in a character's per-tick cognitive loop:

| Cognitive step | Code location | Role |
|---|---|---|
| Body (passive changes) | `survival.go` | Stat decay, sleep/wake, damage |
| Motivation (what do I need?) | `intent.go` | Urgency tiers, stat priority |
| Evaluation (what options?) | `intent.go` + scoring files | Food scoring, drink finding, sleep finding |
| Selection (which wins?) | `intent.go` + `order_execution.go` + `helping.go` + `discretionary.go` | Four-bucket routing: Needs → Orders → Helping → Discretionary |
| Spatial planning | `movement.go` (NextStepBFS) | Greedy-first pathfinding with sticky BFS |
| Execution | `apply_actions.go` (applyIntent) | All side effects — movement, consumption, crafting, talking |

The **Intent struct** is the universal contract between Selection and Execution. Calculation produces an Intent; application consumes one. This clean split is what makes the simulation testable, rewindable, and eventually parallelizable.

## Key structural patterns

1. **Four-Bucket Decision Model.** Priority routing (Needs → Orders → Helping → Discretionary), each bucket in its own file. The orchestrator in `CalculateIntent` calls each bucket explicitly. Deliberately explicit over clever.

2. **Tier-based urgency.** Stats have five tiers (None, Mild, Moderate, Severe, Crisis). Max tier governs routing. Tier-worsening triggers re-evaluation mid-flow.

3. **Self-managing vs ordered actions (two interruption paradigms).**
   - *Self-managing* actions (ForageAuto, FillVessel, HelpFeed, HelpWater) own their full multi-phase flow as a single continuous intent; they yield to needs when `CalculateIntent` detects elevated tiers.
   - *Ordered* actions (TillSoil, Plant, Craft, BuildFence) complete one work unit then clear intent — the one-tick idle window is the natural re-evaluation point for needs-based interruption.

4. **Stateless phase detection.** Multi-phase actions infer their current phase by reading world state ("is my target vessel on the ground or in inventory?") rather than storing phase numbers. Save/load survives in-flight actions for free.

5. **Shared tick helpers with status enums.** `RunVesselProcurement` returns `ProcureReady | ProcureApproaching | ProcureInProgress | ProcureFailed`. Handlers switch on status rather than re-implementing the sub-flow. The same shape for `RunWaterFill`.

6. **Data-driven extensibility via registries.** `ActivityRegistry` (entity/activity.go), `RecipeRegistry` (entity/recipe.go), `ItemLifecycle` config map. Adding a new activity or recipe is primarily a data change plus a small number of wiring points enumerated in "Adding New X" checklists in architecture.md.

7. **Descriptive vs functional attribute split.** Items have *descriptive* attributes (ItemType, Color, Pattern, Texture, Kind — what they ARE) and *functional* attributes (Edible, Poisonous, Plantable, Healing — what they DO). Characters form opinions only against descriptive attributes. This split is load-bearing for the emergent-culture concept.

8. **Simple flags over ECS.** Capability booleans (Edible, Poisonous, Passable, Plantable) rather than a pure Entity-Component-System. Optional property structs (`EdibleProperties`, `PlantProperties`, `ContainerData`) nil when not applicable. Pragmatic; can evolve toward ECS if needed.

9. **Sparse grid + indexed slices.** O(1) position lookups via maps (`water map[Position]WaterType`, `tilled map[Position]bool`). Separate slices for characters/items/features/constructs. Not a scene graph; not a single ECS world.

10. **Ephemeral vs serialized state as an explicit distinction.** Displacement fields, `UsingBFS` flag — ephemeral, not serialized, cleared on save/load. This is a deliberate architectural line, not an oversight.

11. **Headless simulation tests for balance.** `internal/simulation/observation_test.go` runs the game logic without UI to measure emergent behavior (does the community starve? does gardening sustain food supply?). Balance is observed, not asserted.

12. **Position abstractions over inline math.** `pos.DistanceTo(other)`, `pos.IsCardinallyAdjacentTo(other)`. Never inline `abs(x1-x2) + abs(y1-y2)`. Values.md: Isomorphism.

13. **Serialization discipline.** Explicit `ToSaveState` / `FromSaveState`. Constructor-set fields (symbols, certain flags) must be explicitly restored on load. Variety round-tripping: items can be reduced to variety references (e.g., in vessel stacks) and reconstituted, so the variety must carry every field the reconstituted item needs.

14. **Bundles and variety-locked vessels.** Inventory has two slots, each holding loose item / vessel / bundle. Vessels lock to first-added variety. Bundles have max sizes. These rules are where a lot of interaction richness lives.

## What this is NOT, technically

Naming what it isn't clarifies which library articles miss the target:

- **Not a web service, not a database-backed app.** No CRUD, no migrations-in-flight, no ORM, no transactions.
- **Not an LLM-powered product.** The word "agent" here means *simulated character*, not *LLM with tools*. Zero LLM calls at runtime. This distinction is important because most 2026 articles about "agents" mean the other thing.
- **Not async or event-driven** in the reactive sense. Deterministic tick loop. Parallel intent calculation is explicitly deferred in triggered-enhancements.md.
- **Not a microservice.** One binary, one process.
- **Not networked.** No auth, no rate limiting, no API design, no network resilience.
- **Not a search or retrieval system.** The world state is the knowledge base, fully in-memory. No embeddings, no vector DB, no RAG.
- **Not streaming.** In-memory state is canonical; saves are snapshots, not event logs.

## Implications for which library practices apply

The library should be read through this lens:

**Highly relevant to Petri:**
- Context engineering for AI-assisted coding (how to prompt, what to load into context). The *build process* uses Claude Code heavily — methodology articles that improve that process compound.
- Intent-anchoring, spec/test design, decision traceability. Petri already practices these; articles may sharpen them.
- Sub-agent patterns and multi-agent workflows — for *development*, not runtime. Code review, exploration, parallel research.
- Skill and slash-command composition — Petri lives on this.
- Workflows for long-running projects with compounding institutional knowledge.
- Test discipline for complex systems — especially emergent ones where regressions are subtle.

**Irrelevant or misleading when applied to Petri:**
- RAG patterns, chunking, embedding, reranking. Petri has no corpus, no retrieval. The knowledge model is per-character in-memory state.
- Vector databases, hybrid search. Same reason.
- LLM orchestration patterns (prompt chaining, tool-use loops, agent frameworks like LangChain/LlamaIndex at runtime). Petri doesn't use LLMs at runtime.
- Streaming / event-sourcing / message bus architectures.
- API design, rate limiting, auth, schema evolution for public interfaces.
- Kubernetes, deployment, scaling. Single binary.
- Database sharding, read replicas, connection pooling.

**Adjacent / sometimes relevant:**
- Determinism and rewindability in distributed systems — the same concerns apply to saveable simulations, though the context differs.
- State machine design — Petri's multi-phase actions are explicitly state machines (see flow-diagrams.md).
- Observability patterns — ActionLog is a per-character observability mechanism, not system-wide tracing, but some of the same discipline (bounded retention, semantic events) applies.
- ECS patterns from game-engine literature — Petri has chosen "simple flags over ECS" but may evolve.

## A note on "agent" as a word

The word "agent" is overloaded across the library. In Petri:
- A **character** is a simulated agent with needs, memory, preferences, and autonomous decision-making — but no LLM is involved.
- A **Claude Code agent** (subagent_type in the Task tool) is a dev-time AI helper for exploration, review, or planning.
- A **runtime LLM agent** (LangChain-style, tool-using) is neither of these and does not exist in Petri.

When library articles discuss "agents," we should silently classify which kind and judge relevance accordingly. Articles on sub-agent patterns within coding-assistant tools are relevant to our development process. Articles on "agentic AI" that describe tool-using LLMs in production are about a different class of system than Petri.

## Is the structure serving its goals?

From this analysis — which is based on docs, code layout, and file sizes, not runtime observation or development friction — some things can be assessed with reasonable confidence and others cannot.

**What looks well-served:**
- **Emergent behavior from rules.** The intent/execution split with the four-bucket decision model produces observable emergent behavior (helping, foraging, vessel management, construction coordination) without requiring per-behavior orchestration. That the behaviors exist and are documented as intended is evidence that the structure supports the goal.
- **Isomorphism.** The cognitive-loop-to-file-layout mapping is explicitly documented and enforced through values like "Follow the Existing Shape." When new concepts arrive (helping, constructs, bundles), the structure has absorbed them into the existing shape rather than bolted them on.
- **Deterministic + rewindable.** Save/load discipline is heavy, and stateless phase detection for multi-phase actions means in-flight work survives save/load without extra serialization. The headless simulation test harness exists to exercise this.
- **Forward-planning discipline.** `triggered-enhancements.md` tracks deferred work with explicit activation conditions. The 2nd-bug rule and "adding new X" checklists in `architecture.md` exist to absorb future additions without structural drift.

**Pressure points visible:**
- **Growing single files.** `order_execution.go` (1,950 LOC), `apply_actions.go` (1,894 LOC), `intent.go` (1,685 LOC) are sizable. Test counterparts are larger still (`order_execution_test.go` at 6,540 LOC). `triggered-enhancements.md` already anticipates splitting `architecture.md` past ~1,000 lines and flags "Need-evaluator split from intent.go" as triggered when a 5th stat type arrives.
- **Decision-layer coupling.** The `continueIntent` rules require four touchpoint updates when adding a new target field. The doc acknowledges this explicitly. More phases like relationships and events will test whether that coupling stays manageable.
- **Scoring generality.** The "Future: Unified Item-Seeking" note in `architecture.md` says food-scoring and item-scoring diverge by context. Construction scoring unified them partway. Preference-weighted scoring across all item acquisition is deferred work with a clear path.
- **Registry extensibility not yet stressed.** The "Simple Flags over ECS" choice is documented as evolvable. Whether it holds through relationship and event systems is an open question the codebase hasn't answered yet.

**What can't be determined from this analysis:**
- Runtime performance under larger character counts (16+ is flagged as a parallelization trigger).
- Whether the codebase feels good to work in day-to-day — development friction, onboarding cost, time-to-change for a given type of feature.
- Whether emergent behavior matches the PM's intent without playing the game.
- Whether the deferred items in `triggered-enhancements.md` are being tracked on a reasonable timeline, or silently accumulating.

**Overall read.** The structure is serving current goals well and has a visible, disciplined plan for the next layer of expansion. The strongest signal is that past phases (construction, helping, gardening) landed without destabilizing the earlier architecture — new mechanics joined the existing shape rather than fragmenting it. The weakest signal is file-size growth: the architecture anticipates splits but hasn't executed them yet, and future phases will test whether the triggers fire at the right moment.

## Where Petri might grow technically

If future phases (see VISION.txt, especially "Creatures, Theft, and Destruction" and relationship systems) significantly expand simulated population or interaction richness, some of the deferred items in `triggered-enhancements.md` become relevant:
- Parallel intent calculation (currently single-threaded)
- Category formalization (moving toward ECS-lite)
- Character event/signal system (richer inter-character signaling)
- Terrain-aware interaction (discovery from looking at terrain, not just items)

None of these introduce LLM integration, networking, or database architecture. The technical shape stays the same.
