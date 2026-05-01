# Meta-Claude Improvement Proposals

*Last updated: 2026-04-26*

*Drafted 2026-04-25. Revised 2026-04-26 after PM feedback (re-ordered P2/P3; folded `analysis-process.md` retirement into the skills proposal; switched decision-record shape to a single log file; reshaped P5 to use an end-of-scan consolidation pass with a 2-instance threshold; open questions resolved).*

---

## Implementation status (2026-04-26)

- **P1 — Codify artifact-map alternative:** ✅ Done. Variant subsection added to `analysis-process.md` Phase 1b; canonical example referenced.
- **P2 — Encode phases as chained skills; retire `analysis-process.md`:** ✅ Done. `meta-claude/CLAUDE.md` created; five SKILL.md files created at `meta-claude/.claude/skills/`; five lessons-learned migrated to auto-memory; `library-intake-scan` SKILL.md and catalog footer updated to remove dependency on `analysis-process.md`; Content-triage and Fluff disciplines added to CLAUDE.md; fluff sweep applied across all six skills (2026-04-26); long-term automation direction added under Purpose. PM read-through review completed 2026-04-26 with substantive refinements across all five skills (see "Refinements from P2 review" below). `analysis-process.md` deleted 2026-04-26.
- **P3 — Retire intake doc as stub:** ✅ Done 2026-04-26. `claude-code-source-library.md` replaced with a stub redirecting to `intake-decisions.md` and the catalog; preserved (not deleted) so future readers find the redirect.
- **P4 — Interactive intake + decision capture:** ✅ Done 2026-04-26. `library-intake-scan` SKILL.md reshaped: candidates surfaced one-at-a-time in chat (promote / drop / defer / discuss), de-duplication and date-range checks read `intake-decisions.md`, bulk-report flow retired, decision-log schema covers drop / promote / defer / replace / platform-update / no-op. New file `meta-claude/intake-decisions.md` created with header. `intake-reviews/` preserved as historical record; no new files written there.
- **P5 — End-of-scan memory consolidation:** ⬜ Not started.
- **P6 — Cross-pass synthesis (emergent):** ⬜ Deferred until ≥3 analysis passes exist.
- **Tier 4 (Theme C, confidence ratings, structural enforcement):** ⬜ Deferred per re-open triggers.

### Next action

**P5** — derive PM-preference memory via end-of-scan consolidation. Will accumulate signal once `intake-decisions.md` has ~5–10 real decisions; until then, this is design-only work.

### Refinements from P2 review (2026-04-26)

The PM read-through of all five skills produced substantive shape changes beyond transcription. Captured here as the canonical record so a future skim of the proposals doc shows what the chain looks like post-review.

**Analysis-chain checkpoints are designed as data-gathering moments.** What the PM keeps / edits / removes / approves / drops / revises and the reasoning is captured in the artifact itself, parallel to how P4's `intake-decisions.md` will work for intake. The shape mirrors P5's eventual consolidation pattern: per-event decision data is the calibration signal that lets the chain progressively need less PM input. CLAUDE.md's "Checkpoint frequency" rule was rewritten to reflect this. Three designed checkpoints in the chain — opportunity-inventory, map-opportunities (strategy), draft-proposals (per-proposal review).

**Per-skill changes:**

