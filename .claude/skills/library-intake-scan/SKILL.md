---
name: library-intake-scan
description: "Weekly scan for new candidate articles and platform feature updates for the meta-claude library. Walks the PM through candidates one at a time during a chat session for promote / drop / defer / discuss, capturing each decision as an append-only entry in `intake-decisions.md`. Designed to be invoked by a scheduled remote agent on Friday noons; can also be run ad-hoc."
---

## Inputs

Read these before scanning:

- **Catalog (source of truth for what's already in the library):** `claude-code-source-library-catalog.md`
- **Decision log (avoid re-surfacing):** `intake-decisions.md`. Newest-first append-only log of past intake decisions (promote / drop / defer). If empty or absent, this is the first run.
- **Historical reviews (read-only reference):** `intake-reviews/`. Old bulk-report files from before P4 (2026-04-26). Preserved for history; do not write new files here.
- **Library curation rules:** see the "Library: scope, schema, and evaluation" section below.

## Target sources

### Tier 1 — Actively checked (8 sources)

Fetch recent content from each directly. Only evaluate pieces published **since the date of the most recent entry in `intake-decisions.md`**. If the log is empty or absent, look back 7 days.

**Date range enforcement:** Do not evaluate content older than the most-recent decision date. For official Anthropic sources (changelog, release notes), read only the entries dated within the past 30 days — stop reading when you hit older entries even if the page continues.

- https://martinfowler.com
- https://hamel.dev
- https://addyosmani.com
- https://simonwillison.net
- https://hillelwayne.com
- https://thedecipherist.com/articles/
- https://claudefa.st/blog
- Official Anthropic sources:
  - https://code.claude.com/docs/en/whats-new
  - https://code.claude.com/docs/en/changelog
  - https://github.com/anthropics/claude-code/releases
  - https://platform.claude.com/docs/en/release-notes/overview
  - https://anthropic.com/news

### Tier 2 — Priority-if-surfaces (6 library-known domains)

Do not fetch these directly. When they appear in Tier 3 broader-search results, flag the match as **priority-signal**: elevated credibility presumption because the library already trusts content from this domain.

- the-ai-corner.com — Ruben Dominguez. **Relevance filter:** only AI-coding practitioner content. His VC/founder-focused content is out of scope for this library.
- stackbuilders.com/insights
- popularaitools.ai/blog
- domainlanguage.com
- danielschleicher.com

### Tier 3 — Broader search

Run queries and evaluate fresh candidates.

**Web search queries** (include current year when relevant):
- "Claude Code best practices"
- "AI pair programming workflow"
- "LLM coding patterns"
- Feature-specific: "Claude Code memory", "Claude Code hooks", "Claude Code subagents", "Claude Code skills", "Claude Code plugins"
- Go-specific: "Go AI coding", "Go Claude Code" (an identified gap in the library)

**GitHub trending:** the `claude-code` topic at https://github.com/topics/claude-code — look for new repositories with substantive README content and community traction.

**Hacker News:** via Algolia HN Search (https://hn.algolia.com). Run topic queries, filter for posts with ≥50 points and substantive comment threads (not just drive-by links).

**Reddit:** search these subreddits for topic queries: r/ClaudeAI, r/LocalLLaMA, r/ExperiencedDevs, r/ChatGPTCoding, r/programming. Filter for upvoted posts (≥100 upvotes) with real discussion.

## Process

1. **Load inputs.** Read catalog, decision log, and (if needed) historical reviews. Apply the evaluation criteria and scope discipline (in "Library: scope, schema, and evaluation" below) at evaluation time. Build a working set of already-known URLs and topics — anything in the catalog, anywhere in `intake-decisions.md`, or in recent historical review files — to de-duplicate against. Also grep `Trend note:` lines from `intake-decisions.md` to build the **active trend watchlist** — forward-looking observations from prior decisions that are open. These become evaluation lenses to bring forward, parallel to the priority-signal mechanism for Tier 2 domains.

2. **Tier 1 scan.** For each actively-checked source, fetch recent content. Note pieces published since the most recent decision in the log.

3. **Tier 3 scan.** Run broader-search queries. When a result's domain matches a Tier 2 library-known domain, tag the result with the **priority-signal** flag.

4. **De-duplicate.** Drop anything already in catalog, decision log, or historical reviews.

5. **Walk the PM through candidates one at a time.** For each new piece:
   - Fetch the content.
   - Write a short paraphrased summary — never reproduce source text, never quote at length, summarize in your own words.
   - Evaluate against the 5 criteria: source credibility, durability, coverage complement (specifically note any catalog entries it overlaps with), practitioner depth, community consensus.
   - Tag topic areas (suggested tags: subagent orchestration, memory patterns, hooks, workflow patterns, DDD, evals, context engineering, testing emergent systems, retros/feedback loops, spec/design, multi-agent coordination, tool use, MCP, language-specialist).
   - Check the candidate against the **active trend watchlist** (built in step 1). If it develops or substantively articulates a watched trend, surface that as a `**Trend match:**` line in the candidate block — positive signal on the coverage-complement criterion (the catalog has been waiting for it). When promotion happens via a trend match, the trend is implicitly closed (the catalog now holds the treatment); no separate closure entry is needed.
   - Surface in chat as a self-contained block:

     > **Candidate — [Title](url)**
     > Source: domain.com (tier-1 / tier-2 priority-signal / tier-3)
     > Summary: ... (paraphrased, 2–4 sentences)
     > Evaluation: credibility / durability / coverage complement / depth / consensus — one line each.
     > Topic tags: ...
     > **Trend match:** *(optional, only if relevant)* Develops the open trend on `<trend>` from `<YYYY-MM-DD entry>`.
     > Recommended: promote | drop | replace `<catalog entry>`
     > Reasoning: 1 sentence.
     >
     > Promote / drop / defer / discuss?

   - Wait for the PM's response.
     - **promote** — append a promote entry to `intake-decisions.md` and add to the catalog (in the section the PM names, or ask if unclear).
     - **drop** — append a drop entry with the reasoning.
     - **defer** — append a defer entry with the reasoning (deferred candidates can be re-surfaced in a future scan).
     - **discuss** — answer the PM's question; once resolved, re-prompt for the action.

   Use the schema in "Decision log entries" below.

6. **Anthropic feature updates.** For new Claude Code / platform features surfaced from Tier 1 official sources, surface to the PM in chat with:
   - What it does (paraphrased).
   - Topic tags.
   - Potential library relevance — does it affect or obsolete existing catalog entries? Note overlap. Do NOT cross-reference project-specific proposal documents.

   Platform-feature decisions go into the decision log too: `## YYYY-MM-DD · platform-update · <feature name>` with PM action (note / promote-doc / catalog-update / defer / drop) and reasoning.

7. **Tier reclassification check.** After all candidate decisions are logged, scan the catalog for any domain now holding ≥2 entries that is not already in Tier 1 or Tier 2, and that hasn't already been the subject of a `tier-no-op` entry in `intake-decisions.md`. For each, surface to the PM:

   > **Domain promotion candidate:** domain.com — now holds N catalog entries (list titles).
   > Current tier: 3. Recommended: tier-2 (priority-if-surfaces) | tier-1 (actively checked).
   > Reasoning: one sentence — tier-1 if the author publishes regularly enough to warrant direct fetching, tier-2 if the signal is "trust the credibility when it surfaces."
   >
   > Promote to tier-2 / promote to tier-1 / leave as-is?

   When the PM promotes, also update the Tier 1 or Tier 2 list in this SKILL.md so the next scan picks up the new tier assignment. This is the only step that mutates the skill itself; the calibration loop closes here. When the PM declines, log a `tier-no-op` entry so the question doesn't re-surface every scan.

   Use the `tier-promote` and `tier-no-op` schemas in "Decision log entries" below.

8. **End-of-scan summary in chat.** After all candidates are walked, surface counts: N evaluated, X promoted / Y dropped / Z deferred / W discussed. Include any tier reclassifications applied. No separate report file is written — the decision log is the durable record.

## Decision log entries

The decision log is a single append-only file at `intake-decisions.md`. Newest-first order: insert new entries at the top of the entries section. The header line (`## YYYY-MM-DD · decision · title`) is grep-able for later analysis (e.g., "show me all drops from claudefa.st").

**Drop:**

```markdown
## 2026-04-26 · drop · [Article Title](url)
**Source:** domain.com (tier-1) · **Recommended:** drop · **Decided:** drop
Reasoning: <one sentence>.
```

**Promote** (include catalog destination):

```markdown
## 2026-04-26 · promote · [Article Title](url)
**Source:** simonwillison.net (tier-1) · **Recommended:** promote · **Decided:** promote · **Catalog:** Principles and Methodologies
Reasoning: <one sentence>.
```

**Defer** (re-surfaceable in a future scan):

```markdown
## 2026-04-26 · defer · [Article Title](url)
**Source:** domain.com (tier-3) · **Recommended:** intake · **Decided:** defer
Reasoning: <one sentence; include the condition under which to re-surface, if any>.
```

**Replace** (when the candidate displaces a catalog entry):

```markdown
## 2026-04-26 · replace · [Article Title](url)
**Source:** domain.com (tier-1) · **Recommended:** replace · **Decided:** replace · **Replaces:** `<catalog entry name>`
Reasoning: <one sentence — what this does better>.
```

**Platform update:**

```markdown
## 2026-04-26 · platform-update · <feature name>
**Source:** code.claude.com (tier-1) · **Decided:** note | promote-doc | catalog-update | defer | drop
Reasoning: <one sentence>.
```

**Tier promote** (domain crosses the catalog-entry threshold and the PM elevates it):

```markdown
## 2026-04-26 · tier-promote · domain.com
**From:** tier-3 · **To:** tier-2
Reasoning: <one sentence — usually citing the catalog entries that triggered the threshold>.
```

**Tier no-op** (PM declines to elevate; prevents the question from re-surfacing every scan):

```markdown
## 2026-04-26 · tier-no-op · domain.com
**Recommended:** promote to tier-2 · **Decided:** leave as tier-3
Reasoning: <one sentence>.
```

If a scan turns up no candidates, append a `no-op` entry so the date is still recorded:

```markdown
## 2026-04-26 · no-op · (scan completed; no new candidates)
```

**Trend note** *(optional field, any entry type):* When a candidate surfaces a forward-looking idea worth watching for in future material — even if the candidate itself doesn't earn promotion — record it inline as a `**Trend note:**` line within the entry. This makes the observation grep-friendly across the log (`grep "Trend note:" intake-decisions.md` surfaces the watchlist) without inflating the decision schema. Use sparingly — only when the trend is concrete enough to recognize again, not for vague "this was interesting."

```markdown
## 2026-04-29 · drop · [Article Title](url)
**Source:** domain.com (tier-1) · **Recommended:** drop · **Decided:** drop
Reasoning: <one sentence>.
**Trend note:** <concrete pattern or angle worth watching for; one or two sentences naming what would earn its own evaluation if it appears more developed elsewhere>.
```

## Library: scope, schema, and evaluation

### Catalog entry schema

- **Title** with link
- **Topics** — 1–3 sentences covering what the piece actually argues or documents
- **Reach for it when** — 1–2 sentences describing the use cases where this piece is the right reference

### Evaluation criteria for promotion

- **Source credibility** — author track record, publishing venue, content substance vs. SEO roundup.
- **Durability** — does the content age fast? Is it feature-specific?
- **Coverage complement** — does it add an angle not already covered, or only repeat? Identify catalog entries that overlap with the candidate, then compare directly:
  - **Resource-type entries** (awesome-lists, drop-in repos, plugins, templates, starter kits) — overlap is at the *resource kind*: lists compare to other lists, starter kits to other starter kits. The catalog stays tight on these. When a 3rd or 4th candidate of the same kind surfaces, treat it as a replacement question: keep best 2 of 3, or best 3 of 4, drop the weakest. Avoid accumulation.
  - **Article-type entries** (methodology, practitioner workflows, framings) — overlap is at the *topic*: candidates addressing the same subject (e.g., spec-driven development, memory consolidation, multi-agent orchestration) get compared. When topical overlap is significant, decide between `replace` and `drop` on **recency** (newer if practices are evolving), **detail** (more substantive treatment), and **credibility** (stronger source). Distinct angles on the same topic can coexist.

  The comparison sharpens the call beyond evaluating the candidate in isolation.
- **Practitioner depth** — substantive patterns vs. marketing.
- **Community consensus** *(optional, not a gate)* — does the piece reflect or meaningfully contribute to emerging consensus practice? A well-reasoned minority view can still earn inclusion; consensus alignment is a confidence multiplier.

### Library scope

- **In scope:** AI-assisted development practices, AI-coding workflows, context engineering, evaluation methodology, agent orchestration, DDD-adjacent thinking on LLM use. Two continuous-surface categories: (a) **new Claude Code and Anthropic platform features** as they ship — how to apply them, what they replace; (b) **emerging community-consensus patterns** — practitioner writing earning adoption beyond its author.
- **Out of scope:** General software engineering knowledge (refactoring, testing theory, language craft). If a gap surfacing in an analysis would need such content, address via per-project skill or reference doc, not by expanding library scope.
- **Specific official sources beyond what's cataloged are fair game.** When an opportunity points clearly at a platform feature, the canonical feature-specific docs on `code.claude.com` or `platform.claude.com` are valid primary sources even if only the top-level docs URL is in the catalog. Specific docs that prove load-bearing should earn an entry in Official Sources.
- **Working-style filter.** The catalog is calibrated to practices applicable to (or readily transferable to) the user's working style — solo, single-contributor projects with the meta-claude analysis-and-library workflow as primary case. Material clearly designed for contexts the user doesn't have (PR review gates, multi-developer coordination, large-org admin tooling) gets noted in the decision log rather than promoted to catalog, even when it's a well-shaped feature doc. The decision log is where "I saw this and it didn't apply" lives; the catalog is where "this is in my toolbox" lives.
- **Noting vs. ignoring platform features.** Only note features that are *plausibly* in-scope but declined for catalog (working-style mismatch, redundancy with existing entries, underdeveloped angle). Do not note pure bug fixes, cosmetic/UI changes, pricing or admin-only changes, or model-version releases — those are tracked at the source and noting them adds audit-trail bloat without dedup value.

### Borderline scope calls

When a candidate sits on the in/out scope edge (general software content that touches AI collaboration, or vice versa), make the call with explicit reasoning rather than silently adding or rejecting. The PM reviews.

## IP discipline

This skill reads third-party web content. When summarizing article content, always paraphrase in your own words. Never reproduce source text, never quote at length, never present source content as original analysis. Summaries should describe the piece's arguments and framings without copying prose. The evaluation (5 criteria, topic tags, recommendation, reasoning) is your own analysis and is fine.

## Scope — what this skill does NOT do

- Does not promote, drop, or defer silently — every action is captured in the decision log with PM confirmation in chat.
- Does not modify the catalog without an explicit PM `promote` or `replace` decision in the same session.
- Does not cross-reference against project-specific proposal documents (`petri-proposals-*.md`, `nq-proposals-*.md`).
- Does not write new files in `intake-reviews/` (the bulk-report flow is retired as of 2026-04-26 per P4).
