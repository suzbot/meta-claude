# Intake Decisions

Append-only log of intake decisions captured during `/library-intake-scan` sessions. Newest entries at the top. Schema and decision criteria are defined in `.claude/skills/library-intake-scan/SKILL.md` under "Decision log entries."

The header line of each entry — `## YYYY-MM-DD · decision · title` — is grep-able for later analysis (e.g., "show all drops from claudefa.st", "show all platform updates").

Started 2026-04-26.

---

## Entries

<!-- New entries are inserted below this line, newest first -->

## 2026-04-30 · drop · [Anthropic (Jess Yan): Product development in the agentic era](https://claude.com/blog/product-development-in-the-agentic-era)
**Source:** claude.com (tier-1) · **Recommended:** drop · **Decided:** drop · **Captured elsewhere:** `Career/Library.md`
Reasoning: 60% product positioning; the substantive 40% (cloud-native PM-task automation, spec-to-prototype velocity, two-phase discovery+build) is non-novel for the catalog (covered by Addy Long-running Agents, Routines, Factory Model). PM-craft framing is genuinely interesting in a professional context but doesn't add a methodology angle the meta-claude library is missing — captured in the PM's separate career-context library instead.

## 2026-04-30 · drop · [Simon Willison: LLM 0.32a0 is a major backwards-compatible refactor](https://simonwillison.net/2026/Apr/29/llm/)
**Source:** simonwillison.net (tier-1) · **Recommended:** drop · **Decided:** drop
Reasoning: Release post for Willison's `llm` Python library — out of scope (third-party AI-tool-building library, not Claude collaboration methodology). Existing Willison catalog entry (Agentic Engineering Patterns) is the right shape for this library; release notes are not.

## 2026-04-30 · drop · [Claudefa.st: Subscription Safe Use](https://claudefa.st/blog/guide/development/claude-code-subscription)
**Source:** claudefa.st (tier-1) · **Recommended:** drop · **Decided:** drop
Reasoning: Subscription policy compliance content (safe / gray / bannable usage tiers) — useful awareness for a solo Max-sub practitioner running automation, but PM is squarely in the safe tier and the article doesn't compound across reads. Read-once content rather than tools-in-toolbox; drops out of catalog scope (commercial governance, not workflow methodology).
**Trend note:** Two specific patterns worth watching for further articulation: (a) **auth-artifact conflict** — OAuth subscription token and `ANTHROPIC_API_KEY` both present in environment can silently bill the wrong account; verify via rate-limit fields in API responses. (b) **Pattern-based enforcement signal** — sustained non-human-paced automation (e.g., a fast-cron `/loop` running for hours) can look botlike to enforcement even when usage is single-user and benign. If either gets a substantive practitioner treatment that goes beyond "be careful," that earns its own catalog evaluation.

## 2026-04-30 · promote · [Adam Tornhill: How Long Should a Function Be? (And Why It's the Wrong Question to Ask)](https://adamtornhill.substack.com/p/how-long-should-a-function-be-and)
**Source:** adamtornhill.substack.com (tier-3, surfaced via Fowler Fragments April 29) · **Recommended:** discuss · **Decided:** promote · **Catalog:** Principles and Methodologies
Reasoning: Opens a code-as-context angle the catalog didn't previously hold — argues code structure functions as feedforward agent context, backed by a 5,000-Python-file empirical study (Tornhill et al., "Code for Machines, Not Just Humans") correlating CodeHealth with semantic preservation after LLM refactoring. PM flagged downstream use: refactor proposals justified on agent-comprehension grounds.

## 2026-04-30 · drop · [Chris Parsons: Coding with AI](https://www.chrismdp.com/coding-with-ai/)
**Source:** chrismdp.com (tier-3, surfaced via Fowler Fragments April 29) · **Recommended:** discuss · **Decided:** drop
Reasoning: Verification-bottleneck and CI-fundamentals framings already substantively held by Addy Factory Model (first-principles development of generation-vs-verification), Fowler Harness Engineering (computational-sensors framing of CI/tests/lint), and Simon Willison Agentic Engineering Patterns (vibe-vs-agentic distinction). Parsons is rhetorically tighter but doesn't out-substance any of the three; CI-as-halt-condition lever is already implied by the existing entries — no new operational mechanism to drive a project proposal.

## 2026-04-29 · drop · [thedotmack: claude-mem](https://github.com/thedotmack/claude-mem)
**Source:** github.com (tier-3) · **Recommended:** discuss · **Decided:** drop
Reasoning: As a tool, redundant with Auto Dream's built-in memory consolidation for solo working style. Philosophy/principles docs apply established HCI work (Cognitive Load Theory, Information Foraging, Progressive Disclosure NN/g) to RAG-specific problems and explicitly claim novelty in application rather than in principle — so for a reader familiar with existing context-engineering work, ~80% is restatement, dressed in tool-specific examples (MCP search/timeline/get_observations API, observation IDs, icon legend), and the doc structure is implicitly tool-marketing.
**Trend note:** Visible retrieval costs in indices — surfacing per-item token costs ("~155 tokens to fetch") alongside metadata so agents make ROI-based fetch decisions, not just relevance-based. Worth watching for further articulation; if a substantive treatment appears in a non-tool-marketing context, that earns its own catalog evaluation.