- **`/analyze-methodology`** — kept "don't read code" tight; added skim-first rule for >300-line docs; dropped stale CLAUDE.md cross-cutting pointer.
- **`/analyze-structure`** — added auto-combine-with-methodology behavior for the artifact-map variant (appends `## Artifact map` to the existing methodology doc when it exists for today); added bounded code-reading guidance (targeted, skim-first for large files, stop-when condition); dropped stale CLAUDE.md cross-cutting pointer; renamed sibling-project terminology to cross-project throughout.
- **`/opportunity-inventory`** — reframed from "prioritize and recommend focus" to "name the lenses to bring to the library scan." Prioritization is a downstream concern; this skill confirms coverage. Schema dropped `priority`, added `source` paraphrase. Process is now one-at-a-time per candidate (confirm / edit / remove / add) with review actions captured in the doc itself as the data layer.
- **`/map-opportunities`** — solutioning stripped (no proposed handlings for gaps, no skill-vs-doc judgments, no ranking — those moved to `/draft-proposals`). Strategy checkpoint reframed as data-gathering (PM strategy decisions captured in the doc per theme). Sibling renamed to cross-project. The over-engineered library 3-class memory entry was rewritten as a calibration entry warning against multi-class taxonomies.
- **`/draft-proposals`** — added per-proposal review checkpoint (one-at-a-time, approve / drop / revise, with reason captured in `pm_review_action` and `pm_review_reasoning` fields). New consolidated Disciplines section homes the moved skills-vs-doc rule, value-grounding, analysis-vs-proposal boundary, genericizing, and effort/recommendation discipline. Tier framing dropped from the Recommended sequence template.
- **CLAUDE.md** — added a "Cross-project pattern sourcing" cross-cutting rule (referenced from `/map-opportunities` and `/draft-proposals`); rewrote "Checkpoint frequency" to capture the data-gathering framing and three-checkpoint design.

Source: `analysis-2026-04-25.md` (methodology + artifact map) and `mapping-2026-04-25.md` (opportunity inventory and library mapping). Opportunity IDs reference the inventory in the mapping doc.

---

## Strategy framing

**Trajectory.** These proposals are oriented toward an analysis chain that eventually runs unattended. Current PM-as-trigger between skills is scaffolding — useful while the chain is unproven, but the goal is to earn out of it. Read the proposals through that lens: changes that build trust in the chain (clearer outputs, tighter contracts between skills, retros that surface where PM redirects vs. rubber-stamps) are investments toward automation. Adding checkpoints is a cost, not a default.

The 17 opportunities cluster into three cross-cutting themes (named in the mapping doc):

- **Theme A — Skills-as-runnable-workflows.** Encoding the analysis flow and the intake flow as named, chained skills. Affects opportunities 1, 2, 3, 7, 10, 13.
- **Theme B — Feedback loops that close.** Turning intake decisions and proposal uptake into durable artifacts that improve the system over time. Affects opportunities 4, 5, 6, 11, 14.
- **Theme C — Cross-project automation infrastructure.** Pushing meta-claude's outputs into target projects and triggering re-analysis automatically. Affects opportunities 10, 12, 13.

This proposal set advances Themes A and B. **Theme C is deferred entirely** at PM direction — the right shape will be clearer after the foundation skills and feedback loops are in use. Re-open trigger named in Tier 4.

The wave shape:
- **Tier 1** removes friction (codify the artifact-map alternative).
- **Tier 2** builds the foundation: encode phases as chained skills (which absorbs `analysis-process.md`), retire the now-pointing-at-nothing intake doc, replace bulk reports with interactive review backed by a decision log, and derive PM-preference memory from accumulated decisions.
- **Tier 3** anticipates emergent needs (cross-pass synthesis when corpus grows).
- **Tier 4** defers Theme C and dependent learning features with explicit re-open conditions.

The Tier 2 proposals interlock — P2, P4, P5 form one designed system. They can land sequentially but should be designed together. P3 (retire intake doc) is small but sequence-locked behind P2 since the stub points to artifacts P2 establishes.

---

## Tier 1 — Quick wins

### Proposal 1: Codify the artifact-map alternative

**Addresses:** Opportunities 3 + 15 (artifact-map alternative as a first-class option).

**Change.** Add a short subsection to Phase 1b of `analysis-process.md` (or, after P2, the relevant SKILL.md) naming the artifact-map variant: when the project has no software stack (a methodology workspace, a curated library, a knowledge base), Phase 1b is replaced with an artifact map covering document inventory by volatility, how artifacts feed each other, what the project is NOT, and where it might grow. Reference `meta-claude/analysis-2026-04-25.md` as the canonical example. Update the workflow-backbone table to read "structure or artifact-map."

**Value.** This pass produced the artifact-map adaptation by improvising. Without codification, the next non-software project analyzed will improvise again, and the format will drift. Trivial cost to fix; ongoing cost to leave undone.

**Sources.** Methodology-internal refinement. No library lift available — the gap is acknowledged in the mapping doc.

