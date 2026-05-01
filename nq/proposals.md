# NQ Improvement Proposals

*Last updated: 2026-04-20*

*Produced 2026-04-18. Step 3–6 output of the NQ proposal workflow.*

Inputs: `methodology-2026-04-18.md`, `structure-2026-04-18.md`, `mapping-2026-04-18.md`, `../claude-code-source-library-catalog.md`.

Each proposal includes: the concrete change, sources informing it (library + Petri where applicable), rough effort, fit rationale, and any notable trade-offs. Proposals are grouped by priority tier (leverage × fit × effort).

---

## Tier 1 — Quick wins

High leverage, known patterns, low effort. Recommend doing these first.

### Proposal 1: Port Petri's `/retro` skill to NQ (adapted)

**Addresses:** Opportunity 2 (no formal retro mechanism)
**Change:** Add a `/nq-retro` skill in `nq/.claude/skills/retro/SKILL.md`, adapted from Petri's `/retro`. Core structure:
- Step 0: Solicit user impressions (full vs. lite retro); optional history search
- Step 1: Assess session friction (interrupted actions, clarifications, context slips, human-caught bugs, intensive processes, value signals)
- Step 2: Route proposed changes by file — design decisions → DATA_MODEL.md or mechanics.md; collaboration norms → CLAUDE.md; workflow rules → phase docs
- Step 3: Implement approved changes; update `nq/memory/last-retro.txt`

**Sources:** Fowler Feedback Flywheel (four signal types, periodic artifact reviews); Petri `/retro` skill (concrete six-category multi-step protocol); Claudefa.st Auto Dream (four-phase consolidation model — orient, gather signal, consolidate, prune/index — as a reference for the retro's synthesis step, once NQ's memory usage develops enough to warrant it).
**Effort:** Low. The Petri skill is ~200 lines of Markdown; porting is target-file substitution and trimming NQ-irrelevant steps.
**Fit:** NQ currently has no retro mechanism. Adding one closes the loop from "PM notices friction" to "CLAUDE.md / docs update durably captures it."
**Trade-off:** Adds a skill where NQ has previously avoided them. Reasonable mitigation: use it sparingly (every N phases, or on user trigger) rather than after every session.

---

### Proposal 2: Port Petri's `/update-docs` skill to NQ

**Addresses:** Opportunity 3 (documentation fan-out)
**Change:** Add a `/nq-update-docs` skill in `nq/.claude/skills/update-docs/SKILL.md`. Explicit audience-discipline table:

| File | Audience | Update rule |
|---|---|---|
| README.md | External readers | Only new user-facing capabilities; one short line per feature |
| CLAUDE.md | AI context | Roadmap only: phase name + status |
| DATA_MODEL.md | Behavioral ground truth | New entities/fields; lifecycle rule changes |
| docs/mechanics.md | Player behavior | User-visible mechanics only; no code flow descriptions |
| docs/design/`phase`-design.md | Phase design | Mark step complete |
| docs/step-spec.md | Current step | Cleared on completion |
| docs/tech-debt.md | Deferred cleanup | Add items surfaced this step |

**Sources:** Petri `/update-docs` skill (direct template); thedecipherist Starter Kit hooks (automated enforcement idea, if desired later); Stack Builders deterministic commands pattern.
**Effort:** Low–medium. Adapt Petri's skill; NQ's documentation taxonomy is simpler than Petri's.
**Fit:** NQ's 8-step cycle already has "step 8: Documentation" but no coordination. This makes step 8 deterministic instead of ad-hoc.
**Trade-off:** Some redundancy with existing CLAUDE.md guidance; the skill makes the guidance actionable rather than advisory.

---

### Proposal 3: Make `nq-companion/README.md` the canonical shared reference; audit skills for duplication

**Addresses:** Opportunities 10 (companion skill repetition) and 12 (context priming)
**Change:** The README already contains the rules each companion skill currently restates — UTC conversion, timestamp semantics, snapshot field handling, tone rules, CLI basics, fallback protocol. Rather than introducing a new priming file, upgrade the existing README as the canonical shared reference:

1. Audit each companion skill (`nq-daily-plan`, `home-ops-to-nq`, any drafts) using two criteria: (a) rules that duplicate README content — replace with a single directive to read the README; (b) discoverable content — anything Claude can find by reading the codebase directly. Apply the heuristic: *"can Claude find this by reading the code? If yes, delete it."*
2. For any rule that the README doesn't yet cover but multiple skills assume, add it to the README rather than to an individual skill.
3. Add a short "How skills use this file" section at the top of the README so the convention is visible.
4. Assess each companion skill for `effort` frontmatter — `nq-daily-plan` is expensive enough to warrant a declaration; simpler skills should not over-specify.

