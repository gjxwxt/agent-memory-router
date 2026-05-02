---
name: project-memory-init
description: Use when entering a repository for the first time, when project memory is missing or feels unreliable, when resuming after context loss, or when you need to initialize, repair, or audit the project's memory structure. Also use when the user asks to organize AGENTS.md, tasks/index.md, tasks/todo.md, tasks/lessons.md, tasks/knowledge.md, or wants a practical memory scheme inspired by persistent-memory systems but adapted to lightweight daily use. This skill should be used only when the problem is the memory structure itself, not for ordinary task routing.
---

# Project Memory Init

## Role

Initialize or repair a project's memory structure without turning the repo into a documentation dump.

This skill helps you answer:
- what are the memory entrypoints of this project
- what must be always-on versus conditionally loaded
- where should task state, lessons, and long-term knowledge live
- what is missing, duplicated, stale, or misplaced

This skill is about memory structure, not feature implementation.

## When to Use

Use this skill when:
- you enter a repo and need a fast memory map
- the user asks for project memory, memory init, or memory organization
- `AGENTS.md` exists but the surrounding memory system is weak or unclear
- there is confusion between rules, task state, lessons, and knowledge
- you need to resume work after a long gap or context loss
- the user wants a lightweight alternative to fully automatic memory systems

Do not use it for ordinary single-task routing. Use `rules-router` for that.

## Core Philosophy

Borrow the good parts of persistent-memory systems, but keep the implementation lightweight:

- progressive disclosure:
  - index first, details later
- non-invasive:
  - memory should guide work, not hijack it
- structured write-back:
  - different kinds of information go to different destinations
- practical over exhaustive:
  - a small accurate map beats a giant stale knowledge base

## Memory-Ready Definition

By default, treat a project as `memory-ready` only when all of these exist:
- root `AGENTS.md`
- `tasks/index.md`
- `tasks/todo.md`
- `tasks/lessons.md`
- `tasks/knowledge.md`

Until that structure exists, `rules-router` should stay in `generic mode`.

Daily logs (Layer 5) are optional and do not affect memory-ready status.

## Memory Model

Treat project memory as four core layers plus one optional fifth layer:

### Layer 1: Entry

Files that explain where to look next:
- root `AGENTS.md`
- `tasks/index.md`

These files should route, not carry all details.

### Layer 2: Active State

Files that track current execution state:
- `tasks/todo.md`

This is temporary and should change often.

### Layer 3: Experience

Files that preserve reusable collaboration lessons:
- `tasks/lessons.md`

This stores user corrections, repeated pitfalls, and workflow learnings.

### Layer 4: Long-Term Knowledge

Files that preserve durable project facts:
- `tasks/knowledge.md`
- stable project rules docs such as `docs/rules.md`

This layer stores architecture, domain constraints, long-lived decisions, and stable rules.

Distinguish within this layer:
- `tasks/knowledge.md`: facts, decisions, and durable operational knowledge
- stable project rules such as `docs/rules.md`: always-on constraints and process rules

### Layer 5: Daily Logs (Optional)

Files that track daily work and learnings:
- `tasks/daily/YYYY-MM-DD.md`

This layer is **optional** and should NOT be required for memory-ready status.

Purpose:
- help `resume` mode reconstruct recent context
- provide a lightweight reflection habit
- separate "what happened recently" from long-term memory

Structure:
```markdown
# YYYY-MM-DD

## 完成了什么
- [task or milestone]

## 学到了什么
- 建议写回：[lessons/knowledge] — [reason]

## 明天要做什么
- [next step]
```

When to create:
- only if the user explicitly wants daily tracking
- or after 3+ sessions of context loss where recent history would have helped

When to skip:
- if the user prefers lightweight approach
- if the project is short-lived
- if memory-ready is being evaluated (daily logs are not part of the definition)

Guardrails for Daily Logs:
- Daily logs are NOT a second `tasks/todo.md`; do not put task tracking here
- Daily logs are NOT a substitute for `tasks/lessons.md` or `tasks/knowledge.md`; use them only for "what happened today" summaries
- Do not auto-create daily log files; only create when the user explicitly wants daily tracking
- Daily logs should be short (5-10 lines max); if they grow longer, extract reusable items into lessons/knowledge

## Operating Modes

Choose one mode before acting.

### `scan`

Use when first entering a repo.

Goal:
- identify entrypoints
- map memory destinations
- note obvious gaps

Write behavior:
- read only
- do not write or propose edits as if they are already approved

Output:
- a short memory map

### `bootstrap`

Use when the project has weak or missing structure.

Goal:
- propose the smallest useful memory skeleton
- avoid creating unnecessary files

Write behavior:
- may recommend creating missing files or sections
- do not create them unless the user asks or the surrounding workflow explicitly allows it

Output:
- minimal structure recommendation
- whether anything actually needs to be created or just clarified

### `resume`

Use when recovering after time passed or context was lost.

Goal:
- reconstruct where work stopped
- distinguish current task state from durable knowledge

Write behavior:
- do not write back rules, lessons, or knowledge
- at most, note candidate write-backs for later confirmation