**Effort.** Low. ~30 minutes of editing.

**Fit.** The process document already names lessons-driven refinements as the way it improves itself; this is exactly that kind of refinement.

**Trade-off.** If P2 lands first and `analysis-process.md` is retired, the codification target shifts to `/analyze-structure`'s SKILL.md. Either order works; the content is the same.

**Recommendation.** Do now. Single edit, no design work.

---

## Tier 2 — Structural process additions

### Proposal 2: Encode analysis phases as chained skills; retire `analysis-process.md`

**Addresses:** Opportunities 1 (encode phases as skills) and 2 (explicit handoffs). Resolves the open question of where `analysis-process.md`'s content should live.

**Change.** Build a graph of skills, one per phase of the analysis flow:

| Skill | Phase | Inputs | Output artifact |
|---|---|---|---|
| `/analyze-methodology` | 1a | Project directory + collaboration-norms files | `<project>-methodology-YYYY-MM-DD.md` |
| `/analyze-structure` | 1b | Project directory + arch docs + manifests | `<project>-structure-YYYY-MM-DD.md` *or* artifact-map variant |
| `/opportunity-inventory` | 3.1 | Both Phase 1 docs | Inventory section in mapping doc |
| `/map-opportunities` | 3.2 | Inventory + catalog + sibling docs | `<project>-mapping-YYYY-MM-DD.md` |
| `/draft-proposals` | 3.3 | Mapping doc | `<project>-proposals-YYYY-MM-DD.md` |

Each skill names its inputs, produces a single named output, and ends with an explicit handoff line: *"Output saved to `<path>`. Next skill in the chain: `/<next-skill>`. Run when ready to proceed."* The PM remains the trigger between skills.

The structure skill takes a `--variant` flag (`software` | `artifact-map`) to support Proposal 1's branch.

**`analysis-process.md` retirement.** With skills canonical, `analysis-process.md`'s content distributes as follows:

| Current content | New home |
|---|---|
| Phase 1a structure, schema, review gates | `/analyze-methodology` SKILL.md |
| Phase 1b structure, schema, artifact-map variant | `/analyze-structure` SKILL.md |
| Phase 3.1 opportunity inventory schema and persistence rule | `/opportunity-inventory` SKILL.md |
| Phase 3.2 mapping schema and gap-handling | `/map-opportunities` SKILL.md |
| Phase 3.3 proposal schema, tier structure | `/draft-proposals` SKILL.md |
| Phase 2 library curation criteria + scope discipline | `library-intake-scan` SKILL.md (already mostly there) + `meta-claude/CLAUDE.md` for the scope-discipline principles |
| Cross-cutting rules (gap handling, sibling-project sourcing, genericizing references, checkpoint frequency) | `meta-claude/CLAUDE.md` (new file) |
| "Where Claude is opinionated but the PM has review" section | `meta-claude/CLAUDE.md` |
| Lessons learned (currently 5 entries from the NQ pass) | Auto-memory, feedback type — same destination as the PM-preference memory in P5 |
| Automation candidates | This proposal doc has consumed them. Remaining future ideas land in a new `meta-claude/triggered-enhancements.md` (Petri pattern) if any surface. |
| Purpose / when to use / required inputs / outputs produced | Top of `meta-claude/CLAUDE.md` as a short "what this project is" section |

After distribution, `analysis-process.md` is deleted, not stubbed. Discovery happens through `meta-claude/CLAUDE.md` (which any session loads anyway) and the skill list.

**Value.** Today the analysis flow is a prose process document I read and execute by hand. Three problems: (1) prose drifts between passes — the structure-phase artifact-map variant in this analysis was improvised; (2) inputs and outputs of each phase are implicit, which is how I made the "inventory lives only in chat" mistake; (3) re-running a single phase requires reconstructing context manually. Skills fix all three. Retiring `analysis-process.md` consolidates rather than splits content — one canonical home per kind of rule (procedure → skill, principle → CLAUDE.md, lesson → memory, future idea → triggered-enhancements).

