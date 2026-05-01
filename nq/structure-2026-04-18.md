# Next Quest: Structural / Technical Analysis

*Analysis performed 2026-04-18*

Companion to `methodology-2026-04-18.md`. That document describes how NQ is built; this one describes what it is, technically, and how it's set up to be that. The goal is a sharp reference for judging which coding and agent practices from the library apply — and which are about a different kind of system.

The analysis covers two projects jointly: NQ (the app) and NQ-Companion (the external analytical layer). The relationship between them is a structural feature of NQ, not an implementation detail.

## What NQ is, in one paragraph

NQ is a **single-user, local-only, Rust + Tauri desktop app** (~14k lines of Rust and HTML/JS) for macOS, with a bundled command-line interface that shares all business logic with the GUI. SQLite is the only data store. Everything runs in one process with no network dependencies except GitHub releases. The frontend is vanilla HTML/CSS/JS — no framework, no bundler, a single ~4000-line `index.html`. The product is an RPG-themed quest giver: a deterministic scoring engine that selects one quest at a time from a user-curated list. NQ-Companion is a separate project that reads NQ's database (read-only) and writes through NQ's CLI to provide AI-assisted analysis, daily plans, and imports from other projects.

## Stack

**NQ:**
- Rust 2021, Cargo workspace
- Tauri 2 (native shell + system webview — no Electron)
- `rusqlite` 0.31 (SQLite, bundled)
- `clap` 4 (CLI)
- `serde` / `serde_json` (data interchange)
- `uuid` 1, `dirs` 6, `libc` 0.2
- Vanilla HTML/CSS/JS (no React, Vue, bundler, or package manager usage beyond an empty-ish `package.json`)

