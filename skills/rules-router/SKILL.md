---
name: rules-router
description: Use whenever a request involves AGENTS.md, rules loading, docs discovery, complexity classification, skill routing, or deciding what context to read before work starts. Also use when the user adds a new rule, preference, lesson, or long-term knowledge, or when you need to decide whether something belongs in tasks/todo.md, tasks/lessons.md, tasks/knowledge.md, project rules, or only this session. Use this as the default entrypoint before coding, bugfixing, reviewing, documenting, or saving memory whenever there is any doubt about what to load, whether project memory is initialized, or how to route to installed skills versus built-in fallback behavior.
---

# Rules Router

## Role

Route work before substantive execution starts.

This skill decides:

- what must be read now
- what should be deferred
- which capability should take over next
- whether a named skill is available for that capability
- whether new information should be written back, and where

This skill is a router, not the final worker. After routing, hand off quickly.

## When to Use

Use for requests about:

- global rules, project rules, `AGENTS.md`, memory, docs discovery, or skill distribution
- deciding whether a task is simple, medium, or complex
- choosing between coding, bugfix, review, docs, QA, planning, or brainstorming workflows
- saving new long-term rules or project knowledge
- resolving conflicts between new instructions and existing rules
- resuming work after context loss or a previous session
- deciding whether to read `tasks/index.md`, `docs/rules.md`, `tasks/lessons.md`, or `tasks/knowledge.md`
- deciding whether to skip loading more context because the task is still simple
- deciding whether project memory is initialized enough to participate in memory-aware routing
- deciding how to route when a preferred skill is not installed

Do not use it as a heavy design process for ordinary feature ideas; use `brainstorming` when the main problem is product/design discovery.

## Core Principle

Load the smallest context set that can support a correct next step.

Do not front-load everything.
Do not confuse "available" with "necessary".
Prefer index first, detail later.

## Activation Gate

Before memory-aware routing, check whether the project memory structure is ready.

By default, treat the project as `memory-ready` only when all of these exist:

- root `AGENTS.md`
- `tasks/index.md`
- `tasks/todo.md`
- `tasks/lessons.md`
- `tasks/knowledge.md`

If any of these are missing:

- stay in `generic mode`
- do not perform memory-aware distribution
- do not recommend write-back into project memory files unless the user explicitly asks
- do not pretend the memory structure exists

If the user explicitly wants project memory and the structure is missing:

- hand off to `project-memory-init`
- use `scan` when a structure may exist but is unclear
- use `bootstrap` when the structure does not exist yet

## Routing Flow

### 1. Respect active instructions first

Apply in this order:

1. system and platform constraints
2. current user instructions
3. active project rules and project memory
4. global rules and workflow rules
5. scenario rules

### 2. Read the minimum always-on context

Start with the smallest index available:

- global: `C:\Users\gaoji\.codex\rules\INDEX.md`
- project: root `AGENTS.md` if present

Read more only when the task warrants it.

### 3. Select routing mode

- `generic mode`:
  - use when project memory is not initialized
  - classify intent and complexity
  - route to a capability or built-in workflow
  - avoid project-memory write-back advice
- `memory mode`:
  - use only when the activation gate passes
  - enable project-specific reads and write-back decisions
  - selectively load `tasks/*` and project rules

### 4. Classify the user's intent

Pick one primary intent:

- `explore`: understand current code, docs, or context
- `decide`: compare options, choose direction, clarify constraints
- `execute`: write or change code/config/skill files
- `review`: inspect changes and report findings
- `document`: write or reorganize docs/knowledge/rules
- `memory-maintain`: initialize, repair, or save project memory

Do not mix intents unless the user explicitly asks for combined behavior.

### 5. Classify task complexity

- `simple`: single location, low risk, no public API, no docs/story/test sync, no memory/rule change
- `medium`: multiple files or steps, behavior changes, tests/docs/story impact, or any memory/rule change
- `complex`: cross-module, release/CI, architecture, org-wide workflow, or long-lived process change

If the task touches public API, component behavior, tests/Storybook/docs synchronization, release/CI, architecture, rules, workflow, or long-term memory, treat it as at least `medium`.

### 6. Decide what extra context to load

In `generic mode`, stop after the minimum context unless the user explicitly asks for deeper project inspection.

In `memory mode`, use this decision table:

- Always:
  - root `AGENTS.md`
- For medium+ project tasks:
  - `tasks/index.md`
- For component-library rules, Storybook, release, or verification:
  - `docs/rules.md`
- For bug reports, user corrections, repeated pitfalls, or "remember this next time":
  - `tasks/lessons.md`
- For architecture, long-term domain constraints, or reusable project facts:
  - `tasks/knowledge.md`
- For current multi-step task state:
  - `tasks/todo.md`

Skip deeper reads when the next step is already clear.

If `tasks/knowledge.md` has a summary section, read that before chasing linked details in `docs/*.md`.

