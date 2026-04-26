# Agent Memory Router

[English](./README.md) | [简体中文](./README.zh-CN.md)

Lightweight skills for Codex / Claude Code style workflows that separate task routing from project memory structure.

This repository is not trying to build a fully automatic memory system. It is designed for teams and solo developers who want a memory workflow that is:

- inspectable
- versionable
- predictable
- easy to adopt incrementally

## Included Skills

- [`rules-router`](./skills/rules-router/SKILL.md)
- [`project-memory-init`](./skills/project-memory-init/SKILL.md)

## Why These Skills Exist

Many projects do not need a full memory stack, but almost every project benefits from a reliable way to decide:

- what context to load
- what context to skip
- what workflow should take over next
- where new knowledge should be written back

This repository splits those concerns into two layers:

- `rules-router`
  - routes work before execution starts
  - classifies intent, complexity, context budget, and memory readiness
  - resolves a capability first, then maps it to an installed skill or a built-in fallback

- `project-memory-init`
  - initializes, repairs, resumes, or audits project memory structure
  - helps teams separate task state, lessons, long-term knowledge, and stable rules
  - does not replace ordinary day-to-day task routing

## Core Design Principles

- Load the smallest context set that supports a correct next step
- Do not assume every project needs memory-aware routing
- Prefer capability routing over hardcoded skill names
- Fall back gracefully when a preferred skill is not installed
- Keep task state, lessons, knowledge, and rules in separate lanes

## Repository Layout

```text
agent-memory-router/
├── LICENSE
├── PUBLISHING.md
├── README.md
├── README.zh-CN.md
└── skills/
    ├── project-memory-init/
    │   └── SKILL.md
    └── rules-router/
        └── SKILL.md
```

## How the Two Skills Work Together

### `rules-router`

Use it when you need to decide:

- which rules or docs to read before starting work
- whether the task is coding, bugfix, review, docs, or brainstorming
- whether project memory is initialized enough to participate in memory-aware routing
- where to store a new correction, lesson, rule, or long-term fact
- how to continue when a preferred skill is missing

It operates in two modes:

- `generic mode`
  - for projects that do not yet have the expected memory structure
- `memory mode`
  - only after the project has `AGENTS.md`, `tasks/index.md`, `tasks/todo.md`, `tasks/lessons.md`, and `tasks/knowledge.md`

### `project-memory-init`

Use it when the memory structure itself is the problem:

- first time in a repository
- missing or unreliable project memory
- resuming after context loss
- repairing drift between rules, task state, lessons, and knowledge

It supports four modes:

- `scan`
- `bootstrap`
- `resume`
- `repair`

## Installation

Copy the two skill folders into your local skill search path.

Common locations include:

- `~/.agents/skills/`
- `~/.codex/skills/`

Example:

```powershell
Copy-Item .\skills\rules-router "$HOME\.agents\skills\rules-router" -Recurse -Force
Copy-Item .\skills\project-memory-init "$HOME\.agents\skills\project-memory-init" -Recurse -Force
```

If your environment uses a different skill directory convention, copy the two subdirectories to the equivalent location.

## Recommended Usage Flow

1. Enter a new or unfamiliar repository
   - start with `rules-router`
   - decide whether `generic mode` is enough or whether to run `project-memory-init`
2. The project does not yet have memory structure
   - use `project-memory-init` with `scan` or `bootstrap`
3. The project now has a stable structure
   - let `rules-router` handle daily routing
4. The structure starts drifting
   - use `project-memory-init` with `repair`

## Shared Write-Back Taxonomy

Both skills use the same write-back categories:

- `session-only`
- `tasks/todo.md`
- `tasks/lessons.md`
- `tasks/knowledge.md`
- `AGENTS.md` or stable project rules such as `docs/rules.md`

Guiding rules:

- facts go to `tasks/knowledge.md`
- behavior constraints go to `tasks/lessons.md` or stable rules
- always-on workflow behavior belongs in `AGENTS.md` or stable rules
- task-local execution state stays in `tasks/todo.md`

## Who This Is For

This repository is a good fit for:

- engineering teams with explicit collaboration rules
- projects with long-lived component standards, quality gates, or shared conventions
- people who care about why a context source was loaded and why a piece of information was written back

If you want a fully automatic memory layer with semantic retrieval and minimal human involvement, this repository is intentionally not optimized for that model.

## Feedback That Matters Most

The most useful real-world feedback usually looks like this:

- routing feels too heavy
- write-back categories are still confusing
- fallback behavior is not smooth enough
- some projects should stay in `generic mode` longer
- lesson-to-rule upgrades are either too eager or too conservative

## License

This repository is released under the [MIT License](./LICENSE).