**NQ-Companion:**
- Runs in Cowork (Anthropic's Linux sandbox environment)
- Reads NQ's SQLite DB via `sqlite3` when CLI doesn't cover the query
- Writes to NQ only via the `nq` binary
- Output: HTML reports in `reports/`, JSON state files (e.g., `weekly-focus.json`), skills in `.claude/skills/`

The stack is deliberately minimal. Dependencies are justified line-by-line in `CLAUDE.md`'s ledger.

## What it's trying to accomplish

Five technical commitments drive the architecture:

1. **A quest giver, not a quest list.** The core product output is a single selected quest, not a view of many. This makes the scoring engine the heart of the app. Everything else (UI, storage, CLI) serves the selector.

2. **Never become the burden the app is supposed to relieve.** If a feature adds management overhead, it's cut. The data model stays tight. The UI stays narrow. The dev process has pacing discipline. This is the dominant constraint.

3. **Determinism and no punishment.** XP only goes up. No streaks, no decay. Every day is a fresh start. The scoring algorithm must produce consistent, predictable output given the same state.

4. **Multi-surface presence without multi-app complexity.** Menu bar (system tray), always-on-top overlay (Encounters), main window — all surface the same state. One process, multiple surfaces.

5. **Be analyzable by external AI without risking app state.** Completion history snapshots become self-contained records so the companion can reason about them without joining against current state. The CLI is the write surface — raw SQL writes are not sanctioned. The companion's database access is read-only by convention.

## Architectural spine: the quest selector

The selector is the product. Its scoring formula is:

```
score = overdue_ratio + importance_boost + list_order_bonus + membership_bonus + balance_bonus - skip_penalty
```

Everything else in the codebase either feeds the selector input (quest list, saga steps, completion history, filters, lane assignment) or surfaces its output (quest giver UI, overlay, CLI `list-quests`). The three-lane model (Castle Duties / Adventures / Royal Quests) runs three independent instances of the selector with different candidate pools.

When reading the codebase, start with the selector logic in `nq-core/src/db.rs`. Everything else is scaffolding around it.

## Key structural patterns

1. **Cargo workspace with three crates, one shared library.** `nq-core` is a Rust library crate holding all business logic, data access, migrations, and tests. `src-tauri` is the GUI binary. `src-cli` is the CLI binary. Both binaries depend on `nq-core` — business logic lives in exactly one place.

2. **`db.rs` as the domain monolith.** 7,557 lines. Holds migrations, entities, queries, quest selector, XP engine, campaign progress, tests. This is intentional — consolidation over layering. A second pattern to justify the split would need to clearly earn it.

3. **CLI as the public API for AI callers.** The companion uses `nq list-quests --due` rather than reimplementing due-filtering in SQL. The CLI exposes business-logic-aware views: resolved skill/attribute names, computed `is_due`, pre-filtered by time-of-day and saga step sequencing. This is an architectural decision that AI agents are first-class consumers.

4. **Snapshot-on-completion.** The `quest_completion` table carries `difficulty`, `skills`, `attributes`, `tags` as JSON snapshots taken at completion time. These allow external analysis without joining against the current quest state (which may have changed). Explicit design note: "No backfill — existing completions get NULL for the new fields." Nullable throughout.

5. **Orphan-tolerant foreign keys.** `completion.quest_id` is nullable. Deleting a quest orphans its completions — history survives. Campaign criteria can become stuck if a target is deleted; the recourse is documented (duplicate campaign without that criterion).

6. **Extensible string enums.** `campaign_criterion.target_type` is `"quest_completions"` or `"saga_completions"` as a string, explicitly documented as extensible. Designed for growth without schema migration.

7. **Bitmask filters for time-of-day and days-of-week.** `time_of_day INTEGER` (Morning=1, Afternoon=2, Evening=4, Night=8; 15 or 0 = anytime). `days_of_week INTEGER` (Mon=1..Sun=64; 127 = every day). Hard-filtered at candidate pool construction, not post-scoring.

8. **Unified sort_order namespace for quests and sagas.** Sagas appear on the quest list as single slots interleaved with quests, sorted by a shared sort_order field. Reordering works across both. The quest list is the single source of truth for priority.

9. **Multi-path completion handling.** Completion can happen from five paths: quest list, quest giver, timer (Victorious!), saga tab, overlay (Cast Completion). All must trigger `check_campaign_progress` and XP distribution. Documented as a list to prevent one path drifting.

10. **Build-time asset manifest.** `src-tauri/build.rs` scans `ui/images/` at compile time and generates `manifest.json`. The frontend reads the manifest instead of hardcoding image lists. Adding a new quest giver image is a file-drop + rebuild, not a code change.

11. **External flavor-text and image folders.** `ui/text/` and `ui/images/` hold content editable without code changes. Per-lane subdirectories (`lane1/`, `lane2/`, `lane3/`). No-repeat random selection is encoded in the frontend.

12. **Three surfaces, one state.** The main window, the always-on-top Encounters overlay, and the system tray menu all read and write the same database. State synchronization happens through polling (the overlay polls on a configurable interval) rather than inter-window messaging.

13. **WAL mode SQLite.** Concurrent reads (GUI + CLI) and queued writes. Added specifically because the CLI may run while the GUI is running. The CLI also handles `SQLITE_BUSY` with brief retries.

14. **Platform-specific data path via `dirs`.** Database lives at `~/Library/Application Support/com.nextquest.desktop/next-quest.db` on macOS. Fixed location, no user configuration.

15. **Menu bar app with tray state.** `src-tauri/src/tray.rs` manages the system tray icon, menu, and event handling. The app is designed to live in the menu bar — `cta_enabled` toggles the Encounters polling thread.

16. **Vanilla HTML frontend, single file.** `ui/index.html` is ~4000 lines. No bundler, no framework. Template strings + DOM manipulation. Documented tech debt items acknowledge the cost but don't prioritize a framework migration.

17. **Tauri commands as Rust↔JS boundary.** `src-tauri/src/commands.rs` (757 lines) wraps `nq-core` functions as Tauri commands. This is the only layer where Tauri-specific concerns live — everything else is pure Rust or pure web.

## The companion architecture (NQ + NQ-Companion)

This is a structural feature worth treating as its own topic:

- **NQ is the store of truth and the interactive surface.** Users complete quests, edit priorities, and see the quest giver in NQ.
- **NQ-Companion is the analytical + assistive surface.** It generates daily plans, weekly focus tracking, and imports from upstream projects. It never interacts with the user directly — the user sees its output (HTML reports) and asks Cowork to generate them.
- **Read contract:** Companion prefers CLI reads. Falls back to direct SQLite when CLI doesn't expose the query (e.g., custom aggregations).
- **Write contract:** Companion writes only via the CLI. Brain dumps, saga breakdowns, and home-ops imports all flow through `nq add-quest` / `add-saga-step` / `add-batch`.
- **Skills live in the companion, not in NQ.** `nq-daily-plan`, `home-ops-to-nq`, and skill drafts are all in `nq-companion/.claude/skills/` or as top-level `.skill` files.
- **State files in the companion.** `weekly-focus.json` tracks two one-off quests the user is working toward. It's persistent state that doesn't belong in the app database because it's an analytical concept (focus), not a product concept (quest).
- **Upstream project integration.** `home-ops-to-nq` reads a CSV matrix from a third project (`~/projects/home-ops/`) and translates matrix items into NQ quests and sagas. The companion orchestrates cross-project flow.
- **Tone separation.** App is RPG-themed ("quests," "monsters," "Victorious!"). Companion is "straight and warm." Same user, different voices. Explicit rule: never let RPG flavor leak into the analytical layer.

## What NQ is NOT

Naming what it isn't clarifies which library articles miss the target:

- **Not a web app, not a service.** Single desktop process. No HTTP endpoints, no auth, no sessions.
- **Not cloud-synced today.** Data lives on one machine. Multi-device support is a possible future direction but not current architecture.
- **Not multi-user.** One character, one database, one human.
- **Not mobile.** Desktop-only. macOS primary, Linux/Windows via Tauri are possible but not prioritized.
- **Not framework-driven on the frontend.** No React, Vue, Svelte, bundler, or package manager runtime.
- **Not LLM-powered at runtime.** The quest selector is deterministic. All LLM use is in NQ-Companion, at analysis time, outside the app.
- **Not a microservice architecture.** One binary (plus CLI binary). One data store.
- **Not a background service in the Linux-daemon sense.** It's a menu bar app — user-facing, but minimized.
- **Not a RAG or retrieval system.** The database is small and fully in-memory per query. No embeddings, no vector search.
- **Not event-sourced.** SQLite with nullable FKs and snapshot-on-completion. History is a supplementary log, not the canonical state.

## Implications for which library practices apply

**Highly relevant to NQ + Companion:**
- AI-assisted coding workflows — the whole build relies on them.
- Context engineering for long-running projects — 20+ phases means CLAUDE.md and DATA_MODEL.md discipline matters.
- Spec/plan artifacts — directly analogous to NQ's requirements/design/step-spec pipeline.
- DDD-adjacent thinking and code-domain alignment — the quest selector is a domain model; keeping it coherent matters.
- Feedback loops and retros — NQ doesn't have a formal `/retro` like Petri, but the companion's daily reports are a de facto feedback surface for the PM.
- Evals methodology — could apply if the companion's analytical outputs grew into something that needs evaluation discipline.

**Adjacent / sometimes relevant:**
- RAG patterns — irrelevant to NQ at runtime, but if the companion's reports start pulling from many sources, retrieval discipline could apply there.
- Multi-agent coordination — the companion's skills (daily-plan, home-ops-to-nq) form a small agent ecosystem. Articles on agent-teams could inform future skill composition.

**Irrelevant to NQ:**
- Web/CRUD patterns (ORMs, migrations-in-flight, transactions) — no HTTP, trivial migrations.
- Microservice, streaming, Kubernetes — single binary.
- Vector databases — no retrieval.
- Runtime LLM orchestration — the app is deterministic.
- Database sharding, read replicas — single-user SQLite.

## Is the structure serving its goals?

From this analysis — based on docs, code layout, and file sizes, not runtime observation or day-to-day development friction — some things can be assessed with reasonable confidence and others cannot.

**What looks well-served:**
- **Quest-giver-not-quest-list.** The selector is genuinely the spine of the codebase: scoring formula in one place, three surfaces (main window, overlay, CLI) all consuming it. The product identity and the architectural center are the same thing.
- **Don't become a burden.** ~14k lines total is small. Dependency list is short and justified line-by-line. Minimal-framework choice on the frontend limits drift. These all align with the product's core constraint.
- **CLI-as-API for external AI.** NQ-Companion is actually consuming the CLI productively — daily plans generate, home-ops imports work, weekly focus tracking runs. The contract held up when reality tested it.
- **Snapshot-on-completion.** An analytical decision that shaped the data model and is paying off: the companion reasons about historical completions without joining against mutable current state. The pattern is working as designed.
- **Phase rhythm.** 20+ phase design docs, 18+ requirements docs, all following the same 8-step cycle. Features keep landing. The structure absorbs new entities (saga, campaign, tag, accomplishment) without structural rewrites.

**Pressure points visible:**
- **`db.rs` as a 7,557-line monolith.** All business logic, migrations, entities, queries, selector, XP engine, and tests in one file. Currently readable and grep-friendly, but the growth curve is steep. Any future split would cross concerns that are currently co-located.
- **`ui/index.html` as a 4,000-line single file.** Tech-debt doc explicitly acknowledges this with three specific items (DOM-manipulation vs. inline-HTML inconsistency, add-form duplication, saga-step form duplication). One bug has already been attributed to the inconsistency.
- **Five completion paths must all trigger the same downstream effects.** Campaign progress, XP distribution, saga bonus checks. Coupling is documented but every new completion path is a new place to get it wrong.
- **Vanilla frontend + Tauri bridge.** Works today. If the UI gains interactive complexity (drag-and-drop across more surfaces, animations beyond current XP flashes), the no-framework choice may start to cost more than it saves.
- **No formal retro mechanism.** Petri has `/retro`; NQ does not. Process improvements flow into `CLAUDE.md` edits directly, which works for a small codebase and active PM but has no structural audit. As the phase count grows, surfacing recurring friction may require more discipline than the current ad-hoc approach provides.

**What can't be determined from this analysis:**
- Whether the `db.rs` monolith is painful to navigate or fine to work with right now.
- Whether the 4,000-line HTML is hitting real debugging pain or just theoretical concerns.
- Whether the quest selector's scoring formula produces the "right" quest in practice (requires using the app).
- How the companion + upstream-project (home-ops) pattern holds up as more upstream projects connect.
- Whether the tone separation between app and companion is working as a self-discipline or starting to blur.

**Overall read.** The structure is serving current goals well, and the PM has a clear sense of where the pressure points are — they're acknowledged in `docs/tech-debt.md` and implied in phase planning. The two-project split (NQ + Companion) is doing real work: it keeps the product narrow while allowing the analytical layer to grow with a different tone, voice, and tooling. The largest open structural question is whether the monoliths (`db.rs`, `index.html`) get deliberately split at the right moment, or whether phase pressure pushes that decision past the right window.

## Where NQ might grow technically

From the roadmap (VISION.md phases 5D and 6):

- **Smart achievements** — auto-generated accomplishments from completion data. Stays within NQ; no new stack.
- **Weather-aware scoring** — first external data dependency. Would introduce a tiny HTTP client for a free weather API. First place where network resilience becomes a concern.
- **Timer as always-on-top overlay** — architectural shift: timer leaves main window, becomes a third window like Encounters.
- **More companion skills** — quest creation from brain dumps (write path), saga breakdown, email/calendar → quest creation. Each extends the CLI-as-API surface.

None of these introduce networking, multi-user state, or LLM-in-the-product. The structural shape stays the same: local app + CLI + companion.

## The word "agent" in NQ's world

As with Petri, "agent" is overloaded:
- **Cowork** (where NQ-Companion runs) is Anthropic's computer-use environment. It's an LLM agent setting.
- **Companion skills** (`nq-daily-plan`, `home-ops-to-nq`) are Claude-Code-style skills that run in Cowork.
- **No runtime agent in NQ itself.** The app has no LLM, no tool-using agent, no chat interface. The quest selector is deterministic scoring math.

When library articles discuss "agents," the relevant case for NQ is almost always **dev-time agents** (Claude Code / Cowork assisting with code) or **companion-time agents** (skills generating reports and imports) — not runtime agents.