If `tasks/lessons.md` uses typed entries, prefer loading only the relevant types:

- `gotcha`: recurring pitfalls
- `problem-solution`: repeated issue and known fix
- `trade-off`: decision constraints and reasons
- `how-it-works`: compact operational explanation

### 7. Resolve the next capability

Route to a capability first, not a named skill.

Preferred capability labels:

- `coding`
- `bugfix`
- `review`
- `docs`
- `brainstorming`
- `memory-init`
- `memory-repair`

### 8. Resolve capability to skill or fallback

Use this order:

1. session override
2. project override
3. global override
4. installed matching skill
5. built-in fallback behavior

Rules:

- only route to a named skill if it is actually available in the current environment
- if the user says "default to skill X for capability Y", treat that as an override
- if the preferred skill is not installed, say so briefly and continue with built-in fallback
- do not block the task just because a preferred skill is missing

Built-in fallback behavior should stay simple:

- `coding`: follow the active coding rules directly
- `bugfix`: follow the active bugfix/investigation rules directly
- `review`: perform a review-only pass
- `docs`: perform docs-only work if explicitly requested
- `brainstorming`: do lightweight option exploration
- `memory-init` or `memory-repair`: route to `project-memory-init` if available, otherwise explain the missing capability and fall back to manual structure analysis

### 9. Decide write-back destination

Use this canonical taxonomy in both routing and memory skills:

- `session-only`:
  - temporary instruction, one-off exception, local experiment, or information that does not deserve persistence
- `tasks/todo.md`:
  - current task status, next steps, temporary execution tracking
- `tasks/lessons.md`:
  - user correction, repeated pitfall, collaboration rule learned from experience, or reusable implementation lesson
- `tasks/knowledge.md`:
  - long-lived project fact, architecture constraint, domain decision, or stable operational knowledge
- `AGENTS.md` or stable project rules such as `docs/rules.md`:
  - stable routing rule, process rule, or always-on constraint that future work should start with

If something overlaps:

- facts go to `tasks/knowledge.md`
- behavior constraints go to `tasks/lessons.md` or stable project rules
- always-on behavior belongs in `AGENTS.md` or stable project rules
- task-local status stays in `tasks/todo.md`

## Write-Back Gate

Before writing memory, ask:

1. Will this still matter later?
2. Is it reusable across more than one task?
3. Can it be derived directly from code or existing docs?
4. Is the destination specific enough?
5. Is it semantically a fact, a behavior rule, or a temporary state?

If any answer is unclear, keep it out of long-term memory for now.

## Conflict Protocol

When the user introduces a new rule or memory candidate:

1. Classify the destination: global rule, project rule, lesson, knowledge, task state, or session-only instruction.
2. Check likely existing sources before writing.
3. If it conflicts, ask the user to choose: overwrite, record as exception, use only this session, or do not write.
4. Never silently replace an existing rule or broaden a local preference into a global rule.

Treat conflicts as:

- `hard conflict`: old and new cannot both be true
- `soft conflict`: new rule narrows an existing one
- `scope conflict`: content is fine but destination is wrong

## Output Contract

For medium+ tasks, output a short routing block before substantive work:

```md
Routing: <primary intent + complexity + generic|memory mode>
Read: <files/rules to load now>
Skip: <what you are intentionally not loading yet>
Next: <capability + named skill or built-in fallback>
Write-back: <none / todo / lessons / knowledge / rules>
Status: <DONE | DONE_WITH_CONCERNS | NEEDS_CONTEXT | BLOCKED>
```

For simple tasks, compress this to one sentence and proceed.

## Status Vocabulary

Use these only when they add signal:

- `DONE`: routing is clear and execution can start
- `DONE_WITH_CONCERNS`: routing is clear but you are carrying a known risk, assumption, or degraded fallback
- `NEEDS_CONTEXT`: one specific file, answer, or project fact is still missing
- `BLOCKED`: there is a hard conflict, missing required entrypoint for the requested mode, or missing capability that cannot be safely replaced

## Daily Use Heuristics

- First time in repo:
  - enter `generic mode`, then decide whether `project-memory-init` is needed
- Starting a normal task:
  - route, then hand off quickly
- After user says "记住这个":
  - route the destination before writing
- After context loss:
  - route whether to read `todo`, `lessons`, `knowledge`, or run `project-memory-init`
- During simple code edits:
  - keep the route tiny and avoid ceremony

## Guardrails

- Do not turn routing into a long planning ritual.
- Do not read every document just because it exists.
- Do not silently upgrade a project into memory mode when the required files are missing.
- Do not save temporary task state as long-term knowledge.
- Do not perform implementation and review at the same time unless the user explicitly wants both.
- Do not write new rules when a session-only constraint is enough.
- Do not hardcode capability routing to a named skill when built-in fallback is sufficient.
