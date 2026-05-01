---
name: map-opportunities
description: "For each opportunity in the inventory, list potentially relevant library catalog entries and cross-project patterns with one-line reasons each. Surface cross-cutting themes and hold a strategy checkpoint with the PM. The PM's strategy decisions are captured in the doc as data for future calibration. No solutioning — that's `/draft-proposals`'s job."
user-invocable: true
argument-hint: Path to the mapping doc with Step 1 inventory already filled in — e.g., petri/mapping-2026-04-25.md
---

## Inputs

- Mapping doc with Step 1 inventory (`<project>/mapping-YYYY-MM-DD.md`) — required.
- Catalog (`claude-code-source-library-catalog.md`) — for library entries.
- Cross-project analysis docs — methodology / structure / proposals docs from other projects in `meta-claude/` whose analyses have been completed (e.g., when mapping NQ, Petri's docs are valid input). See "Cross-project pattern sourcing" in `meta-claude/CLAUDE.md` for what counts and how to use them.

## Output

Append Step 2 (the mapping itself), cross-cutting themes, and gap summary to the existing mapping doc. The Step 1 inventory remains unchanged.

### Per-opportunity mapping schema

```
opportunity_id: integer (FK to inventory)
core_question: string (the decision the opportunity is really asking)
library_primary: list<catalog_entry_reference> (strong fit)
library_secondary: list<catalog_entry_reference> (moderate fit)
cross_project_patterns: list<string> (concrete skills, files, conventions from other projects' docs)
note: string (gap | coverage summary | force-fit warning)
```

The split between primary and secondary reflects strength of fit, not priority. The job here is to surface every potentially relevant entry with a one-line reason; downstream (`/draft-proposals`) decides what actually drives change.

### Output structure (appended to the mapping doc)

```markdown
---

## Step 2: Mapping

### Opportunity 1: <title>

**Core question:** <one-sentence question>.

- **Library primary:** <entries with brief reason>.
- **Library secondary:** <entries with brief reason>.
- **Cross-project patterns:** <specific skills/files/conventions from other projects' docs; name why they apply>.
- **Note:** <gap flag | coverage summary | force-fit warning>.

(repeat for each opportunity)

---

## Cross-cutting themes (strategy checkpoint)

**Theme A — <name>.** Opportunities X, Y, Z. Library: <anchor entries>. Cross-project: <anchor patterns>. *Question for PM:* <strategy question>.

**PM strategy decision:** <captured after the checkpoint — what the PM said the theme is/isn't, where it creates value, whether to bundle related opportunities or split, and any reasoning. Bullet list of PM responses, paraphrased.>

(repeat for each theme — typically 2–4)

---

## Gap summary

Opportunities where library coverage is thin or absent:

- **#X (title)** — <what didn't map and why no obvious entry fits>.

(list only the gaps; do not enumerate everything that DID map. Do not propose handling — that's `/draft-proposals`'s job.)
```

## Process

1. **Read inventory.** Confirm Step 1 is present and confirmed. If not, halt — `/opportunity-inventory` should run first.

2. **Read catalog and cross-project docs.** Build a working set of catalog entries (by Topics field) and cross-project patterns (skills, files, conventions from `<other-project>/methodology-*.md` and `<other-project>/proposals.md`).

3. **Per-opportunity mapping.** For each opportunity:
   - State the core question in one sentence.
   - List every potentially relevant library entry with a one-line reason it might apply (split into primary / secondary by strength of fit, not priority).
   - List cross-project patterns that have already addressed something close, with a one-line reason — see "Cross-project pattern sourcing" above for what counts.
   - Flag gaps and force-fit warnings in the `note` field. **Don't propose handling for gaps — that's `/draft-proposals`'s job.** This skill stops at "what didn't map and why no obvious entry fits."

4. **Identify cross-cutting themes.** When mapping surfaces a theme that spans multiple opportunities (e.g., "memory system unleveraged," "hooks not used," "skills-as-runnable-workflows"), treat it as a cross-cutting theme worth a strategy discussion before proposal drafting.

5. **Strategy checkpoint in chat — second of the two designed Phase 3 checkpoints.** For each cross-cutting theme:
   - Name the theme and the opportunities it spans.
   - State the load-bearing library entry and cross-project pattern (if any).
   - Ask the PM: does the theme apply here? Where does it create specific value? Should related opportunities be addressed as one designed system or separately?

   Also invite the PM to flag any per-opportunity mappings that look force-fit, any library entries they expected to see and didn't, or any gap-summary entries that concern them. Catching these here saves wasted proposal drafting downstream.

   Required when ≥2 opportunities cluster around the same theme. Capture the PM's response in the `PM strategy decision` block of the output schema — this checkpoint is also a data-gathering moment. What the PM affirms, redirects, or rejects on theme application is signal for future calibration of how the chain interprets cross-cutting patterns (parallel to `/opportunity-inventory`'s review-action capture).

6. **Save the doc.** Append Step 2, cross-cutting themes (with PM strategy decisions captured), and gap summary.

## Discipline

**Do not list library entries that didn't apply.** That kind of accounting adds length without insight. Surface every potentially relevant entry with a one-line reason, but don't enumerate the rest of the catalog.

**No solutioning.** This skill produces lenses, not fixes. Don't propose handlings for gaps, don't recommend skill-vs-doc choices, don't rank by urgency. Those judgments belong to `/draft-proposals`, after the PM strategy checkpoint has shaped what to bundle.

**Genericize references** in downstream analysis docs. The mapping doc itself is the bridge artifact and is allowed to name specific entries; proposals built from this mapping should reference topics, not titles.

## Handoff

After saving, surface a summary in chat covering:
- What was added (mappings, themes, gaps — counts).
- Force-fit warnings flagged.
- PM strategy decisions captured.

Mapping saved at `<project-name>/mapping-YYYY-MM-DD.md`. Run `/draft-proposals` when ready to proceed.

## Scope — what this skill does NOT do

- Does not draft proposals (that's `/draft-proposals`).
- Does not modify the inventory (that's locked at Step 1).
- Does not silently force-fit a library entry — note the warning.
- Does not propose handlings for gaps, recommend skill-vs-doc choices, or rank opportunities — those are downstream judgments.
- Does not promote articles to the catalog — that's the intake-scan flow.
