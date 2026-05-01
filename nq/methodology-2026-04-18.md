# Next Quest: Working Methodology

*Analysis performed 2026-04-18*

Next Quest (NQ) is an RPG-themed productivity app for ADHD brains. Unlike Petri, which encodes its methodology in slash-commands and skill files, NQ's methodology lives almost entirely in prose — principally `CLAUDE.md` and `VISION.md` — plus a two-project architecture (NQ + NQ-Companion) that itself is a methodology statement.

## Core principles (the stable layer)

Four commitments recur across every document and phase:

1. **Vision has hard rules.** "No streaks, ever." "One quest at a time." "Don't become the problem." These aren't style preferences — they're constraints that filter features before they reach design. A feature that adds management overhead is cut, regardless of how compelling it sounds, because the app "must never become another system to manage." This is a tight alignment between product values and methodology filters.

2. **The dev process practices what the app preaches.** The app is for people with ADHD, built by someone with ADHD. A dedicated "Pacing & Breaks" section in `CLAUDE.md` requires: build in stopping points, scope work in small chunks (≤3 substeps), treat any commit as a valid stopping point, don't manufacture urgency. This meta-discipline shapes pacing as strongly as scope.

3. **Vertical slices over layers.** Every implementation step must deliver something the user can functionally test. "Implementation steps must be vertical slices, not 'backend then frontend.'" This rule is stated in three places (CLAUDE.md, the phase cycle, design doc guidance) because it's the primary guard against drifting into unusable intermediate states.

4. **Evidence + alignment before action.** Trust user observations as evidence. Don't speculate about causes. If there are substantive concerns, present trade-offs — don't ask "are you sure?". Functional terms over code mechanics. These communication norms are explicit.

## The 8-step phase cycle

Every feature or phase runs through a named sequence, spelled out in `CLAUDE.md`:

1. **Requirements Discussion** — conversation only, no docs yet. Ask questions, surface ambiguities, explore edge cases.
2. **Requirements Doc** — Claude drafts, user refines. Lives in `docs/requirements/`. What and why.
3. **Tech Design Discussion** — conversation about how. Entities, relationships, data flow, trade-offs. Think in vertical slices.
4. **Design Doc** — Claude drafts, user refines. Lives in `docs/design/`. How to build it, broken into vertical-slice steps.
5. **Step Spec** — small implementation spec scoped to one session.
6. **Implementation** — code to the step spec. Tests where appropriate.
7. **Human Testing** — user runs the app and verifies.
8. **Documentation** — update `DATA_MODEL.md`, `CLAUDE.md`, any other docs.

"Do NOT skip steps or combine them without discussing it first. Do NOT draft docs that introduce decisions we haven't discussed." Hard rule.

This cycle has run 20+ times — 20 design docs and 18 requirements docs in `docs/`, numbered by phase (phase0, phase0.5, phase1, 1.5, 2A-2H, 3, 4, 5A-5D).

## The artifact architecture (layered by volatility)

Documents are organized by audience and change frequency:

| Layer | Example | Purpose |
|---|---|---|
| **North star** (rarely changes) | `VISION.md` | Core concept, hard rules, phased roadmap |
| **Living ground truth** | `DATA_MODEL.md` | Entity definitions, relationships, quest selector logic. Read first when troubleshooting. |
| **Collaboration norms** | `CLAUDE.md` | How we work together, 8-step cycle, dependency ledger |
| **Player-visible reference** | `docs/mechanics.md` | How the app works from the user's perspective |
| **Per-phase specs** | `docs/requirements/phaseN.md`, `docs/design/phaseN.md` | What and how for each phase |
| **Current target** (replaced per slice) | `docs/step-spec.md` | Implementation-ready detail for the step in flight |
| **Deferred items** | `docs/tech-debt.md`, `docs/test-later.md` | Postponed cleanup; behavior that needs manual verification |
| **CLI surface** | `docs/cli-requirements.md`, `docs/cli-guide.md` | The external API contract for NQ-Companion and future AI callers |

Two things stand out:

- **Doc + commit discipline.** "Don't commit docs without code." Design docs, requirements, and step specs only ship alongside implementation — or on explicit request. Prevents docs from drifting ahead of reality.
- **`DATA_MODEL.md` as single source of truth for lifecycle rules.** It's where subtle behaviors (how `active` flags interact with completion, how saga steps differ from regular quests) live. Explicit instruction to consult it first when bugs surface.

## The two-project architecture is itself a methodology

NQ-Companion is a separate project that reads NQ's live database (read-only by convention) and writes to it only through the NQ CLI. This split encodes a methodology choice:

- **The app does one thing.** It surfaces one quest at a time.
- **Analysis, planning, and insight live outside the app.** Daily insight reports, weekly focus tracking, imports from other projects — all in NQ-Companion.
- **The CLI is the contract.** Companion prefers CLI reads over raw SQL, so business logic (due filtering, saga step eligibility) lives in one place.
- **Skills live in the consumer project.** NQ has no project-level Claude skills. NQ-Companion has `nq-daily-plan`, `home-ops-to-nq`, and others.

This is the opposite of Petri, where skills live in the project being built. NQ's reasoning: the analytical layer is a different set of concerns (tone, framing, reporting) that shouldn't pollute the app's codebase.

## Distinctive methodological moves

Worth naming because these diverge from common practice (and from Petri's pattern):

1. **ADHD-aware pacing as explicit discipline.** Not "take breaks if you want" — rules about chunk size, stopping points, and framing. "It's always okay to stop." "Any commit point is a valid stopping point. Frame it that way." Cliffhangers to the next step are prohibited.

2. **Dependency ledger in CLAUDE.md.** Every Rust crate and npm package is documented with what it does and why. The user is a PM without deep Rust ecosystem familiarity, so the ledger itself is the security/audit guardrail. "No silent installs."

3. **Assumption discipline.** "Actually think about gaps before saying there aren't any. When asked 'do you have questions before drafting,' genuinely analyze the requirements for edge cases. Don't default to confidence." This is a guard against false-completeness.

4. **Summarize after every design doc.** End with a conversational summary of approach + implementation steps, each step a vertical slice. This both validates the design and sets up the next conversation.

5. **Product rules double as scope filters.** "No streaks" and "Don't become the problem" don't just shape the product — they kill features at the requirements stage. A proposed feature that would create management overhead gets rejected before design.

6. **Tone rule for companion vs. app.** The app is RPG-themed. The companion is "straight and warm" with explicit anti-patterns: no guilt, no streaks, no imperatives ("do this," "tackle," "anchor your night"), never reference items that weren't completed. Tone is treated as a design decision with the same rigor as entity modeling.

7. **Snapshot-on-completion as an analytical choice.** When completion history gained `difficulty`, `skills`, `attributes`, `tags` snapshot fields, the explicit motivation was "so completion history is self-contained for external analysis." The app's data model is shaped to serve the analytical layer.

## What this methodology optimizes for

- **A user who can't afford to manage the system.** The app and its development discipline both protect against task-manager bloat.
- **Small, shippable, verifiable slices.** Every commit is a viable stopping point. Every design step is testable by hand.
- **Cross-project continuity.** NQ + NQ-Companion + Home-Ops form a three-project graph. The methodology handles that via CLI contracts and shared READMEs rather than monorepo conventions.
- **Low ceremony, high rigor on the parts that matter.** No slash commands or custom skill files in NQ. But the phase cycle, vertical-slice rule, and data-model-first-for-debugging rules are tightly enforced.

## Comparison to Petri's methodology (at a glance)

Both projects are built by the same PM with Claude Code. Their methodologies diverge in revealing ways:

| Dimension | Petri | NQ |
|---|---|---|
| Methodology encoding | Slash commands + SKILL.md files | Prose in CLAUDE.md |
| Phase cycle | Skill-driven (/new-phase → /refine-feature → /implement-feature → ...) | 8 named steps enforced by convention |
| Retros | /retro skill with history search | No formal retro mechanism — feedback flows into CLAUDE.md edits directly |
| Skills location | In-project (.claude/skills/) | In companion project (.claude/skills/ of nq-companion) |
| Vertical slicing | Implicit in step design | Explicit rule, mentioned in 3+ places |
| Pacing discipline | None specific | Named section; ADHD-framed |
| Values doc | Values.md (emergent) | VISION.md + hard product rules |
| Dependencies | Implicit | Ledger in CLAUDE.md |

The two projects converge on: trust-user-observations, functional-framing, requirements-first, minimal-dependencies, evidence-before-hypothesis. They diverge on: how much to encode as tooling vs. prose, where the analytical layer lives, and how much pacing discipline gets formalized.

## Is the process serving its goals?

From this analysis — based on CLAUDE.md, VISION.md, the 20+ phase specs, and the companion's skill definitions, not session-level observation of how the process feels during use — some things can be assessed with reasonable confidence and others cannot.

**What looks well-served:**
- **Features keep landing on a tight product.** 20+ phases completed, hard product rules intact (no streaks ever shipped, no management overhead creep). The scope filter is working — features that would have violated product principles don't seem to have made it through.
- **ADHD-aware pacing is honored.** Phases are broken into small slices. Each has a testable outcome. "Any commit is a valid stopping point" framing is visible in how phase design docs are structured.
- **Cross-project flow is working.** Home-ops → NQ-Companion → NQ CLI → NQ DB is a real pipeline that generates real output (daily plans, weekly focus tracking, imports). The contracts between projects are holding.
- **Tone separation holds.** App is RPG-themed; companion is "straight and warm." The discipline shows up in both codebases — no RPG flavor leaks into companion reports; no analytical prose creeps into the app.
- **Data-model-first debugging.** "Read DATA_MODEL.md first when troubleshooting" is a simple rule with outsized leverage — it keeps debugging grounded in domain semantics rather than code spelunking.

**Pressure points and opportunities for greater efficiency:**

These are areas where the current approach works but could potentially be sharpened as AI-coding practices and tooling evolve. Specific tool and process choices are out of scope here — this is about naming where leverage exists.

- **Prose-heavy encoding.** The 8-step cycle, vertical-slice rule, dependency ledger, numbered-list norm all live in CLAUDE.md prose. Prose rules rely on being re-read and applied each session. As process-gate tooling evolves — structural ways to enforce rules rather than just state them — some of this could shift from "instruction the model must follow" to "default the environment enforces."
- **No formal retro mechanism.** Process improvements currently flow into CLAUDE.md edits directly, without a structured reflection step. Recurring friction must be noticed and diagnosed manually. More structured feedback-signal capture could compound learning across phases rather than relying on moment-to-moment recognition.
- **Documentation fan-out without a named skill.** Each phase completion updates README, CLAUDE.md, DATA_MODEL.md, mechanics.md, design doc, and step-spec. Unlike Petri's /update-docs, there's no dedicated skill coordinating this — it's step 8 of the phase cycle, done by hand. As schema grows, tooling for documentation consistency or derivation may offer leverage.
- **Companion skill repetition.** Daily plans, weekly focus, home-ops imports each have their own skill with similar mechanics (read CLI output, apply filters, format as HTML). Some patterns could consolidate if a lighter mechanism for sharing skill fragments emerges.
- **Cross-project coordination is skill-encoded.** home-ops-to-nq traverses three projects through detailed prose steps. As the pattern recurs (more upstream projects, more downstream skills), tooling for cross-project contracts beyond "write it into the skill" may reduce duplication.
- **Context priming for companion skills.** Companion skills carry extensive tone rules, data-interpretation rules, workflow steps. Each session re-reads these. Evolving context-management tooling may offer ways to prime durable context without paying the re-read cost per session.
- **Persistent per-phase state is minimal.** step-spec.md is a single file, and phase design docs carry forward. Working memory across sessions is reconstructed from those artifacts plus CLAUDE.md. Richer mechanisms for persistent per-phase state may reduce the context-rebuild cost at session start.
- **Assessing product-rule adherence.** "Don't become the problem" is a hard rule, but catching violations relies on PM vigilance when a feature is proposed. Tooling that flags scope-drift or management-overhead candidates earlier could make the filter more reliable.

**What can't be determined from this analysis:**
- Whether the prose-heavy approach is hitting fatigue at 20+ phases, or whether it's still sustainable.
- Whether the companion's reports are actually being used day-to-day as intended, or generated but under-consumed.
- How much friction exists in the gap between the 8-step cycle on paper and how it's executed session-to-session.
- Whether the lack of a formal retro is costing learning that would compound, or working fine because the PM is actively synthesizing.
- Whether vertical-slicing discipline is being genuinely honored or occasionally relaxed under phase pressure.

**Overall read.** The process is serving its goals well, and its minimalism is load-bearing — the lack of heavy process machinery is itself aligned with "don't become the problem." The largest efficiency opportunities sit at places where the current approach relies on prose discipline and manual synthesis: retros, documentation fan-out, context priming, cross-project coordination. Each of these is working, but each is also the kind of thing where evolving AI-coding practices and tools may offer structural alternatives to prose-based discipline. A deliberate analysis of which specific tools or practices to adopt is a separate exercise and should be done when the library of available options stabilizes.

## Using this as a lens for the library

For NQ, library articles are most relevant when they address:

1. **Prose-heavy methodology** — how to keep CLAUDE.md-style instructions effective as projects grow.
2. **Cross-project integration** — multi-agent coordination patterns, shared CLIs as contracts, analytical companions.
3. **Data-aware AI analysis** — how to make data self-describing for downstream AI (snapshot-on-completion is exactly this pattern).
4. **Pacing and session discipline** — anything about scoping AI-assisted work into shippable slices.
5. **Product-scope rules as methodology filters** — writing on keeping AI from helpfully expanding scope past the product's design principles.

Articles on heavy tooling/skill composition (Petri's style) are less directly applicable to NQ, but worth reading as a what-if: NQ may grow into that pattern as its phase count increases.
