---
name: draft-proposals
description: "Draft improvement proposals from the mapping doc. Each proposal addresses one or more opportunities by ID and cites both library and cross-project sources. Walks the PM through proposals one at a time for approve / drop / revise, capturing the PM's action and reasoning in the proposals doc as data for future calibration. End of the analysis chain."
user-invocable: true
argument-hint: Path to the mapping doc with Step 2 mapping already filled in — e.g., petri/mapping-2026-04-25.md
---

## Inputs

- Mapping doc with Step 1 + Step 2 + cross-cutting themes (`<project>/mapping-YYYY-MM-DD.md`) — required.
- Methodology and structure docs — for value-grounding context.
- Catalog and cross-project docs — to expand source citations. See "Cross-project pattern sourcing" in `meta-claude/CLAUDE.md` for what counts and how to use them.

## Output

Write to `<project-name>/proposals.md`. This is a *living document* that gets overwritten or updated each pass, not a dated snapshot. Carry a "Last updated: YYYY-MM-DD" line at the top so the most recent revision is visible at a glance. Past versions live in git history.

### Per-proposal schema

```
id: integer
addresses: list<opportunity_id>
change: string (concrete action)
value: string (specific pain addressed or value created, grounded in project reality — not just pattern-fit)
sources: list<source_reference>  (library + cross-project)
effort: enum (low | low-medium | medium | high)
fit: string (pattern / shape alignment with existing project practice — separate from value)
trade_off: string
recommendation: string (when to do it, what to defer, alternatives)
sequence_position: integer | null
pm_review_action: enum (approve | drop | revise) — captured during the per-proposal review checkpoint
pm_review_reasoning: string (one-line note from the PM's response; signal for future calibration)
```

`value` and `fit` are distinct. `value` answers *why this change is worth making for this project*. `fit` answers *why this shape of change is the right mechanism*. **A proposal where `value` can't be articulated beyond "the library names this pattern" is speculative — surface it as such rather than writing it up.**

### Output structure

```markdown
# <Project> Improvement Proposals

*Last updated: YYYY-MM-DD*

Source: <methodology-doc> + <structure-doc> + <mapping-doc>. Opportunity IDs reference the inventory in the mapping doc.

---

## Strategy framing

(brief — name the cross-cutting themes from the mapping doc and state which are advanced in this wave, which are deferred. Note any themes where multiple proposals interlock as one designed system.)

---

## Active proposals

### Proposal 1: <title>

**Addresses:** Opportunity X (...).

**Change.** ...

**Value.** ...

**Sources.**
- *Library:* ...
- *Cross-project:* ...

**Effort.** Low | low-medium | medium | high.

**Fit.** ...

**Trade-off.** ...

**Recommendation.** ...

**PM review:** approve | drop | revise. *<one-line reasoning captured during the per-proposal review checkpoint>*

(repeat for each active proposal)

---

## Deferred

Each entry names the opportunity and the **re-open trigger** (what condition warrants revisiting).

- **Opportunity X (title).** Reason for deferral. **Re-open trigger:** <specific condition>.

---

## Recommended sequence

| Order | Proposal | Reason |
|---|---|---|
| 1 | P1 — <title> | <reason> |
| ... | ... | ... |

(at the end, briefly note any cluster of proposals that should be designed together even if landed sequentially)
```

## Process

1. **Read mapping doc.** Confirm Step 1 + Step 2 + cross-cutting themes are present. If not, halt.

2. **Strategy framing first.** Open the doc with a short framing section that names the cross-cutting themes (from the mapping doc), states which the proposal wave advances, and which it defers. If the PM has named scope constraints in chat (e.g., "defer Theme C entirely"), reflect them here.

3. **Draft proposals.** For each opportunity (or opportunity cluster when proposals span multiple):
   - Identify the concrete change.
   - Articulate `value` grounded in project reality. **If `value` reduces to "the library names this pattern," flag the proposal as speculative or drop it.** Apply the Disciplines below.
   - Cite sources from the mapping (library + cross-project).
   - Estimate effort, fit, trade-offs.
   - Write a recommendation that includes when to act, alternatives, and what to defer.