**Sources.**
- *Library:* Trensee's five-layer architecture (skills are for procedures, CLAUDE.md is for policy — direct fit for the content-distribution decision); Stack Builders Beyond AGENTS.md (deterministic named commands per phase); PopularAiTools Workflow Patterns (named workflow steps).
- *Sibling:* **Petri's pattern is the direct template.** Petri has no central process document — its process lives in the SKILL.md files, with cross-skill conventions in CLAUDE.md and lessons in retros (which feed back into the skills). Meta-claude is converging on the same shape. Petri's anchor-story propagation (the same intent statement is the handoff at each stage) is the model for the explicit handoff line.

**Effort.** Medium. Five skills to write; SKILL.md content is largely already drafted in `analysis-process.md` so the work is mostly transcription with structure-skill `--variant` design. Plus the new `meta-claude/CLAUDE.md`. Plus the lesson migration to memory.

**Fit.** Strong. Petri's skill graph + CLAUDE.md + memory shape is the proven template.

**Trade-off.** Skills add a maintenance surface, but the alternative (process doc + skills as derivatives) is the surface meta-claude currently has and it has already drifted. Consolidating to one canonical home per content kind is the simpler steady-state. Risk: lessons in memory are less browsable than lessons in a markdown file; mitigated because the migrated lessons are small in number (5 entries) and are precisely the kind of content auto-memory's feedback type was designed for.

**Recommendation.** Do this second (after Tier 1). Verify the skills work end-to-end by running them on a test case before deleting `analysis-process.md`. The migration to memory should happen as part of the same pass — open `analysis-process.md`, copy each lesson into a memory write (feedback type), then delete the file when the migration is complete.

---

### Proposal 3: Retire `claude-code-source-library.md` (the intake doc) as a staging area

**Addresses:** Opportunity 8 (retire the intake doc), with light coupling to opportunity 9 (snapshots as log).

**Change.** Replace the intake doc's contents with a short stub: *"Intake-as-staging was retired 2026-04-XX. Decisions about candidate articles now happen interactively per `library-intake-scan` and are captured in `intake-decisions.md` (see Proposal 4). Promotions go directly to the catalog."* Don't delete — the stub preserves discoverability for any future PM looking for the staging area.

**Value.** PM has confirmed the intake doc isn't being used. Removing it removes one source of "where do I look?" friction. The two-bucket pattern (`To Be Evaluated`, `Latest Updates`) implies a workflow that doesn't exist.

**Sources.** Heuristic borrowed from Addy "Stop Using /init for AGENTS.md" — *if the artifact isn't load-bearing, delete it*. Otherwise self-evident.

**Effort.** Low. ~10 minutes once P4 has named the replacement decision-log file.

**Fit.** Aligned with the methodology's bias toward audience-discipline and avoiding parallel content growth.

**Trade-off.** Sequence-locked behind P2 and P4 — the stub needs to point at the new artifacts. If done before, the stub points at vapor.

**Recommendation.** Do after P4 lands.

---

### Proposal 4: Interactive intake review backed by a single decision log

**Addresses:** Opportunities 4 (decision records), 7 (interactive review), 9 (snapshots as log).

**Change.** Reshape `library-intake-scan` so candidates are surfaced one at a time during a chat session rather than batched into a long report. Each candidate appears with: title, source, paraphrased summary, evaluation against the five criteria, and a recommendation. The PM responds with **promote**, **drop**, **defer** (with reason), or **discuss** (request more context).

Each decision is captured immediately as an entry in a single append-only log file at `meta-claude/intake-decisions.md`. Newest-first order. Schema per entry:

```markdown
## 2026-04-26 · drop · [Article Title](url)
**Source:** domain.com (tier-1) · **Recommended:** drop · **Decided:** drop
Reasoning: Product announcement; library catalogs patterns and methodologies, not tools.
```

Promotion entries include the catalog destination:

```markdown
## 2026-04-26 · promote · [Article Title](url)
**Source:** simonwillison.net (tier-1) · **Recommended:** promote · **Decided:** promote · **Catalog:** Principles and Methodologies
Reasoning: Adds a distinct mental model not in the catalog with strong durability.
```

