---
name: analyze-structure
description: "Read a target project's structural inputs (architecture docs, dependency manifests, code layout) and produce a structure analysis describing what the project is technically. Supports an artifact-map variant for non-software projects."
user-invocable: true
argument-hint: Path to the target project; optionally --variant=artifact-map for non-software projects
---

## Variant selection

Two output shapes:

- **Standard** (default) — for software projects. Stack, architectural spine, key structural patterns, etc.
- **Artifact-map** (`--variant=artifact-map`) — for non-software projects (methodology workspace, curated library, knowledge base).

**Trigger for the variant:** if filling out the standard structure produces trivial or N/A answers for "Stack," "Architectural spine," and "Key structural patterns," use the variant. If those sections are genuinely answerable even for an unusual project, stay with the standard form.

**Auto-combine with methodology (variant only).** The artifact-map variant is light by design — no fitness assessment, since that lives in the methodology doc. When the variant is used and `<project>/methodology-YYYY-MM-DD.md` exists for today, append the artifact-map content as a new top-level `## Artifact map` section to that existing file rather than writing a separate structure file. Standard variant does not combine — its fitness assessment makes it a self-contained doc.

## Inputs

- Architecture docs, code layout, dependency manifests (Cargo.toml / go.mod / package.json), key file sizes, tech-debt docs.
- Targeted code reading only — file sizes, rough scale, select architectural files. Not line-by-line review (see Process step 3 for bounds).

For the artifact-map variant: the documents in the project root, the file structure, and any process / methodology docs.

## Output

Standard variant: write to `<project-name>/structure-YYYY-MM-DD.md`. Create the per-project subdirectory if it doesn't already exist.

Artifact-map variant: append a new top-level `## Artifact map` section to the existing `<project-name>/methodology-YYYY-MM-DD.md` (see auto-combine rule above). If no methodology doc exists for today, fall back to writing `<project-name>/structure-YYYY-MM-DD.md`.

### Standard output structure

1. **What [project] is, in one paragraph** — technical framing.
2. **Stack** — languages, frameworks, data stores, runtime environment.
3. **What it's trying to accomplish** — technical commitments (not product goals).
4. **Architectural spine** — the central abstraction or loop everything else orbits.
5. **Key structural patterns** — numbered list, typically 10–17 items.
6. **Additional domain sections as applicable** (e.g., "Companion architecture" when relevant).
7. **What [project] is NOT** — clarifies which library practices miss the target.
8. **Is the structure serving its goals?** — fitness assessment (same schema as Phase 1a, see below).
9. **Implications for which library practices apply** — relevant / adjacent / irrelevant.
10. **Where [project] might grow technically** — from the roadmap.
11. **A note on "agent" as a word** — disambiguation section.

### Artifact-map variant output structure

1. **What [project] is, in one paragraph** — structural framing.
2. **Document inventory by volatility** — table grouping artifacts by how often they change (e.g., methodology spine / promoted / staging / dated snapshot), with purpose per row.
3. **How artifacts feed each other** — relational view (text or diagram) of which documents produce or consume which; flag the feedback edges that close loops.
4. **What [project] is NOT** — clarifies which library practices miss the target.
5. **Implications for which library practices apply** — relevant / adjacent / irrelevant.
6. **A note on "agent" as a word** — disambiguation.
7. **Where [project] might grow** — framed as future artifacts, processes, or cadences rather than technical capabilities.

The fitness assessment is folded into the methodology analysis when the variant is used — don't duplicate it here.

### Fitness assessment schema (standard variant only)

- **What looks well-served** — evidence of fit
- **Pressure points and opportunities** — named without prescribing tools (load-bearing for `/opportunity-inventory`)
- **What can't be determined from this analysis** — honest limits
- **Overall read** — qualified verdict

## Process

1. **Variant decision.** If the PM didn't specify, run the trigger heuristic above. If unclear, surface the choice with rationale and let PM redirect.

2. **Inventory inputs.** List which structural docs / code files / manifests exist. Note what's missing.

3. **Targeted reading.** For software projects: open key architectural files, read manifests, scan file sizes. Not exhaustive — read manifests fully (small, high-signal), but for code, open key entry points and 1–2 representative files per major architectural concern (e.g., one route handler, one service module). Don't sweep entire folders.

   **Large files — skim first.** For any doc or source file beyond ~300 lines, read the head (first ~100 lines, or the TOC / headings / exports) to map structure, then drill into sections that look load-bearing for the schema. Don't read large files cover-to-cover.

   **Stop when** sections 1–7 (standard) or 1–3 (variant) can be drafted at the level of detail the schema asks for. If meaningful uncertainty remains, name it in section 8 under "What can't be determined" rather than expanding the read budget.

4. **Draft the sections.** Standard form: 11 sections. Variant form: 7 sections. Genericize library references — write topics, not titles (cross-cutting rule from `meta-claude/CLAUDE.md`; bites in section 9 / variant section 5).

5. **Save the output** per the Output rules above (standard variant writes a new file; artifact-map variant appends to the methodology doc when it exists for today).

## Handoff

After saving, surface in chat:

- **Standard:** "Output saved to `<project-name>/structure-YYYY-MM-DD.md`. Next skill in the chain: `/opportunity-inventory`. Run when ready to proceed."
- **Artifact-map (combined):** "Artifact map appended to `<project-name>/methodology-YYYY-MM-DD.md`. The fitness assessment in that doc is the input to `/opportunity-inventory`. Run when ready to proceed."
- **Artifact-map (no methodology doc for today):** "Output saved to `<project-name>/structure-YYYY-MM-DD.md` (no methodology doc found for today, so no auto-combine). Next skill: `/opportunity-inventory`."

## Scope — what this skill does NOT do

- Does not propose changes.
- Does not modify the target project.