4. **Per-proposal review checkpoint with the PM.** After each proposal is drafted, surface it in chat as a self-contained block:

   > **Proposal N — Title** *(addresses opportunity X, Y)*
   > Change: ... Value: ... Sources: ... Effort: ... Recommendation: ...
   >
   > Approve / drop / revise?

   Wait for the PM's response. Capture the action (approve / drop / revise) and a one-line reason in the proposal's `pm_review_action` and `pm_review_reasoning` fields. **This is a hard data-gathering checkpoint** — what the PM approves, drops, or asks to revise (and the reason) is the calibration signal for evolving this skill toward less PM input over time. Don't batch — proposals are heavy enough that per-proposal review is faster for the PM than reviewing everything at once.

5. **Sort active vs. deferred.** Active = approve + revise (after revision). Drop → not in the doc as active; if the underlying opportunity still has merit, move to Deferred with a re-open trigger.

6. **Write the Deferred section with explicit triggers.** Every deferred opportunity needs a re-open condition: "after N decisions accumulate," "if behavior X recurs," "after prerequisite proposal lands and is used through one full cycle." Without triggers, deferred work decays into permanently-shelved.

7. **Recommended sequence table.** A small table at the end with first-pass ordering and reasoning per row. Note any proposals that should be designed together.

8. **Save the output.**

## Disciplines

These rules are load-bearing for proposal authoring. The skill is the home for them.

- **Speculative proposals get flagged or dropped.** If `value` only works as "library says so," do not write it up. Either ground it in project reality, surface the gap in chat for PM discussion, or move the corresponding opportunity to the Deferred section with a re-open trigger. (Memory: `feedback_value_grounding`.)

- **Gap handling — escalate to library intake before proposing project-side.** When an opportunity has no library match, the first question is whether the gap actually points at a missing library entry. If a quick search surfaces an AI-collaboration resource that meets the library inclusion criteria, flag it for the next intake scan rather than proposing a project-side fix immediately — the library legitimately grows this way. Only when no such resource exists does the gap fall through to project-side handling.

- **Don't reflexively propose a skill for a project-side gap.** Project-side gaps get one of: skill (only when recurring judgment justifies it), reference doc / checklist (one-off decisions that might recur years later), or nothing (decisions that may never recur). Default down the list, not up. (Memory: `feedback_gap_skill_default`. CLAUDE.md: "Skills vs. reference docs — decision rule.")

- **Analysis observes; proposals prescribe.** Don't reintroduce framing or opinion absent from the upstream analysis docs. Conversely, don't repeat the analysis's observations as if they were proposal recommendations. (Memory: `feedback_analysis_vs_proposal`.)

- **Genericize library references** in the proposal doc — write topics, not titles. The proposal doc is the most published-feeling output; reduce article-title coupling so the doc ages well as the library evolves. (CLAUDE.md.)

- **Effort estimates are rough.** Use enum values (low / low-medium / medium / high). Don't try to estimate hours.

- **Recommendation column matters.** "Do this fourth, after P3" or "defer until 50 decisions accumulate" is the load-bearing content for the PM. Vague recommendations are a signal that the proposal isn't ready.

## Handoff

After all per-proposal review checkpoints complete and the doc is saved, surface a summary in chat covering:
- Active count, dropped count, revised count, deferred count.
- Recommended starting point (top of sequence).
- Speculative proposals flagged or dropped during drafting, with reasons.
- Notable PM redirects captured during the review checkpoints (signal for future calibration).

Proposals saved at `<project-name>/proposals.md`. End of analysis chain. PM reviews implementation status and acts on proposals manually.

## Scope — what this skill does NOT do

- Does not implement proposals (the PM does that, possibly project-side).
- Does not modify the target project.
- Does not promote anything to the catalog.
- Does not auto-trigger cross-project analyses or cross-pass synthesis (those are separate, deferred mechanisms).
