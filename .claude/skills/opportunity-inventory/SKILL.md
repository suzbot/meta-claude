---
name: opportunity-inventory
description: "Extract opportunities from the methodology and structure analyses and produce the list of lenses to bring to the library scan downstream. Walks the PM through each candidate one at a time for confirm / edit / remove / add. The PM's actions during review are captured in the doc as data for future calibration. Persists as Step 1 of the mapping doc."
user-invocable: true
argument-hint: Path to the methodology doc (and optionally the structure doc) — e.g., petri/methodology-2026-04-25.md
---

## Inputs

- Methodology doc (`<project>/methodology-YYYY-MM-DD.md`) — required. Pressure-points section in the fitness assessment (section 8) is the primary source.
- Structure doc (`<project>/structure-YYYY-MM-DD.md`) — optional. If not found, check the methodology doc for an `## Artifact map` section: that's the auto-combined case from `/analyze-structure`, and any structural opportunities live there.
- PM-stated goals — if the PM has named goals in chat, those are also valid opportunity sources, with equal weight to opportunities surfaced from the analysis docs.

This skill is the first of the two designed Phase 3 checkpoints (the second is in `/map-opportunities`). See "Checkpoint frequency" in `meta-claude/CLAUDE.md` for why the chain holds at exactly two and what each checkpoint protects against.

## Output

The inventory is persisted as Step 1 of the mapping doc. The mapping doc itself is created and finalized by `/map-opportunities` — but this skill writes the inventory section first, so the IDs are stable and referenceable.

Write to `<project-name>/mapping-YYYY-MM-DD.md`.

### Per-opportunity schema

```
id: integer (numeric, stable, referenceable from later proposals)
scope: enum (app | companion | cross-project | project-specific values as needed)
domain: enum (process | structure)
description: string (1–2 sentences)
source: line in the analysis doc this was lifted from (paraphrase + citation)
```

Priority is intentionally not part of this schema. Prioritization happens downstream — `/map-opportunities` and `/draft-proposals` reshape importance once library entries and sibling patterns are in scope. The job here is to name lenses, not rank them.

### Output structure (Step 1 of the mapping doc)

```markdown
# <Project> Opportunity → Source Mapping

*Mapping performed YYYY-MM-DD*

Companion to <methodology-doc> and <structure-doc>. Step 1 below is the opportunity inventory — the list of lenses brought to the library scan in Step 2. `/map-opportunities` adds Step 2 against this confirmed inventory.

---

## Step 1: Opportunity inventory

### <Project> (app) — process / methodology

1. **Title.** Description (1–2 sentences). *(Scope. Domain.)* Source: paraphrase + citation from analysis doc.
2. ...

### <Project> (app) — structure

3. ...

### Cross-project

4. ...
```

Group by scope first, then sub-group by domain. IDs are sequential across all groups (don't restart per group).

**Capture review actions in the doc.** This is a data-gathering checkpoint — what the PM keeps, edits, removes, or adds is signal for future calibration of the inventory step itself (parallel to how `intake-decisions.md` captures the same shape for library scans). Encoding:

- **Confirmed as-is** — listed as the schema above, no annotation.
- **Edited** — final wording shown; original paraphrase shown beneath as `*Original draft: "...". Edited during review: <one-line reason if PM gave one>.*`
- **Removed** — struck through with reason: `~~N. Title — description.~~ *Removed during review: <reason>.*` Keep the ID slot in the sequence (don't renumber).
- **Added during review** — listed normally with annotation `*Added during review: <reason>.*`

## Process

1. **Read inputs.** Methodology doc (required), structure doc or `## Artifact map` section if combined.

2. **Extract candidate opportunities.** Pull from each fitness assessment's "Pressure points and opportunities" section. If the PM has stated goals in chat, fold them in. Each candidate gets the schema above, including a `source` paraphrase tied back to the analysis doc.

3. **Group by scope.** Use `app`, `companion`, `cross-project` for projects with sub-components. Use project-specific scope labels when meaningful (e.g., for meta-claude itself, scope buckets might map to PM-stated goals).

4. **Walk the PM through the inventory one at a time.** For each candidate, surface in chat:

   > **Candidate N — Title** *(Scope. Domain.)*
   > Description (1–2 sentences).
   > Source: paraphrase + citation.
   >
   > Confirm / edit / remove / [add another here]?

   Wait for the PM's response per item. Track the action — confirm, edit, remove, or add — for the doc-side log. Don't recommend a focus area or rank; the inventory is the lens list, not a roadmap.

5. **After all candidates are walked, ask** if anything is missing that should be added. Capture each addition with a short reason for why it surfaced during review (signal for future calibration).

6. **Hard checkpoint — do not skip.** Don't proceed to writing the persisted Step 1 until the PM confirms the inventory or has finished giving per-item actions. `/map-opportunities` should not run against an unconfirmed inventory. The checkpoint also serves as a data-gathering moment — what the PM keeps, edits, removes, or adds is the calibration signal for evolving this skill toward less PM input over time.

7. **Save the inventory.** Write Step 1 of the mapping doc with the confirmed inventory and the review-action annotations described in the output structure. The doc is incomplete at this point — Step 2 mapping is added by `/map-opportunities`.

## Handoff

After saving Step 1 of the mapping doc, surface in chat:

> Inventory saved as Step 1 of `<project-name>/mapping-YYYY-MM-DD.md`. Next skill in the chain: `/map-opportunities`. Run when ready to proceed.

## Scope — what this skill does NOT do

- Does not map opportunities to library entries (that's `/map-opportunities`).
- Does not propose changes (that's `/draft-proposals`).
- Does not rank or prioritize — prioritization happens downstream once library and sibling-pattern context is in scope.
