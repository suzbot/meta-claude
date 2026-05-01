# Meta-Claude

A methodology workspace where Claude Code reads the AI-collaboration literature on a schedule, maintains a curated library of practices, and translates them into source-backed improvement proposals for other repos.

It's not a software product. It's a system for staying current on how to work well with AI coding agents and figuring out what lessons to apply.

## How it works

Two loops feed each other.

### The intake loop (weekly, scheduled)
1. `/library-intake-scan` walks the user through each new candidate article and platform feature update.
2. Promotions land in [`claude-code-source-library-catalog.md`](claude-code-source-library-catalog.md). *Check this out if you are interested in the list of articles that I think are helpful when thinking about AI collaboration and process!*
3. Every decision with reasoning is logged in [`intake-decisions.md`](intake-decisions.md) as calibration data for future scans.

### The analysis chain

This is run when a target project has accumulated enough pressure to warrant an audit. Five chained skills turn the project into a list of concrete, source-cited improvement proposals.

The proposals become a living document that is revised based on subsequent weekly library intake scans.

## Current state

Meta-claude is undergoing its own self-analysis (the same treatment it gives other projects), with the goal of moving to full automation. Right now it's deliberately manual: the user reviews every checkpoint between skills, and that captured reasoning is the calibration data that will eventually let the chain need less input.

## The five-skill chain

Each skill reads what previous skills wrote, and hands its output to the next. Each ends in a PM checkpoint that captures *what was kept, edited, removed, approved, dropped* and *why*. That captured reasoning is the calibration data that lets the chain anticipate decisions next time.

| # | Skill | Reads | Produces | What changes |
|---|---|---|---|---|
| 1 | `/analyze-methodology` | The project's CLAUDE.md, skills, retros, processes, vision docs | A description of *how* the project is built: its working agreements, conventions, and judgment patterns | A grounded picture of the project's working culture |
| 2 | `/analyze-structure` | Code layout, dependency manifests, architecture docs | A description of *what* the project is technically | A grounded picture of the project's structure |
| 3 | `/opportunity-inventory` | Outputs of #1 and #2 | A list of friction points and gaps worth examining; each is a "lens" to bring to the library scan | The ambiguous "what should we audit?" becomes a finite list |
| 4 | `/map-opportunities` | The inventory + the library catalog + analysis docs from other projects | For each opportunity, the potentially relevant library entries and cross-project patterns, with one-line reasons each. Plus a strategy checkpoint with the PM. | Pattern-matching is exposed; PM steers strategy *before* solutioning |
| 5 | `/draft-proposals` | The mapping doc | One-by-one proposals, each citing both library and cross-project sources, walked through accept / drop / revise with the PM | Concrete change recommendations the project can act on |

## Organization

Each analyzed project has its own subdirectory at the repo root (meta-claude, nq, petri). That's where the artifacts produced by the analysis chain are stored. The skills themselves live in [`.claude/skills/`](.claude/skills/).


## License

CC-BY-4.0. See [`LICENSE`](LICENSE).
