# Petri Improvement Proposals

*Last updated: 2026-04-25*

*Produced 2026-04-18. Step 3.3–6 output of the Petri proposal workflow.*

Inputs: `methodology-2026-04-18.md`, `structure-2026-04-18.md`, `../claude-code-source-library-catalog.md`, Step 3.1–3.2 chat checkpoints, memory strategy discussion.

Each proposal includes: the concrete change, value (the specific pain addressed or value created, grounded in project reality), sources, rough effort, fit (pattern alignment), trade-offs, and recommendation. `value` and `fit` are deliberately distinct: `value` answers *why this change is worth making*, `fit` answers *why this shape of change is the right mechanism*.

---

## Memory strategy (framing for memory-related proposals)

Memory-related proposals (P1, P3) proceed from a settled strategy rather than ad-hoc per-skill adoption. The strategy has four elements:

1. **Deliberately develop capture discipline.** Current memory coverage is under-captured, not over-captured. Expand the write scope beyond to include the six retro categories and beyond when signals are real, while still holding a meaningful threshold (not every moment).
2. **Memory + Values.md as provenance architecture.** Values.md carries rules at a high level. Each value references memory IDs that supported its formation. Raw memories are pulled into context on demand, not always loaded. This keeps Values.md clean without losing the evidence.
3. **Framing discipline for writes.** Memories tend to land as absolute rules when the feedback was nuanced. Writes need framing guidance: describe the signal, scope the rule, prefer descriptive to prescriptive.
4. **Let transcript scan recede naturally.** The current `/retro` transcript-scan mechanism works and catches retrospective trends that moment-of-capture cannot. As memory coverage improves and 1M context makes intra-session scan moot, scan recedes without being removed. Inter-session coverage is where memory earns its keep.

This strategy shapes Proposals 1 and 3. Proposals 2, 4, 5, 6 are independent.

---

## Tier 1 — Quick wins

### Proposal 1: Add memory-framing discipline to project CLAUDE.md

**Addresses:** Memory strategy element 3 (framing discipline) — failure mode within Opportunity 2 (memory system is largely unleveraged).
**Change:** Add a subsection to Petri's `CLAUDE.md` Collaboration section covering memory-write discipline:
- Describe the signal, don't prohibit universally. *"User preferred X for Y"* over *"NEVER do Z."*
- Name the scope. When the preference applies, when it doesn't.
- Prefer descriptive to prescriptive. *"User tends to X in these situations"* over *"must always X."*
- Don't generalize from one correction. One signal is evidence for a contextual preference, not a universal law.
- When a memory candidate feels absolute, the fix is to capture it with more context — not to write the absolute rule, and not to skip the memory.

**Value:** Fixes a real current failure mode — memories that land as absolute rules require coaching to reframe, consuming session time on memory hygiene rather than the work. Guidance at write time prevents the coaching loop.
**Sources:** Claude Code Memory docs (four categories and intended use); user observation of the absolute-rules failure mode.
**Effort:** Low. A paragraph in CLAUDE.md's Collaboration section.
**Fit:** CLAUDE.md already carries collaboration norms; this is one more norm in a familiar place.
**Trade-off:** One more rule to read at session start; minor.
**Recommendation:** Do first. Lowest-effort change in the set, addresses an active pain, and lands before broader memory adoption so new memories get shaped correctly.

---

### Proposal 2: Add subagent delegation to `/implement-feature` Readiness Criteria (single touchpoint first)

**Addresses:** Opportunity 5 (subagent delegation is underused).
**Change:**
- **`/implement-feature` Readiness Criteria**: move the pattern-alignment pass (grepping for function signatures, caller sites, existing tests) to an Explore subagent. Main skill receives a structured readiness report.
- Second touchpoint (`/refine-feature` Step 2.7 execution-path tracing) deferred until the first invocation contract is proven. Trigger to add: when readiness-check subagent is reliably returning complete findings without re-exploration.

