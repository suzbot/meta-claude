# Meta-Claude

Meta-claude is a methodology workspace, not a software product. It contains:

1. A chained set of skills that turn a project into a set of source-backed improvement proposals (`/analyze-methodology` → `/analyze-structure` → `/opportunity-inventory` → `/map-opportunities` → `/draft-proposals`).
2. A periodic library scan that surfaces new candidate articles and platform feature updates (`/library-intake-scan`).
3. A curated library of AI-collaboration practices (`claude-code-source-library-catalog.md`) that the analysis skills draw from.
4. The dated outputs of past analysis passes and intake scans.

## Purpose

Help the PM (a) apply new Claude Code and Anthropic platform features as they ship, and (b) adopt community-converging patterns as practitioner consensus forms. The library and catalog surface both signals; the analysis flow turns them into project-specific change.

**Long-term direction:** the analysis chain runs unattended. Current PM-as-trigger between skills is scaffolding being earned out of as the chain proves itself. Proposals and skill changes should be oriented toward that trajectory — adding checkpoints is a cost, not a default.

## When to start an analysis pass

- The project has been running long enough to have stable patterns and visible pressure points.
- The PM wants a deliberate audit rather than iterative ad-hoc improvements.
- It's been long enough since the last audit that the project's context has meaningfully changed.

If those don't apply, it's not yet time. The analysis flow is heavyweight by design.

---

## Cross-cutting rules

These rules apply across multiple skills. The skills reference them by name rather than inlining.

### Skills vs. reference docs — decision rule

A skill is worth creating when the same judgment gets exercised repeatedly and benefits from consistency across invocations. One-off analyses do not justify skills — they lack the feedback loop that makes a skill better than a fresh agent. Prefer:

- **Skill** — recurring workflow with consistent structure (retros, doc updates, bug-fix protocol).
- **Reference doc / checklist** — one-off decisions that might recur years later (monolith split evaluation, framework migration).
- **Nothing** — decisions that may never recur.

### Genericizing library references in analysis docs

Analysis docs reference *topics* rather than specific article titles. The library is mutable; analysis docs should age more slowly. Write "context anchoring practices" not "Fowler's Context Anchoring piece."

### Cross-project pattern sourcing

A **cross-project source** is another project whose analysis docs already live under `meta-claude/<other-project>/` (e.g., `<other-project>/methodology-YYYY-MM-DD.md`, `<other-project>/proposals.md`). These are valid input for `/map-opportunities` and `/draft-proposals` — patterns proven elsewhere are evidence that a library entry has real-world traction, and a clean translation can be lifted directly.

Distinct from app-and-companion comparisons within a single project (e.g., NQ + NQ-companion) — those are handled in `/analyze-methodology`'s comparison table.

How to use cross-project docs:

- **Lift directly** when an opportunity has near-identical shape to one another project already addressed (cite the proposal or skill name, summarize what was done, name why it transfers).
- **Note divergence** when the shape rhymes but the project's constraints differ — name the divergence so `/draft-proposals` can decide whether to adapt or reject.
- **Don't list as relevant just because the other project mentioned the same library entry.** Only count as relevant if the other project's *use* of the entry maps to this opportunity.

### Checkpoint frequency

Phase 3 has three designed checkpoints — after opportunity inventory, after mapping, and per-proposal during drafting. Each is also a data-gathering moment: what the PM keeps / edits / removes / approves / drops / revises and the reasoning is captured in the artifact itself, parallel to how `intake-decisions.md` captures the same shape for library scans. The captured signal is the calibration loop — over time it lets the chain pre-shape decisions and need less PM input. Adding checkpoints beyond these three is a cost, not a default.

---

## Content triage — where guidance lives

When new guidance, rules, or learnings need a home, decide based on scope and lifecycle:

- **SKILL.md** — procedural rules used in one skill. Schemas, output formats, step-by-step process, evaluation criteria specific to that skill. *Heuristic:* "guidance close to the action" — if only one skill uses it, put it there.
- **CLAUDE.md** (this file) — project orientation and cross-cutting rules used by ≥2 skills. Light on examples, since examples accumulate as memories. *Heuristic:* if you can't clearly say "this is used by multiple skills," it doesn't belong here.
- **Auto-memory (feedback type)** — calibration learned from specific decisions. Empirical observations, PM preferences, lessons of the form "we used to default to X, now we don't." Single instances stay in the source artifact (decision log, retro notes); a feedback-memory entry forms once a pattern recurs (≥2 supporting instances per P5's consolidation rule).
- **Reference doc** — one-off decisions or "revisit-when-X" items that need a stable home.

The discipline: don't put procedural detail in CLAUDE.md (bloats always-loaded context); don't put cross-cutting rules in a single SKILL.md (drift across skills); don't put rules in memory (memory is for calibration, not procedure).

### Fluff discipline

Don't write lines that won't carry actionable information. Applies to any artifact (skills, CLAUDE.md, analysis docs, proposals, intake reviews, memory entries) — reader attention and context are the budget.

Patterns to avoid:
- Meta-commentary about why content is organized a particular way.
- Restatements of descriptions, headers, or surrounding context.
- Navigation prose the reader can derive from structure.
- Procedural restatements (e.g., "save the file" as both a process step and a confirmation).

Before writing a line, ask: would a reader's ability to act suffer if it weren't there? If no, leave it out.

When reading existing content that contains these patterns, propose trimming it.

---

## Key locations

- **Catalog (promoted entries):** `claude-code-source-library-catalog.md`
- **Skills (analysis flow + intake scan):** `.claude/skills/` (project-scoped)
- **Past analyses:** per-project subdirectories. Frozen snapshots are `<project>/methodology-YYYY-MM-DD.md`, `<project>/structure-YYYY-MM-DD.md`, `<project>/mapping-YYYY-MM-DD.md`. The living document is `<project>/proposals.md`, which carries an internal "Last updated" line.
- **Intake decision log:** `intake-decisions.md`
- **Auto-memory (lessons + PM preferences):** `~/.claude/projects/-Users-suzanneerin-projects-meta-claude/memory/`