## 2026-04-29 · promote · [hesreallyhim: awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code)
**Source:** github.com (tier-3, GitHub trending) · **Recommended:** catalog-update · **Decided:** promote · **Catalog:** Resource-type entries (after VoltAgent)
Reasoning: Adds the missing ecosystem-wide directory the catalog lacked — VoltAgent covers subagent-specific deep dives, hesreallyhim covers the broader index across skills/hooks/commands/orchestrators/plugins. Curatorial substance (stated quality criteria, executable tooling, structured data) puts it above flat-list-only candidates. Compared against existing list-type entries in catalog and judged complementary rather than redundant.

## 2026-04-29 · drop · [Go LSP (gopls) — Claude Plugin](https://claude.com/plugins/gopls-lsp)
**Source:** claude.com (tier-1) · **Recommended:** discuss · **Decided:** drop
Reasoning: Plugin shipped 2025-12-19, well outside the scan window — surfaced via the Go gap-targeted Tier 3 query, not as new content. Page only conveys "this exists, install it" with no methodology insight on how it changes process; user has already installed it in Petri. Would have been a latest-updates candidate at release and aged into a drop by now.

## 2026-04-29 · platform-update · /ultrareview enters public research preview
**Source:** code.claude.com (tier-1) · **Recommended:** catalog-update · **Decided:** note
Reasoning: Plausibly in-scope (sibling shape to cataloged Routines / Monitor / Ultraplan entries), but its primary use case is pre-merge verification on team PRs — doesn't fit the user's solo single-contributor working style. First decision under the new working-style filter added to SKILL.md this session.

## 2026-04-29 · drop · [Anthropic Engineering: An update on recent Claude Code quality reports](https://www.anthropic.com/engineering/april-23-postmortem)
**Source:** anthropic.com (tier-1) · **Recommended:** promote · **Decided:** drop
Reasoning: Out of scope for what the catalog is trying to accomplish — incident reporting, not practitioner-applicable methodology. Doesn't translate into actionable practice for working around quality regressions, only documents what happened. Note: surfaced as a borderline-window candidate (Apr 23, one day before the Friday cutoff).

## 2026-04-29 · drop · [Marmelab (Caroline Schneider): Claude Code Tips I Wish I'd Had From Day One](https://marmelab.com/blog/2026/04/24/claude-code-tips-i-wish-id-had-from-day-one.html)
**Source:** marmelab.com (tier-3) · **Recommended:** drop · **Decided:** drop
Reasoning: Roundup format with mostly-conventional advice; the distinctive contributions (don't-fix-bugs-yourself discipline, session-retrospective knowledge routing, parallelization-as-illusion critique) are sharp but not enough on their own to anchor a catalog entry next to existing practitioner-workflow voices that articulate similar ideas.

## 2026-04-29 · promote · [Addy Osmani: Long-running Agents](https://addyosmani.com/blog/long-running-agents/)
**Source:** addyosmani.com (tier-1) · **Recommended:** promote · **Decided:** promote · **Catalog:** Principles and Methodologies
Reasoning: Genuinely new territory for the catalog — durability and resumption mechanics for agents operating across days/weeks, with four named architectures (Ralph Loop, Brain/Hands/Session, Planner/Worker/Judge, Memory Bank) the catalog didn't previously cover.

## 2026-04-29 · promote · [Boris Tane: How I Use Claude Code](https://boristane.com/blog/how-i-use-claude-code/)
**Source:** boristane.com (tier-3) · **Recommended:** promote · **Decided:** promote · **Catalog:** Principles and Methodologies
Reasoning: Practitioner-substantive day-to-day workflow with two patterns the catalog didn't crisply name — annotation cycles as the human-judgment injection point, and revert-and-re-scope rather than incrementally patch. Adds a sharper third voice next to PopularAiTools and AI Corner in a category the catalog was thin on.

## 2026-04-29 · drop · [Boris Tane: The Software Development Lifecycle Is Dead](https://boristane.com/blog/the-software-development-lifecycle-is-dead/)
**Source:** boristane.com (tier-3) · **Recommended:** drop · **Decided:** drop
Reasoning: The "process collapses into a tight agent loop" thesis is already covered more rigorously by Addy's Factory Model and Fowler's Reduce Friction hub; the genuinely novel angle (observability/telemetry as closed-loop agent context) is only a paragraph here, too underdeveloped to anchor a catalog entry.
**Trend note:** Telemetry/observability as closed-loop context for the agent that shipped the code — production runtime data feeding back to the agent for automatic remediation rather than human incident response. Worth watching for further articulation; if a substantive treatment appears, that earns its own catalog evaluation.

## 2026-04-29 · drop · [Boris Tane: Slop Creep — The Great Enshittification of Software](https://boristane.com/blog/slop-creep-enshittification-of-software/)
**Source:** boristane.com (tier-3) · **Recommended:** drop · **Decided:** drop
Reasoning: Prescriptive workflow (research-plan-implement with engineer annotation rounds) overlaps heavily with ~5 existing spec-driven entries (Fowler Design-First / Context Anchoring, Addy Good Spec, Schleicher Spec-Driven, thedecipherist MDD); the sticky vocabulary ("slop creep," "one-way doors") wasn't enough on its own to earn a slot in an already-dense category.

## 2026-04-28 · promote · [Fowler: Structured-Prompt-Driven Development (SPDD)](https://martinfowler.com/articles/structured-prompt-driven/)
**Source:** martinfowler.com (tier-1) · **Recommended:** promote · **Decided:** promote · **Catalog:** Principles and Methodologies
Reasoning: Distinct angle on spec-driven work — treats prompts as version-controlled artifacts kept in sync with code, with a named seven-part scaffold (REASONS Canvas) and an explicit fitness assessment that pairs cleanly with existing Schleicher / Addy / Fowler entries.