**Value:** Keeps exploration artifacts (grep outputs, file reads, false leads) out of main decision context. The PM has anecdotally observed the specific failure modes this addresses — subsequent reasoning pattern-matching to grep-specific examples even when not relevant, duplicated exploration within a single skill invocation, and occasional sense of main-skill context feeling cluttered during tracing/readiness steps. Isolation via subagent puts the main skill's decision-making on clean inputs (structured findings only) rather than a mix of findings and exploration artifacts.
**Sources:** Claude Code Subagents docs (isolated context, `/agents` structure); Claudefa.st Sub-Agent Best Practices (sequential dispatch, invocation quality); PopularAiTools Workflow Patterns (Operator pattern).
**Effort:** Low–medium. One skill step gets rewritten; subagent prompt spec needs to be tight so findings come back complete.
**Fit:** Matches Petri's existing grep-first discipline but executes it in a context that doesn't pollute the main skill.
**Trade-off:** Each subagent invocation adds latency; incomplete prompts mean incomplete findings. Claudefa.st identifies invocation quality as the primary failure mode for subagents — "most sub-agent failures are invocation failures, not execution failures." Starting with one touchpoint lets us iterate on the invocation contract (scope, file references, success criteria) before wiring a second.
**Recommendation:** Do alongside the other Tier 1 and Tier 3 quick wins. No dependencies on other proposals.

*Updated 2026-04-20 by petri-claudecode-agent: Scoped to single touchpoint first. Rationale: Claudefa.st Sub-Agent Best Practices identifies invocation quality as the dominant risk. Starting with one touchpoint proves the contract works before committing to two; `/implement-feature` readiness is more concrete than `/refine-feature` tracing and more likely to produce complete findings on the first iteration.*

---

### ~~Proposal 10: Wire Monitor into `/fix-bug` and `/test-world` for real-time test observation~~ → Deferred to `triggered-enhancements.md`

*Added 2026-04-19. Moved to Tier 4 on 2026-04-20 by petri-claudecode-agent.*

**Rationale for deferral:** The proposal itself acknowledged unclear value — test output in Petri is short and well-structured, and reporting friction is low. Rather than implementing speculatively, this belongs as a triggered enhancement with a clear signal: "if test-output reporting becomes a repeated friction point during `/fix-bug` flows (3+ instances where verbose output or multi-failure sequences cause rework), wire Monitor into the test-observation step." If the trigger never fires, the overhead was never worth adding.

---

## Tier 2 — Structural process additions

### Proposal 3: Memory capture discipline and Values.md example-extraction

**Addresses:** Opportunity 2 (memory system is largely unleveraged) — primary driver — plus memory strategy elements 1 and 2 (capture discipline; Values.md provenance).
**Change:** Two movements, sequenced so capture expands before management changes.

**(a) Expand capture scope.**
- Skills add explicit capture-trigger language at common signal points — when a correction is received, when a multi-step approach is changed mid-work, when a user preference is explicitly stated.
- The signal set expands beyond the six existing `/retro` categories to include discoveries, validations, explicit preferences, and patterns noticed in the moment. Threshold rule: if the signal is specific, scoped, and likely to matter in a future session, capture it.
- `/retro` Step 1 still reads both memory and transcript (scan stays; see strategy element 4). New memory signals supplement what transcript scan finds.

**(b) Values.md example-extraction rule + retro-time provenance.**
- **Core rule:** Values entries are rules. Supporting examples and evidence live in referenced memory files, not inline in Values.md. This directly addresses the stated bloat problem — examples accumulating in Values.md that then require periodic paring.
- **Provenance links form at retro-synthesis time**, not at capture time. When `/retro` synthesis promotes a raw memory to support a value (or forms a new value from accumulated signal), the value entry gets a `Sources:` line referencing the memory files that supported its formation. This aligns with how values actually form — in holistic reflection, not in the moment.
- **Retro synthesis step** runs a four-phase consolidation pass (aligned with Claudefa.st Auto Dream model): (1) orient by reviewing current memory structure, (2) gather signal by searching transcripts for corrections, explicit preferences, and recurring patterns — targeted searches, not exhaustive reads, (3) consolidate by merging overlapping entries, converting relative dates to absolute, and removing contradicted facts, (4) prune and update MEMORY.md index. Values.md provenance links are formed or updated during phase 3.
- Raw memories that are promoted to values stay referenced but aren't always loaded in context — retrieved on demand when a value is being revisited or questioned.

~~**(c) PreCompact hook as safety net.** — Removed.~~