One sentence of reasoning per article. The decision header (date · decision · title) is grep-able for later analysis (e.g., "show me all drops from claudefa.st").

The `intake-reviews/intake-review-YYYY-MM-DD.md` files retire — their content is fully absorbed into `intake-decisions.md`. The directory is preserved as historical record but no new files are added.

**Value.** Three concrete pains:
1. *Bulk reports are hard to assess.* PM has stated this directly: "the report on articles is really long when it would be easier for me to assess your findings one at a time and respond." Interactive review fixes this.
2. *Decision reasoning is lost.* Today, when an article is dropped, the reasoning is buried in a long doc. Recovering "why did we drop this?" is hard. Structured log entries make it trivial.
3. *Future learning has no input.* P5 and the deferred Opportunity 6 (confidence rating) require structured decision history. Today there is none. This proposal is the data layer.

**Sources.**
- *Library:* Addy Code Agent Orchestra (conductor vs. orchestrator framing — interactive review is a deliberate downshift to conductor mode for a high-judgment step); AI Corner power user guide (Socratic prompting, ask before executing); Fowler Feedback Flywheel (decision records as a signal type fed back into shared artifacts).
- *Sibling:* Petri's `/refine-feature` Step 2a halt and `/fix-bug` evidence-then-agree halt are the explicit "halt for PM confirmation" patterns. The single-log-file shape follows Petri's `triggered-enhancements.md` pattern (one append-only file, dated entries, grep-able decisions) rather than per-decision files.

**Effort.** Medium. Modify `library-intake-scan` SKILL.md to surface one candidate at a time and append to the log. Decide the log schema (above is the proposed default). The interactive flow itself is straightforward — Petri has it working.

**Fit.** Strong. Discussion-first / halt-for-confirmation is the most-used mechanism across Petri's skill set; the bulk-report shape was meta-claude's outlier.

**Trade-off.** A long log file grows over time. At ~50 entries it remains scannable; at ~500 it would need a year-based partitioning (`intake-decisions-2026.md`, `intake-decisions-2027.md`) or similar. Partitioning is deferred until the file actually feels long. The grep-by-decision-header pattern keeps individual lookups cheap regardless of total length.

**Recommendation.** Do third (after P2). Could in principle precede P2, but P2's skill-encoding patterns establish the SKILL.md conventions this builds on.

---

### Proposal 5: Derive PM-preference memory via end-of-scan consolidation

**Addresses:** Opportunity 5 (PM-preference memory).

**Change.** At the end of each intake-scan session, automatically run a consolidation pass that synthesizes auto-memory entries (feedback type) from the decision log. Two-instance threshold: a pattern must be supported by at least two related decisions before it earns a memory entry.

The decision log (`intake-decisions.md`, from P4) is the raw signal layer; memory entries are the synthesized layer. No "provisional" intermediate — single-instance observations stay only in the log until they recur.

