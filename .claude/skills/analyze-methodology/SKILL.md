---
name: analyze-methodology
description: "Read a target project's methodology inputs (CLAUDE.md, skills, processes, retros, values, vision docs) and produce a methodology analysis describing how the project is built."
user-invocable: true
argument-hint: Path to the target project (e.g., ~/projects/petri)
---

## Inputs

Read everything that isn't code or technical planning:

- Requirements docs, vision docs (high-level roadmap; not detailed task plans)
- CLAUDE.md (or equivalent collaboration norms file)
- Skills and agents (`.claude/` directories)
- Process / workflow docs, flow diagrams
- Retro records, memory files, values docs
- README

**Skip:** code files, implementation plans, phase plans that are pure task breakdowns.

## Output

Write to `<project-name>/methodology-YYYY-MM-DD.md`, where `<project-name>` is the basename of the target project directory and `YYYY-MM-DD` is today's date. Create the per-project subdirectory if it doesn't already exist.

### Output structure

1. **What [project] is, in one paragraph** — framing.
2. **Core principles (the stable layer)** — 3–5 commitments that recur across documents.
3. **The workflow backbone** — the main repeating cycle (phase cycle, skill graph, etc.).
4. **The artifact architecture (layered by volatility)** — tabular view of documents by audience and change frequency.
5. **Distinctive methodological moves** — practices that diverge from common practice.
6. **Skill audit** *(conditional — only when the project has a project-level skill set)* — for each skill, a short observational audit across three dimensions: (a) overlap with new platform features (especially auto-memory and capabilities post-dating the skill's design); (b) outdated patterns or conventions (verbose headers, context rules newer models handle by default, orchestration patterns superseded); (c) unleveraged current capabilities (hooks, subagent delegation, structured memory, scheduled tasks, improved search). **Describes state and identifies opportunities — does not prescribe changes.** Patterns that recur across multiple skills flow forward into section 8 (pressure points).
7. **What this methodology optimizes for** — summary.
8. **Is the process serving its goals?** — fitness assessment.
9. **Comparison table** *(conditional — only when a sibling project exists)*. Most projects analyzed in isolation skip this entirely; reserved for cases like Petri ↔ NQ.
10. **Using this as a lens for the library** — forward-looking questions for reading library articles.

### Fitness assessment schema (applied in section 8)

- **What looks well-served** — evidence of fit
- **Pressure points and opportunities for greater efficiency** — named without prescribing tools
- **What can't be determined from this analysis** — honest limits
- **Overall read** — qualified verdict

The pressure-points list is load-bearing — it's the input to `/opportunity-inventory` later in the chain. Number items implicitly by listing position; ensure each is described in 1–2 sentences and stands alone.

## Process

1. **Inventory inputs.** List which methodology docs exist in the target project and which standard inputs are missing (e.g., no retros, no values doc). Note absences — they're informative.

2. **Read methodology inputs.** Don't read code. For skills: read each SKILL.md in `.claude/skills/` if present.

   **Large docs — skim first.** For any doc beyond ~300 lines, read the head (first ~100 lines, or the TOC/headings) to map its structure, then drill into sections that look load-bearing for the 10-section output. Don't read large docs cover-to-cover. If meaningful uncertainty remains after that, name it in section 8 under "What can't be determined" rather than expanding the read budget — `/analyze-structure` will fill in technical context downstream.

3. **Draft the 10 sections.** Sections 1–7 describe state. Section 8 (fitness) interprets it. Section 9 only if a sibling exists. Section 10 frames the library lens.

4. **Save the output.** Write to the path above.

## Handoff

After saving, surface in chat:

> Output saved to `<project-name>/methodology-YYYY-MM-DD.md`. Next skill in the chain: `/analyze-structure`. Run when ready to proceed.

The PM is the trigger between skills — do not auto-invoke the next skill.

## Scope — what this skill does NOT do

- Does not read source code (architecture is `/analyze-structure`'s job).
- Does not propose changes (that's `/draft-proposals`).
- Does not modify the catalog or any project files outside `meta-claude/`.