**Value:** Addresses the under-capture pain (memories are sparse where they'd be valuable) while preventing the follow-on pain of Values.md becoming unwieldy with accumulated examples. The example-extraction rule solves the bloat problem directly. The retro-time provenance preserves reasoning access — per Fowler Context Anchoring, "reasoning degrades faster than decisions; the what survives, the why does not." Without externalized reasoning, values become disconnected rules that can't be questioned or scoped.
**Sources:** Claude Code Memory docs (categories, storage structure); Fowler Context Anchoring (externalize durable state, retrievable on demand — specifically: reasoning must be preserved externally because it fades faster than decisions); Claudefa.st Auto Dream (four-phase consolidation model — orient, gather signal, consolidate, prune/index — as the concrete design for the `/retro` synthesis step); existing Petri patterns (Values.md structure, `/retro`'s synthesis step).
**Effort:** Low–medium. The capture-discipline expansion is cross-skill guidance. The example-extraction rule is a one-time migration + discipline going forward. `/retro` gets provenance-linking as part of its existing synthesis step.
**Fit:** Matches Values.md's existing role as a high-level rules document; adds provenance without adding specific examples to the doc body. Aligns with the externalize-durable-decisions principle.
**Trade-off:** Provenance links depend on `/retro` running. If retros don't happen regularly, raw memories pile up without being linked to values. Mitigated by the expanded `/retro` synthesis step and periodic memory review.
**Recommendation:** Do after Proposal 1 lands so memory-framing guidance exists before capture expands. Second memory-related change to execute.

*Updated 2026-04-20 by petri-claudecode-agent: (1) Removed PreCompact hook — per Fowler Harness Engineering framework, this is an inferential control masquerading as computational. The hook fires deterministically but whether it produces useful, well-framed output is still model-dependent — the same enforcement gap it's meant to close. If capture gaps exist during rapid flows, the fix is better explicit triggers (part a), not a safety net with the same failure mode. (2) Simplified provenance to example-extraction rule + retro-time linking. PM observation that values form in reflection, not in the moment, means provenance links belong at retro-synthesis time. The full memory-ID infrastructure was reframed after reading Fowler Context Anchoring — provenance is justified even at low volume (reasoning loss is the failure mode, not scale), but implementation should match how values actually form. (3) Scoped from "provenance architecture" to "example-extraction" — solves the stated Values.md bloat problem directly without over-building.*

---

### Proposal 4: Consolidate cross-skill rules into CLAUDE.md; restructure with two explicit zones; clean up stale content

**Addresses:** Opportunities 1 (skill prose redundancy and tighter-context-era discipline) and 7 (cross-session working state).
**Change:**
- **Audit each SKILL.md** using two criteria: (1) cross-cutting rules restated across multiple skills — candidates include context discipline (grep-first, targeted reads), evidence-first discipline, sibling-flow scan, Values.md reference, DD-N convention, `[TEST] → [DOCS] → [RETRO]` unit rule, and the memory-framing discipline landing from Proposal 1; (2) discoverable content — anything Claude can find by reading the codebase (directory structure, tech stack, module layout, existing conventions visible in code) does not belong in CLAUDE.md or SKILL.md files. Apply the heuristic: *"can Claude find this by reading the code? If yes, delete it."* This second criterion extends the audit scope beyond consolidation into genuine deletion.
- **Restructure CLAUDE.md into two explicit zones:**
  - **Zone 1 — always-loaded system knowledge:** project overview, architecture pointers, collaboration norms, the consolidated cross-skill rules. Stable content that doesn't change between sessions.
  - **Zone 2 — current working context:** primarily *links* to the active artifacts — `step-spec.md`, the current phase design doc, roadmap status. Doesn't duplicate those documents; just points to them so a reader lands in the right working context immediately.
- **Each SKILL.md** replaces restated cross-cutting rules with a reference to CLAUDE.md rather than restating.
- **Effort frontmatter pass:** assess each skill for whether an `effort` level should be declared in frontmatter. High-cost skills (architectural review, full retros, complex implementation passes) should declare appropriate effort; lightweight skills should not over-specify. This is a one-line change per skill but benefits from being done deliberately across all of them at once rather than piecemeal.
- **Cleanup pass** as part of the same work: remove or rework stale content in CLAUDE.md, tighten structure, consolidate where the current doc has drifted.

**Value:** Eight SKILL.md files with duplicated cross-cutting rules create real drift risk — a rule updated in one skill may lag in others. Consolidating to one source of truth reduces drift and lets tighter-context-era rules evolve in one place as models and context windows continue to change. The two-zone structure gives the reader (PM and Claude) a clear separation between stable context and working state without introducing a new file. The discoverable-content criterion extends cleanup beyond consolidation — content Claude can infer from reading the codebase actively degrades context quality by adding noise, not just redundancy. More precisely: the accumulated prose across SKILL.md files is a form of **context rot** — each redundant rule and stale convention is a small drag on reasoning quality per session that compounds across the full skill graph. The restructure eliminates the rot source, not just the duplication.
**Sources:** Fowler Context Anchoring (CLAUDE.md is already the canonical externalized durable rules and working decisions — no separate file earns its own existence); thedecipherist MDD (two-zone concept applied *within* CLAUDE.md rather than via a separate file); Stack Builders Beyond AGENTS.md (context engineering as infrastructure — the file already exists by Anthropic convention); Addy Osmani Stop Using /init (discoverable-content deletion criterion and cost evidence — +20% tokens, -2-3% performance from redundant context); Trensee Advanced Patterns (CLAUDE.md = policy, Skills = procedures, Hooks = enforcement — reinforces zone separation logic); get-shit-done (context rot framing — quality degrades as accumulated context pollution fills the window; the structured markdown loading approach demonstrates the alternative).
**Effort:** Medium. Audit 8 SKILL.md files for duplicated rules, extract to CLAUDE.md, update skill bodies to reference rather than restate. Restructure CLAUDE.md into two-zone layout. Cleanup pass.
**Fit:** Matches Anthropic's convention of CLAUDE.md as the AI-facing project instruction file. The two-zone structure adds internal clarity without fighting the convention or creating a new file to maintain. Zone 2's link-don't-duplicate approach keeps the spec/design docs as the sources of truth for working context.
**Trade-off:** CLAUDE.md grows in one dimension (cross-skill rules added) and shrinks in another (skills reference rather than restate). Net effect is manageable if discipline is held on what's actually cross-cutting vs. skill-specific.
**Recommendation:** Can run in parallel with Proposal 3. Both are structural Tier 2 work with independent benefits.

---

### Proposal 5: Add schema-validation hook for `/test-world`-generated save files

**Addresses:** Opportunity 3 (enforcement relies on model discipline rather than structural mechanisms) — one specific structural-enforcement case.
**Change:**
- Register a `PostToolUse` hook matching `Write` operations on `~/.petri/worlds/*/state.json`. Hook command runs a small validator checking the written JSON against `state.go` schema. Failure surfaces inline with the mismatch location.
- The validator ideally derives from `state.go` so it doesn't drift from the schema source of truth.
- `/test-world`'s caller-requirements prose stays as-is; the hook is a safety net, not a replacement.

**Value:** When `/test-world` produces malformed save files, current detection happens at game load time — later in the loop and with worse error context. A hook catches errors at write time, immediately surfacing the mismatch. Value scales with the rate at which `/test-world` currently produces malformed outputs.
**Sources:** Claude Code Hooks docs (PostToolUse event, matcher system, protocol); thedecipherist Starter Kit (example hook set); `/test-world`'s current caller-requirements discipline (remains primary).
**Effort:** Medium. Validator is the bigger cost than hook wiring. Maintenance lives in keeping validator in sync with `state.go`.
**Fit:** `/test-world` already operates on a schema contract; the hook enforces the contract structurally rather than via prose discipline.
**Trade-off:** New dependency (validator). Mitigated by deriving from `state.go` or keeping it minimal.
**Recommendation:** Do after memory-related proposals (P1, P3) are comfortable. Establishes the hook pattern for future additions.

---

### Proposal 8: 2nd-bug-rule enforcement via CLAUDE.md feedforward rule + visible recap

**Addresses:** Opportunity 3 (enforcement relies on model discipline rather than structural mechanisms) — specifically the 2nd-bug rule, which is confirmed to be getting violated in practice.
**Change:** Two pieces that work together — a feedforward rule (shapes behavior before it happens) and a feedback signal (makes state visible after each fix).

**(a) CLAUDE.md-level rule (always loaded).** Add to Petri's CLAUDE.md Collaboration section:

> During testing, before writing any code change in response to a reported problem, explicitly name whether this is the first or a subsequent fix in the current testing session on the current step. If subsequent, halt for design review before proceeding.

Applies to any code change during testing, independent of whether `/fix-bug` is formally invoked.

**(b) Explicit visible recap at fix-close.** Fix-close is defined as: the moment when either (i) the user confirms the fix worked, or (ii) the user reports a new/different problem on the same step (which implicitly closes the prior fix). Whichever happens first. At that moment:
- State visibly: *"Fix #N on <current step>."*
- If N ≥ 2, halt for design review before any further fix work.

The visible statement is the enforcement mechanism — it forces the counting that context drift erodes, and puts the human in the feedback loop at the detection point.

~~**(c) `/fix-bug` restructure with memory counter.** — Removed.~~
~~**(d) Memory counter infrastructure.** — Removed.~~

**Value:** The 2nd-bug rule is confirmed to be violated in practice — the PM almost always has to trigger the correct response manually. The failure mode is context drift during rapid testing-and-fixing flows. The CLAUDE.md rule is a feedforward guide — it shapes behavior proactively by requiring explicit counting before any fix. The visible recap is a feedback sensor — it makes state observable to the human. Together they put the human in the loop at the right moment. The deeper intent is the **Ratchet Principle**: every bug that slips past the 2nd-bug threshold is a failure that should become a permanent design constraint. The rule isn't just enforcement — it's the mechanism that ensures failures get encoded into lasting guardrails rather than patched and forgotten.
**Sources:** User observation of the failure mode (rule violations during rapid back-and-forth fix flows); Fowler Harness Engineering (feedforward guides shape behavior before generation; feedback sensors detect after — neither alone suffices, both together close the loop; behavioral correctness remains the hardest to enforce structurally and still relies primarily on human review); Addy Osmani *Agent Harness Engineering* (Ratchet Principle — treat each failure as a permanent design constraint, encode it into rules or hooks rather than dismissing it as one-off).
**Effort:** Low. One paragraph in CLAUDE.md + one behavioral addition to `/fix-bug`'s close step.
**Fit:** CLAUDE.md-level rule fits the file's role as cross-skill collaboration norms. The visible recap aligns with Harness Engineering's feedback-sensor pattern.
**Trade-off:** Still relies on model discipline to state the count — but the act of stating it visibly creates human oversight at the detection point, which is where behavioral correctness enforcement works. If this proves insufficient (the explicit naming itself gets skipped), the escalation path is a computational control (e.g., a hook tracking file-write operations during testing phases), not a memory counter.
**Recommendation:** Do after P1 lands so the memory-framing discipline is in place. Addresses a confirmed current pain, so prioritize earlier than P3 or P4 in sequence.

*Updated 2026-04-20 by petri-claudecode-agent: Simplified from four parts to two. Removed memory counter infrastructure (parts b, d) and `/fix-bug` restructure (part c). Rationale per Fowler Harness Engineering framework: the memory counter doesn't achieve computational enforcement — the model must still decide to read it, which is the same inferential step that fails under context drift. The counter adds machinery without breaking the failure-mode loop. The simplified version uses the CLAUDE.md rule as feedforward (proactive shaping) and the visible recap as feedback (state made observable to the human). Behavioral correctness — the hardest enforcement category per the framework — relies on human review at the detection point, which is exactly what the visible recap enables. Escalation trigger: if the simplified version fails (explicit naming gets skipped 3+ times), evaluate computational controls (hooks) rather than adding memory infrastructure.*

---

### Proposal 9: Reshape `/go-code-reviewer` as integration + Go-specific technical architecture reviewer; auto-trigger at phase design and implementation

**Addresses:** Opportunity 5 (subagent delegation underused) adjacent, plus the design-and-triggering gap surfaced in the original Open Questions — `/go-code-reviewer` rarely fires manually, and when it does fire, its per-change scope misses the integration and technical-architecture concerns the PM cares about most.
**Change:** Reshape the existing agent and give it two automatic trigger points.

**(a) Reframe the review lens.** Primary focus becomes integration + Go-specific technical architecture — how the change fits with surrounding code, whether it follows the existing shape, package/file boundaries, type-design choices, idiomatic Go patterns, and cross-file coherence. The agent's current focus areas (performance antipatterns, error-handling gaps, maintainability) remain but drop below architectural/integration review in the report order. The reviewer should also explicitly check for two Go-specific AI failure modes: **frequency bias** (Claude defaults to older Go patterns because training data skews toward legacy code — flag where a modern equivalent exists for the Go version in `go.mod`) and **version-awareness** (idioms appropriate to Petri's current Go release, not a prior one). These checks are distinct from architectural review and belong at the bottom of the report under a dedicated "Go idiom currency" heading.

**(b) Two automatic trigger points.**
- **Design-time:** auto-fires at `/new-phase` completion, after the phase design doc is drafted. Input: phase design doc plus surrounding code. Reviews whether the proposed architecture — entity types, package boundaries, data relationships, interface designs — will compose well in Go, extend cleanly for future phases, and avoid performance pitfalls. This is the higher-value trigger: at phase level, you have enough structural specificity for a Go-informed opinion, and catching a bad abstraction here is cheapest.
- **Implementation-time:** auto-fires during `/implement-feature` as a penultimate step (before `/update-docs`). Input: the actual change plus surrounding code. Reviews whether the implementation integrates cleanly.

**(c) Severity-tagged findings.**
- **Architectural** (requires changing the design, not just the implementation) → halt for PM discussion before the skill that invoked the review proceeds.
- **Cleanup** (local maintainability, mechanical style, minor error-handling polish) → advisory in the report; flow continues.

**(d) Consistent reviewer, different artifacts.** Same agent file, same system prompt, same lens. Only the input artifacts differ between invocations.

**Value:** Two problems the PM confirmed in discussion: (1) the skill almost never gets triggered because manual invocation is the failure mode, and (2) its per-change scope misses the integration-level concerns that matter more to the PM than mechanical checks. Auto-triggering at two natural points in the flow removes the manual-invocation failure; reshaping the lens addresses the scope mismatch.

The design-time trigger at `/new-phase` is where this earns the most value. Per Fowler (LLMs and Building Abstractions), AI acceleration removes friction that used to catch poor abstraction decisions during writing — "the learning that occurs through hands-on coding, where abstractions reveal their limitations, cannot be outsourced." When a phase proposes new entity types, package boundaries, and data relationships, those abstractions are in the discovery/stabilization phase where they're most fragile. A Go-specific reviewer at this moment answers a question `/refine-feature` cannot: "will this type design compose well in Go specifically — do these interfaces scale, will these package boundaries create import cycles, does this data shape have performance implications?"

The implementation-time trigger catches integration issues that emerge when abstractions meet code — a different and complementary question.

**Sources:** Claude Code Subagents docs (read-only reviewer pattern, isolated context, `/agents` structure); Claudefa.st Sub-Agent Best Practices (auto-trigger on task completion, invocation quality); Fowler *Conversation — LLMs and Building Abstractions* (essential vs. accidental complexity — LLMs handle the accidental but the essential creative work of discovering abstractions requires explicit review; AI acceleration increases the need for architectural review, not decreases it); Addy Osmani *The Factory Model* (bottleneck inversion — verification now takes more time than generation; reinforces why early-phase design-time review is the highest-leverage investment, not post-hoc code review); JetBrains *Write Modern Go Code with Junie and Claude Code* (frequency bias and data-cutoff failure modes in AI-assisted Go development — the specific pathologies the "Go idiom currency" checks address); VoltAgent `awesome-claude-code-subagents` (reference collection including a `golang-pro` language-specialist template); Petri's existing `/go-code-reviewer` agent definition (the technical-checks content that stays as secondary focus).
**Effort:** Medium. Rewrite the agent's system prompt for the new lens; wire two auto-trigger points into `/new-phase` and `/implement-feature`; add severity tagging to findings; decide whether to rename the file.
**Fit:** Extends the existing agent rather than replacing it. Two trigger points match the community-consensus auto-trigger-on-task-completion pattern for reviewer subagents. Language-specialist review is an established subagent pattern (see VoltAgent collection's `golang-pro`). The `/new-phase` trigger specifically addresses the Fowler argument: when AI reduces the friction of writing code, the explicit architectural review that friction used to provide must be added back deliberately.
**Trade-off:** Subagent invocation adds latency at two points in the flow. The `/new-phase` trigger reviews a design doc (prose + structural decisions) rather than code — the reviewer must be prompted to evaluate proposed structures against Go idioms and existing codebase patterns, not just review written code. This requires a well-crafted invocation contract per Claudefa.st guidance.
**Recommendation:** Do after P4 (CLAUDE.md restructure) settles, since this proposal modifies `/implement-feature` and P4 may also touch skill context references. Two small implementation decisions to make during execution: (a) rename the agent file to reflect the new focus or keep the name, (b) whether the dual-invocation modes share one prompt or diverge into mode-specific prompts.

*Updated 2026-04-20 by petri-claudecode-agent: Moved design-time trigger from `/refine-feature` (step-spec level) to `/new-phase` (phase-design-doc level). Rationale: at step-spec level, you're too granular for architectural review but too abstract for code-level review — awkward middle ground. At phase level, you have real architecture proposals (entity types, package boundaries, data relationships) with enough structural specificity for a Go-informed opinion. This is also where catching bad abstractions is cheapest — adjust the design doc, not rewrite implemented code. The Fowler LLMs and Abstractions argument directly supports this: AI acceleration removes the friction that used to surface poor abstraction decisions during writing, so explicit architectural review must be added back at the point where abstractions are proposed. `/new-phase` is that point.*

---

## Tier 3 — Larger structural

### Proposal 6: Add a four-touchpoint checklist for decision-layer target-field changes

**Addresses:** Opportunity 9 (decision-layer coupling in `continueIntent`).
**Change:**
- Add "Adding a new Intent target field" section to `architecture.md`'s existing "Adding New X" checklists. Enumerate the four touchpoints: (1) `CalculateIntent` continuation gate, (2) target-recalculation chain in `continueIntent`, (3) arrival-detection block, (4) final-return-intent preservation.
- Cross-reference from `/implement-feature` when the task involves intent-layer changes.
- Does not refactor the underlying coupling. Makes silent violations less likely.

**Value:** `architecture.md` already names this coupling as fragile but doesn't operationalize it. A missing touchpoint currently means the intent silently falls through to full re-evaluation each tick — a hard-to-diagnose bug class. A checklist at the point of change prevents the silent-miss mode.
**Sources:** Petri `architecture.md`'s existing "Adding New X" pattern (this slots in as a sibling); Fowler LLMs and Abstractions (when abstractions need formalization). No direct library pattern for multi-touchpoint coupling; handled project-side per gap-handling rules.
**Effort:** Very low. One new section in `architecture.md` and one small cross-reference addition in `/implement-feature`.
**Fit:** Adds to the existing "Adding New X" pattern with zero new concept.
**Trade-off:** Documentation adds maintenance weight. Negligible.
**Recommendation:** Do preemptively — low cost and the next intent-layer phase (relationships, events) is named upcoming work where this will apply.

---

### Proposal 7: Add split-trigger conditions for `order_execution.go` and `apply_actions.go` to `triggered-enhancements.md`

**Addresses:** Opportunity 8 (growing single files).
**Change:** Add two entries to `triggered-enhancements.md` mirroring the existing patterns for `intent.go` and `architecture.md` split triggers:
- `order_execution.go` (currently 1,950 LOC): trigger candidates — line-count threshold, a new order-type addition that would push past threshold, or a concept cluster matured enough to warrant its own file (e.g., construction-specific logic becoming its own module).
- `apply_actions.go` (currently 1,894 LOC): same structure — line-count threshold, new action-type handler that would push past, or specific action category grown into a coherent cluster worth extracting.

Exact thresholds and specific trigger language to be drafted in the style of existing entries; PM sets threshold based on comfort with file-navigation cost.

**Value:** Both files are comparable in scale to `intent.go`, which already has a documented split trigger. Without trigger conditions, they keep growing silently and the `triggered-enhancements.md` discipline fails to fire at the right moment. Closing this gap catches file-size pressure before it becomes navigation pain.
**Sources:** Petri's existing `triggered-enhancements.md` pattern (direct precedent for the `architecture.md` split and `intent.go` split entries).
**Effort:** Very low. Two entries drafted to match the existing format.
**Fit:** Extends existing pattern; zero new machinery or concept.
**Trade-off:** Small documentation maintenance cost. Negligible.
**Recommendation:** Do alongside Proposal 6. Both are small structural documentation additions that extend already-established patterns.

---

## Tier 4 — Defer or skip

Items surfaced in mapping but not worth action now. Each has a trigger that would re-open the decision.

- **Proposal 10 (Monitor for test observation).** Test output in Petri is short and well-structured; reporting friction is low. Trigger: if test-output reporting becomes a repeated friction point during `/fix-bug` flows (3+ instances where verbose output or multi-failure sequences cause rework), wire Monitor into the test-observation step.
- **Opportunity 4 (documentation fan-out consistency check).** `/update-docs` is mature and the audience-discipline table is working. Post-update consistency checks are a nice-to-have with no specific current pain. Revisit if a drift-caused bug surfaces or if a phase touches enough docs that manual consistency becomes expensive.
- **Opportunity 6 (evaluating emergent behavior).** Web search for AI-collaboration-practice content on simulation-game emergent-behavior evaluation found only adjacent material. Judgment-driven activity that PM plays out in practice. Trigger to revisit: if headless tests pass but the PM observes unexpected emergent behavior during play and can't identify which mechanic is producing it, apply Hamel's diagnostic framing (Hamel *Revenge of the Data Scientist*) — "look at the data" means constructing deliberate play scenarios that isolate specific behaviors, not open-ended sessions or dashboard-watching. The failure modes to avoid: inferring from headless simulation stats that don't reflect real play conditions, or evaluating by instinct alone without targeted observable preconditions (which `/test-world` already enables for specific mechanics).
- **Opportunity 10 (scoring generality).** Already captured in `triggered-enhancements.md` as "Generic scored-item search helper" with a concrete trigger (5th preference-scored search function added). Library entries validate the direction. No action needed — existing trigger will fire at the right moment.
- **Opportunity 11 (registry extensibility / "Simple Flags over ECS").** Open question that can't be answered until relationships and event systems are implemented. Signal to revisit: when a new capability requires state that doesn't fit cleanly as a boolean flag or single-purpose property struct.

---

## Implementation timeline

Proposals are grouped by when they land relative to the game development roadmap. Steps 12-13 (Display Polish + Tuning Pass) are the remaining construction-phase work; a new game phase follows.

### Before Step 12 (process improvement pass)

All six proposals land before Step 12 begins. Steps 12 and 13 exercise them: P3's expanded capture runs through two real retros, P8 gets tested during real [TEST] checkpoints, P4's restructured CLAUDE.md is loaded for two full implementation cycles.

| Order | Proposal | Effort |
|---|---|---|
| 1 | Memory-framing discipline in CLAUDE.md (P1) | Very low |
| 2 | Four-touchpoint checklist (P6) + split-trigger conditions (P7) | Very low |
| 3 | Consolidate cross-skill rules + restructure CLAUDE.md (P4) | Medium |
| 4 | 2nd-bug-rule enforcement (P8) | Low |
| 5 | Memory capture + Values.md example-extraction (P3) | Low–medium |

### After Step 13, before `/new-phase`

Ready for the next phase's first invocations of `/new-phase` and `/implement-feature`.

| Order | Proposal | Effort |
|---|---|---|
| 6 | Reshape `/go-code-reviewer` + dual auto-trigger at `/new-phase` and `/implement-feature` (P9) | Medium |
| 7 | Subagent delegation — single touchpoint in `/implement-feature` (P2) | Low–medium |
| 8 | Schema-validation hook for `/test-world` (P5) | Medium |

### Deferred to triggered-enhancements.md

P10 (Monitor for test observation) — fires only if test-output reporting becomes repeated friction (3+ instances).

*Timeline finalized 2026-04-20 by petri-claudecode-agent: Grouped by game development milestones rather than abstract priority order. "Now" bucket validated by PM — process work lands before Step 12 so it's exercised during two real implementation cycles before the next phase where retros are heavier and process changes are higher-stakes.*

---