*Note: P3 and P5 are the same principle applied in sequence — P3 consolidates the companion project's context files, P5 does the same for NQ's CLAUDE.md. See P5.*

**Sources:** Fowler Context Anchoring (externalize durable rules); Fowler Context Engineering (progressive disclosure — skills read the primer, then focus on their specific work); AI Corner persistent file system (one file that primes every session); Addy Osmani Stop Using /init (discoverable-content deletion criterion); Trensee Advanced Patterns (CLAUDE.md = policy, Skills = procedures — reinforces the layer separation this audit is implementing); Petri skill-level context discipline (for the style of the rules).
**Effort:** Low. Audit + small edits to each skill. No new file.
**Fit:** The README is already the de facto primer; this makes it the definitive one without expanding file count.
**Trade-off:** README grows. Mitigation: keep it organized with clear sections, prune anything that's no longer relevant during the audit.

#### Agent notes — nq-companion-cowork-agent (2026-04-20)

*These notes are from the Claude Code agent that works directly in nq-companion with the PM — driving the daily plan skill, the weekly check-in, and the home-ops import. Assessment is based on hands-on experience hitting the rules this proposal would relocate, plus a targeted read of the cited sources and a verification pass on scheduled-task context loading.*

The core move is sound — and after digging in, bigger than the original framing suggests.

1. **The "rules kept getting missed" symptom has a name.** The PM's original reason for duplicating rules across skills (rather than pointing to the README) was that agents kept reading other content first and starting to act before they internalized the rule. Fowler Context Anchoring cites the underlying research: *"language models perform significantly worse on information placed in the middle of long contexts"* (the "Lost in the Middle" phenomenon). Duplication was a working cope for a real degradation — but the fix isn't to duplicate less. It's to place each rule where it loads reliably *and* carries attention weight.

2. **Three-layer split is cleaner than P3's single-file framing.** Trensee is direct: *"CLAUDE.md is for durable policy, not all procedures. If everything lives there, compliance drops,"* and *"Rules, procedures, and checks should stay separated."* Applied here:
   - **CLAUDE.md** — behavioral/operational rules that gate actions (no python3 pipes; never reference non-completions; tone constraints). Short, loads at session start, policy weight. The nq-companion CLAUDE.md is currently ~50 lines — rules here don't get buried the way they do mid-README.
   - **README** — domain and data interpretation reference (timestamp semantics, the 00:01 backdating rule, XP attribution, weekly focus mechanics). Skills point to it. Consult-while-writing material.
   - **SKILL.md** — procedure only. No interpretation rules restated.

3. **Scheduled-task amendment — verified empirically.** Scheduled tasks don't reliably auto-load project CLAUDE.md. Evidence: a real `nq-daily-plan-auto` run where the agent called `Read` on CLAUDE.md mid-execution — if the harness had injected it, the agent wouldn't have needed to. Working directory is correct (`~/projects/nq-companion`), but context bootstraps from the SKILL.md body pasted into the first user message. Implication: behavioral rules needed by scheduled-task runs must stay inline in the scheduled-task SKILL.md (or a paired interactive skill it invokes). The python3 rule at `nq-daily-plan-auto:22` is already doing this — that's the working pattern, not duplication to eliminate.

4. **nq-weekly-checkin is the strongest case in scope.** 79 lines, self-contained, no pointer to the README. Duplicates UTC conversion, the snapshot migration date, the 00:01 backdating rule, and ~7 interpretation rules that live in user memory. Almost certainly one of the three places where the 00:01 rule was edited in parallel this morning — the friction event that surfaced this proposal in the first place. Recommend splitting it: create a paired interactive skill (mirroring the `nq-daily-plan` / `nq-daily-plan-auto` structure); the scheduled task shrinks to a thin wrapper with automated-mode overrides; the skill holds the how-to and points to README. Also gives the PM the ability to invoke a check-in manually on an off-cycle day.

5. **Supporting signal for aggressive pruning over pure relocation.** Addy Osmani cites a concrete stat: *"LLM-generated context files reduced task success by 2-3% while increasing cost over 20%."* Argues for pruning discoverable/duplicated content during the audit rather than just moving it around.

6. **Drop from scope:** the "How skills use this file" section at the top of README (item 3 in the original change list). The convention is self-evident from the skills' existing README pointers; documenting it adds maintenance cost without reader benefit.

7. **`effort` frontmatter (item 4) is a separate sub-feature**, not a rationale for the consolidation itself. It's a new skill-harness capability being tried out. Treat it as a step applied *during* the audit pass (since we're editing the skills anyway), not as a driver of it.