**Pass structure** (modeled on Auto Dream's four-phase pattern, adapted for event-triggered operation):

1. **Orient.** Read existing `MEMORY.md` and any meta-claude feedback memory entries to understand current state.
2. **Gather signal.** Scan `intake-decisions.md` for recurring patterns: decisions sharing source domain, content category, or explicit rejection reason. The 2-instance threshold gates which patterns qualify.
3. **Consolidate.**
   - For a qualifying pattern with no existing memory: write a new feedback entry with the pattern, instance count, and supporting decision-header references (date · decision · title).
   - For a qualifying pattern matching an existing entry: update the entry — increment instance count, append the new supporting decision reference, sharpen the rule if needed.
   - For two existing entries that have converged on the same pattern: merge them into one (the "memory files get combined" case).
   - For a contradicting decision (one that breaks an established rule): **flag and pause the affected entry for PM review.** Unlike Auto Dream — which deletes the older contradicted entry on the assumption the newer signal is correct — meta-claude pauses, because PM preferences shifting is genuinely possible and a single contradicting decision shouldn't auto-overwrite an established pattern.
4. **Prune.** Update `MEMORY.md` index if entries were added, merged, or paused. Stay surgical — files unchanged in this pass are left alone.

**Notification, not confirmation.** Each pass announces what was written/updated/merged/paused as a summary in chat at session end. PM doesn't confirm each write, but can override at any time by editing or removing memory entries directly. This is the "automatic" part the PM asked for.

This is also the destination for the lessons migrated from `analysis-process.md` in P2 — they're the same kind of content. Migration writes them in directly as established entries (multiple-instance learnings already supported by the NQ pass).

**Value.** Back-half of the learning loop the PM described: *"so that you can improve your own ability to anticipate what my decisions would be, and eventually need less input from me."* The decision log captures inputs; consolidated memory entries are how scan behavior changes next time. The two-instance threshold prevents the system from writing single-data-point hypotheses into memory where they'd bias future scans before being validated.

**Sources.**
- *Library:* Memory (auto-memory, feedback type) is the platform feature this uses. **Claudefa.st Auto Dream contributes the consolidation pattern** — specifically the four-phase shape (orient → gather signal → consolidate → prune), the merge-overlapping-entries logic, and the surgical-not-rewrite discipline. Two deliberate divergences from Auto Dream: (a) trigger is end-of-scan-session rather than 24h+5sessions, because the decision log is a richer per-event signal source than session transcripts; (b) contradictions pause for review rather than auto-resolving by deletion. The 2-instance threshold is a meta-claude addition, not from Auto Dream.
- *Sibling:* Petri's Values.md is the conceptual analogue at file scope ("derived from observed behavior, not prescribed"). Petri's `/retro` derives changes from accumulated friction; this is the analogous pattern for intake.

**Effort.** Medium. The skill is small but the pattern-detection and merge logic are judgment-dense — what counts as "related" decisions? When does a pattern sharpen vs. fragment into two? Worth implementing the simple version (strict relatedness, append-and-increment merging, pause-on-contradiction) and letting nuance emerge from real cases.

**Fit.** Strong. Auto-memory's feedback type is purpose-built for "stop doing X / keep doing X" patterns; Auto Dream's consolidation pattern is a proven template that the library already trusts.

**Trade-off.** Three real risks:

1. *Loose relatedness inflates entries.* If "related" is interpreted broadly, the consolidation pass produces many low-quality entries. Mitigation: keep relatedness strict at first — same source domain, same content category, or same explicit reason cluster. Loosen later if the strict version misses obvious patterns.
2. *Hamel "rater not validated" failure mode.* The 2-instance threshold gates promotion, which is the validator. But the relatedness judgment itself is ungated. Mitigation: every memory entry carries its supporting decision references, so a stale or miscalibrated entry can be audited against its evidence.
3. *Pause-on-contradiction generates a backlog.* If pauses accumulate, the PM has manual review work. Mitigation: surface pauses in the end-of-scan summary; if pauses go unresolved across multiple sessions, escalate the pattern as needing explicit PM attention.

**Recommendation.** Do fourth, after P4 has accumulated a small handful of decisions (~5-10) so the consolidation pass has something non-trivial to operate on. Implement the simple version first; iterate on the relatedness judgment once real patterns surface. The end-of-scan trigger is automatic and requires no PM ritual — but the pass should write its summary clearly enough that the PM notices when it's drifting.

---

## Tier 3 — Larger structural

### Proposal 6: Cross-pass synthesis as an emergent artifact

**Addresses:** Opportunities 14 (cross-project pattern surfacing) and 16 (cross-pass synthesis).

**Change.** When meta-claude has produced ≥3 full analysis passes, add a `cross-pass-synthesis-YYYY-MM-DD.md` artifact summarizing themes that recur across passes:

- *Themes appearing in ≥2 analyses* — name the theme, list passes, propose what it means (library entry, process change, project-level skill that should be generalized).
- *Patterns proposed in one pass and adopted in another* — sibling-pattern transfer track. Useful for spotting which sibling patterns have stickiness.
- *Open questions raised in one pass and answered in another* — closure tracking.

Not fixed-cadence. Emergent — write one when the corpus has enough mass that themes are visible (currently 2 passes; not yet ready). The next analysis pass should check whether one is overdue and produce it if so.

**Value.** Cross-project pattern surfacing is currently hand-spotted (the lessons-learned section already does some of this). As corpus grows from 2 to 5+ analyses, hand-spotting will miss things. The synthesis artifact makes the spotting structural.

**Sources.**
- *Library:* Fowler Feedback Flywheel; Schleicher Spec-Driven (ubiquitous language).
- *Sibling:* Petri's `/retro` routing rules — recurring patterns get routed to the appropriate destination. Same shape applied across projects rather than within one.

**Effort.** Low when triggered, but trigger is far away. Real cost is zero today.

**Fit.** Strong shape-fit with the existing lessons mechanism, at corpus scope rather than single-project scope.

**Trade-off.** Adds a meta-artifact to maintain. Risk: synthesis is true at write time but stale a year later. Mitigation: synthesis docs are dated and superseded by re-runs, not updated in place.

**Recommendation.** Defer until a 3rd analysis pass completes. Add a check to `/draft-proposals` that asks "is a cross-pass synthesis overdue?" when ≥3 dated proposal docs exist.

---

## Tier 4 — Defer or skip

Explicitly deferred per PM guidance ("we'll be able to evaluate options better after the proposals for the first few items are implemented"). Each has an explicit re-open trigger.

- **Theme C entirely (Opportunities 10, 11, 12, 13).** Push proposals into target projects, track uptake, trigger analysis on project-change signals, periodic re-analysis cadence. **Re-open trigger:** after P2, P4, P5 are implemented and have been used through one full cycle (a new analysis pass or a sustained intake-review run). The cleanest library primitive when this re-opens is **Routines** (cloud Claude Code, schedule/API/GitHub-events triggers); the cleanest sibling-pattern reference for the manual-but-structured form is **Petri's `triggered-enhancements.md`** (deferred work with explicit trigger conditions, lifecycle tracking).

- **Confidence-rated scan recommendations (Opportunity 6).** Requires P4 and P5 in place and validated. **Re-open trigger:** after ~50 decision records exist and derived memory entries have been refined through several PM-feedback cycles. Per Hamel Revenge's "LLM evaluators that haven't been validated themselves" failure mode, the rater must be validated before ratings are trustable.

- **Structural enforcement of "surface in chat" rule (Opportunity 17).** Hooks that block proposal docs introducing framings absent from the conversation. **Re-open trigger:** if a proposal pass produces a doc that introduces a framing the PM didn't see in chat. Today the prose discipline holds; structural enforcement is overhead without a demonstrated failure.

---

## Recommended sequence

| Order | Proposal | Tier | Reason |
|---|---|---|---|
| 1 | P1 — Codify artifact-map alternative | 1 | Single edit; supports P2's structure-skill variant |
| 2 | P2 — Encode phases as chained skills; retire `analysis-process.md` | 2 | Foundation for everything else; verify on a test case |
| 3 | P3 — Retire intake doc as stub | 2 | Sequence-locked behind P4's naming; trivial once unblocked |
| 4 | P4 — Interactive intake + decision log | 2 | Replaces bulk reports; produces data infrastructure for P5 |
| 5 | P5 — Derive PM-preference memory | 2 | Closes the learning loop the PM described |
| 6 | P6 — Cross-pass synthesis | 3 | Emergent; defer until corpus reaches ≥3 analyses |
| — | Theme C re-evaluation | 4 | After P2, P4, P5 used through one full cycle |

The Tier 2 cluster (P2, P4, P5) is the central engine. Worth designing them together — the skills built in P2 are the chassis, the decision-log in P4 is the data layer, P5 is the learning step that consumes the data. Each can land sequentially, but design choices in P2 (output paths, project-and-date conventions) directly affect P4 and P5.

---

## Resolved during drafting

Both initial open questions resolved 2026-04-26:

- **Memory derivation cadence** — automatic, end-of-scan, with a 2-instance threshold before a pattern earns a memory entry. Folded into P5.
- **Skill naming** — bare names (`/analyze-methodology`, `/analyze-structure`, `/opportunity-inventory`, `/map-opportunities`, `/draft-proposals`), consistent with `library-intake-scan` and the Petri/NQ convention.