Read order:
1. `AGENTS.md`
2. `tasks/index.md`
3. `tasks/todo.md`
4. `tasks/lessons.md` if there were prior corrections
5. `tasks/knowledge.md` if architecture/domain context matters

For `resume`, `tasks/index.md` is part of the default read order, not an optional escalation.
The index is needed to understand what knowledge and lessons exist before deciding which ones to load.

### `repair`

Use when memory exists but is messy.

Look for:
- duplicated authority
- stale task state inside long-term files
- rules mixed with historical notes
- knowledge that should have been lessons
- lessons that should have been rules

Write behavior:
- produce an executable change list
- default to proposal mode, not silent mutation

Output:
- issue list
- recommended moves with exact destinations
- conflict checks before write-back

## Workflow

### 1. Read the minimum structure

Start with:
- root `AGENTS.md`

If the task is more than trivial, read:
- `tasks/index.md`

Only then decide whether to read:
- `tasks/todo.md`
- `tasks/lessons.md`
- `tasks/knowledge.md`
- project rules docs

### 2. Build a Memory Map

Summarize in five parts:
- `entrypoints`: where future sessions should start
- `always_on`: files that are read in most sessions
- `conditional_reads`: files tied to scenarios
- `write_back_targets`: where each kind of information belongs
- `gaps_or_risks`: what is missing, stale, conflicting, or overloaded

### 3. Separate information by lifespan

Use this classification:
- `session-only`: useful now, discard later
- `task-lifetime`: keep until current work finishes
- `project-lifetime`: stable for future tasks

Do not promote short-lived status into long-term knowledge.

### 4. Use the canonical write-back taxonomy

Use this exact taxonomy, matching `rules-router`:

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

### 5. Recommend the smallest correction

Prefer:
- clarifying routing over adding new docs
- moving content to the right file over rewriting everything
- adding an index row over creating another authority source

### 6. Hand off

After memory structure is clear:
- normal task start -> `rules-router`
- feature work -> coding workflow
- bug -> bugfix workflow
- memory conflict -> stay here until classification is resolved

## Lessons Structure

When `tasks/lessons.md` is maintained, prefer typed entries so future routing can load selectively.

Recommended types:
- `gotcha`
- `problem-solution`
- `trade-off`
- `how-it-works`

Meaning:
- `gotcha`: pitfall worth avoiding
- `problem-solution`: recurring issue with a known fix
- `trade-off`: a decision that constrains future choices
- `how-it-works`: compact explanation of a mechanism or workflow

If the file is still untyped, do not force a migration during ordinary work. Just note the improvement opportunity.

## Lesson to Rule Upgrade Standard

In `repair` mode, consider upgrading a lesson into `AGENTS.md` or stable project rules only when most of these are true:
- the same lesson has repeated at least twice
- missing it keeps causing rework, wrong routing, or repeated corrections
- it describes future default behavior, not just a past observation
- it affects task startup, reading order, validation standards, write-back boundaries, or other always-on workflow behavior
- it is stable enough that future contributors should see it before doing work

Keep it in `tasks/lessons.md` when it is still contextual, area-specific, or not yet proven to be always-on.

## Knowledge Summary Convention

If `tasks/knowledge.md` grows, prefer a short summary section near the top:
- list the major knowledge blocks
- say when each block should be read
- point to deeper docs when they are the authority

This summary should route, not duplicate the body.

## Output Templates

### Quick Memory Map

```md
Memory mode: <scan|bootstrap|resume|repair>
Entrypoints: <files>
Always-on: <files>
Conditional reads: <scenario -> file>
Write-back: <todo / lessons / knowledge / rules / session-only>
Risks: <none or short list>
Next: <router or specific workflow>
```

### Repair Recommendation

```md
Problem: <what is mixed up>
Why it hurts: <how it causes routing or memory drift>
Smallest fix: <move/split/clarify>
Conflict check: <needed or not needed>
Executable changes:
- <source -> destination>
- <source -> destination>
```

## Daily Use Guide

- New repo, not sure how to start:
  - run `scan`
- Repo has `AGENTS.md` but people keep forgetting where to write things:
  - run `repair`
- Coming back after several days:
  - run `resume`
- Building a fresh project memory setup:
  - run `bootstrap`

## Guardrails

- Do not create memory files just because a template suggests them.
- Do not duplicate content across `AGENTS.md`, `lessons`, and `knowledge`.
- Do not turn `todo` into a permanent knowledge base.
- Do not write back anything in `resume` mode except optional candidate notes for later confirmation.
- Do not store facts in `lessons` when they are really architecture knowledge.
- Do not store one-off exceptions as global project rules.
- Do not read the whole repo when a routing map is enough.
- Do not let `scan` quietly become `repair`.

## Relationship with `rules-router`

Use this rule:
- `rules-router` decides whether the project is still in `generic mode` or ready for memory-aware routing
- `project-memory-init` designs or repairs the memory structure itself

If both seem relevant:
1. route first with `rules-router`
2. if the blocker is memory structure, switch here
3. once structure is clear, hand back to `rules-router`