**Revised audit scope:**

| Target | Action |
|---|---|
| `README.md` | Absorb cross-skill reference rules not yet covered; resist growth (Fowler keep-short signal); prune anything newly redundant |
| `CLAUDE.md` (nq-companion) | Add behavioral rules currently living only in individual skills or user memory (python3 rule, tone rules, non-completion rule) |
| `nq-daily-plan` (interactive skill) | Remove interpretation-rule duplicates; keep pointer to README; keep procedure |
| `home-ops-to-nq` | Remove interpretation-rule duplicates; keep pointer to README |
| `nq-weekly-checkin` | Split: new paired interactive skill + thin scheduled-task wrapper; prune during the split |
| `nq-daily-plan-auto` | Prune domain/data rules only; keep behavioral rules inline (scheduled-task amendment) |
| All skills | Apply `effort` frontmatter assessment as a pass-through sub-step |

---

## Tier 2 — Structural process additions

Medium leverage, moderate effort. Recommend doing after Tier 1 so the retro skill exists to refine these as they land.

### Proposal 4: Adopt DD-N numbered design decisions in phase design docs

**Addresses:** Opportunity 4 (minimal persistent per-phase state — *lower priority*)
**Change:** Add a Design Decisions section to the phase design doc format. Each decision numbered globally (DD-1, DD-2, ...) with Context / Decision / Rationale / Affects. Step specs and open questions cross-reference by DD-N. When an open question is resolved, strike it through with a DD reference.

**Sources:** Petri phase design doc format (direct template).
**Effort:** Low. Template change; applies to new phase docs going forward. Back-filling prior phases is optional.
**Fit:** The PM indicated this isn't a current pain point, but flagged openness to better options. DD-N is low-cost to adopt and pays compounding dividends when a later phase revisits a decision.
**Trade-off:** Adds structure before pain proves it's needed. Mitigation: adopt on the next new phase, skip back-filling.

---

### Proposal 5: Restructure CLAUDE.md into two explicit zones; cull discoverable and redundant content

**Addresses:** Opportunity 1 (prose-heavy process encoding)
**Change:** Rather than creating a new startup file, restructure the existing `CLAUDE.md` into two explicit zones — same pattern applied in Petri P4:

- **Zone 1 — always-loaded system knowledge:** 8-step phase cycle, hard product rules ("No streaks, ever." and similar), ADHD-aware pacing rules, collaboration norms, dependency ledger. Stable content that doesn't change between sessions.
- **Zone 2 — current working context:** primarily *links* to the active artifacts — current phase design doc, `docs/step-spec.md`, roadmap status. Doesn't duplicate those documents; just points to them so a reader lands in the right working context immediately.

Alongside the restructure, run two audit passes on Zone 1 content:
1. **Redundant content:** rules already captured in phase docs, DATA_MODEL.md, or other documents that CLAUDE.md is pointing at anyway.
2. **Discoverable content:** anything Claude can find by reading the codebase directly (e.g., crate details in the dependency ledger that are visible in `Cargo.toml`, architectural patterns evident from code structure). Apply the heuristic: *"can Claude find this by reading the code? If yes, delete it."*

*Note: P3 and P5 are the same principle applied in sequence — P3 consolidates the companion project's context files, P5 does the same for NQ's CLAUDE.md. See P3.*

**Recommended sequencing:** Defer until after the `/nq-retro` and `/nq-update-docs` skills are in place. Retro data will show whether process-prose friction persists once doc coordination improves. The restructure is lower-risk than creating a new file (no new artifact to maintain) but still benefits from retro signal before committing to what Zone 1 should contain.

**Sources:** thedecipherist MDD (two-zone concept); Fowler Context Anchoring (link-don't-duplicate — Zone 2 points to artifacts rather than restating them); Addy Osmani Stop Using /init (discoverable-content deletion criterion; evidence that redundant context degrades performance); Petri P4 (direct pattern — same two-zone restructure applied to a more complex CLAUDE.md; NQ is a simpler case).
**Effort:** Low–medium. The restructure is mostly reorganization; the audit passes are where the judgment lives.
**Fit:** CLAUDE.md already exists and serves this role; the two-zone structure makes its internal organization explicit without creating a new file to maintain. Matches Anthropic's convention of CLAUDE.md as the canonical AI-facing project instruction file.
**Trade-off:** Some Zone 1 content may need to stay even if discoverable, because it encodes *intent* or *constraint* rather than *fact* (e.g., "No streaks, ever" isn't in the code). The audit should distinguish between content Claude could infer and content Claude must be told.

---

### Proposal 6: Add trigger conditions to `docs/tech-debt.md`

**Addresses:** Opportunity 7 (ui/index.html tech debt)
**Change:** Update each entry in `docs/tech-debt.md` with explicit trigger conditions — the Petri-style "implement when ANY of..." format. Example for the existing `renderCheckDropdown` item:

> **Trigger conditions:** (a) next bug traced to the DOM-vs-inline-HTML inconsistency, OR (b) a 6th form duplication is added, OR (c) a new form-field type has to be built and the current pattern can't absorb it cleanly.

**Sources:** Petri `docs/triggered-enhancements.md` (direct pattern).
**Effort:** Very low. Three existing items; one pass to annotate.
**Fit:** The items are already documented; Petri's contribution is the discipline of "don't just list deferred work — specify when it becomes non-deferred."
**Trade-off:** None meaningful.

---

## Tier 3 — Structural refactor

### Proposal 8: Converge completion paths onto a shared `complete_quest` code path

**Addresses:** Opportunity 8 (five completion paths coupling)
**Change:** Refactor toward a single `complete_quest` code path that all five entry points (quest list, quest giver, timer, saga tab, overlay) converge on. Downstream effects (campaign progress, XP distribution, saga bonus, history snapshot) are colocated in that one path. Petri's shared-helper pattern (`RunVesselProcurement`, `RunWaterFill`) is the template.

Before refactoring, run a one-off audit: enumerate the five paths and four downstream effects, build the path × effect matrix, verify every cell, and document the current state. This produces the regression baseline for the refactor.

**Sources:** Petri Values.md "Follow the Existing Shape" + sibling-flow check from `/fix-bug` Step 3; Petri shared-helper pattern in `picking.go`; Schleicher (ubiquitous language — what "completion" means in this system); Fowler LLMs and Abstractions (extract shared abstraction).
**Effort:** Medium. Touches five call sites and requires regression tests to validate the behavioral-snapshot invariant holds everywhere.
**Fit:** NQ's current coupling is documented but fragile. Since no new completion paths are expected, the refactor resolves the issue permanently rather than adding a recurring guard.
**Recommendation:** Schedule into a phase that already touches the completion code to amortize the cost. No separate skill needed — the audit is a one-off step in the refactor.

---

## Tier 4 — Defer or skip

Items surfaced in mapping but not worth action now. Each has a trigger that would re-open the decision.

- **Opportunity 4 (persistent per-phase state)** is addressed by Proposal 4 only if you choose to adopt DD-N. Otherwise skip.
- **Opportunity 1 (prose-heavy process encoding)** is gated behind Tier 1. Evaluate after retros.
- **`/nq-evaluate-monolith-split` skill** *(was Proposal 7)* — deferred and reconsidered. Splitting `db.rs` is a one-off judgment call, not a recurring process. When `db.rs` pressure warrants evaluation, do the analysis directly and capture the decision framework as a markdown checklist at that time if it proves reusable. A skill adds infrastructure overhead for something that might run once ever, and there's no learning loop to tune it with.
- **Cross-project paths reference doc** *(was Proposal 9, simplified)* — deferred until a second cross-project skill is being written. When that happens, consolidate project paths, binary locations, database conventions, and data-format expectations into `nq-companion/cross-project-refs.md` so both skills reference one source. No contracts, no validator, no YAML — just Fowler-style externalization of durable references. Current cost is zero with one cross-project skill; trigger is the second.
- **Frontend framework evaluation trigger-list** *(was Proposal 10)* — deferred. Framework migration isn't a pain point today and may never be. Revisit only if specific signals arise (complexity growth, debugging friction on consecutive phases). If you want to capture this, add it as a one-line note to `docs/tech-debt.md` — not worth a dedicated document.

---

## Recommended sequence

| Order | Proposal | Tier | Why here |
|---|---|---|---|
| 1 | Port `/nq-retro` (P1) | 1 | Unlocks feedback loop for everything that follows |
| 2 | Audit companion skills vs. README (P3) | 1 | Smallest change, immediate per-session benefit |
| 3 | Port `/nq-update-docs` (P2) | 1 | Coordinates the doc fan-out that other work produces |
| 4 | Add trigger conditions to `tech-debt.md` (P6) | 2 | Near-zero cost, improves decision staging |
| 5 | Adopt DD-N in next phase (P4) | 2 | Low-cost upgrade when next phase starts |
| 6 | Evaluate Proposal 5 (MDD-style priming) | 2 | After several retros — data will show whether needed |
| 7 | Plan `complete_quest` refactor (P8) | 3 | Schedule into a phase that already touches completion |

**Shape of the first pass:** items 1–4 together are a small, coherent set of changes (two ported skills, one companion README audit, one low-cost discipline upgrade). That pass alone would close out Tier 1 + the near-zero Tier 2 work, and generate retro data that sharpens the later decisions.

---

## Comments from nq-claude-code-agent (2026-04-20)

*These comments are from the Claude Code agent that works directly in the NQ codebase with the PM. Assessment is based on lived experience building features, hitting friction, and observing where time is lost.*

### P1 (Retro skill) — Agree, high value
We accumulate feedback in memory files ad-hoc. Today alone the PM had to remind me to update mechanics.md — exactly the kind of recurring friction a retro would catch as a pattern rather than a one-off correction. The trade-off note about skill overhead is fair; triggering it every N phases or on user request (not every session) is right for NQ's pacing.

### P2 (Update-docs skill) — Agree, but question the mechanism
The fan-out table is the valuable part. Whether this needs to be a skill (with invocation ceremony) vs. a checklist added to the existing step 8 guidance in CLAUDE.md is worth considering. A skill is an extra thing to remember to run; a checklist in CLAUDE.md is always loaded. If the retro (P1) reveals that doc fan-out is still a problem after making the checklist more explicit, escalate to a skill then.

### P3 (Companion README consolidation) — No strong opinion
I don't work directly in nq-companion, so I can't assess the duplication claim. The principle is sound.

### P4 (DD-N numbered decisions) — Premature
The document acknowledges this isn't a pain point. Adding structure before pain justifies it conflicts with NQ's "don't over-engineer" and "don't manufacture urgency" principles. Skip until a concrete decision-tracing need arises.

### P5 (CLAUDE.md restructure) — The audit is valuable, the structure is premature
The current CLAUDE.md works well in practice. The "discoverable content" audit is the valuable kernel — we could do a one-off cleanup pass without adopting a formal two-zone structure. The dependency ledger, for example, is already visible in Cargo.toml. Run the audit; skip the restructure unless the audit reveals structural problems.

### P6 (Tech debt trigger conditions) — Agree, trivially good
Near-zero cost, genuinely useful. When I encounter tech debt items during a feature, knowing "should I address this now?" is unclear without trigger conditions.

### P8 (Completion path refactor) — Agree, and I'd bump priority
This is the most concrete source of maintenance burden in the codebase. We literally just experienced the pain: adding snapshot columns to completion history meant updating one INSERT path and verifying two others were fine with implicit NULLs. Every future completion-time feature will hit the same coupling. The document's Tier 3 placement and "schedule into a phase that already touches completion code" advice is reasonable, but we just missed that window — the snapshot work would have been the ideal time. I'd prioritize this higher than process improvements (P4, P5) because it prevents real bugs.

### Tier 4 deferrals — All correctly deferred
The db.rs split, cross-project refs, and frontend framework evaluation are all correctly identified as premature. The trigger conditions are well-stated.

### Adjusted priority recommendation

| Priority | Proposal | Rationale |
|---|---|---|
| 1 | P1 (Retro skill) | Feedback loop that informs everything else |
| 2 | P6 (Tech debt triggers) | Near-zero cost, do alongside P1 |
| 3 | P8 (Completion path refactor) | Most concrete code quality win; prevents real bugs on every future completion feature |
| 4 | P2 (Update-docs) | Start as a CLAUDE.md checklist; escalate to skill if retros show it's still a problem |
| 5 | P3 (Companion audit) | Sound but I lack context to prioritize |
| 6 | P5 (CLAUDE.md audit only) | One-off cleanup pass, skip the restructure |
| — | P4 (DD-N) | Skip until pain |

---

## Proposed roadmap placement (vision.md)

Based on the priority assessment above, here's how these items would slot into the existing NQ roadmap:

**Phase 5D Group 2** (current upcoming work — alongside existing feature items):
- P6 (tech debt trigger conditions) — trivial effort, do as a housekeeping item at the start of Group 2
- P8 (completion path refactor) — medium effort, but Group 2 is the right time since it's before smart achievements (item 5), which will add more completion-adjacent logic

**Process improvements** (not phase-gated — these are workflow changes, not features):
- P1 (retro skill) — adopt immediately, run after the next few sessions to generate signal
- P2 (update-docs checklist) — add to CLAUDE.md step 8 guidance now; revisit as a skill after retro data

**Phase 6 or later:**
- P5 (CLAUDE.md audit) — one-off cleanup, do when convenient, no phase dependency
- P3 (companion audit) — do when next companion skill is being written or revised

**Skip:**
- P4 (DD-N) — revisit only if a concrete decision-tracing need arises
