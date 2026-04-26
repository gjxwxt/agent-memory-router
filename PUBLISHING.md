# Publishing Guide

This repository is designed to be published as a lightweight skill repository on GitHub. It does not require extra scripts, databases, or hooks.

## Pre-Publish Checklist

Before publishing, verify the following:

1. Each `SKILL.md` can be read on its own without depending on private local files
2. `rules-router` and `project-memory-init` have clear boundaries and do not compete for the same job
3. The repository-level `README.md` explains installation, usage flow, and design boundaries
4. The license is present and matches your publishing intent
5. The repository name, short description, and topics are clear enough for GitHub readers

## Recommended Repository Naming

Examples:

- `agent-memory-router`
- `codex-memory-routing-skills`
- `project-memory-skills`

Current local path:

`F:\F-code\infor\codex-memory-routing-skills`

## Local Git Setup

If you are starting from scratch:

```powershell
cd F:\F-code\infor\codex-memory-routing-skills
git init
git add .
git commit -m "feat: add memory routing skills"
```

## Create the GitHub Repository

1. Create an empty repository on GitHub
2. Prefer using the same name as your local project
3. Choose public or private according to your sharing goal
4. If your local repository already has a `README.md`, do not initialize the remote with a new README

## Connect and Push

```powershell
cd F:\F-code\infor\codex-memory-routing-skills
git remote add origin <your-github-repo-url>
git branch -M main
git push -u origin main
```

Example remote URLs:

- `https://github.com/<your-name>/agent-memory-router.git`
- `git@github.com:<your-name>/agent-memory-router.git`

## Recommended First Release

If you want fast feedback, start with:

`v0.1.0`

That communicates “usable, but still learning from real-world workflows.”

## Good Follow-Ups After First Publish

If you decide to maintain this repository long-term, these are good next additions:

- changelog
- issue templates
- realistic eval prompts
- examples of typed `lessons.md`
- examples of `generic mode` versus `memory mode`

## Maintenance Questions Worth Watching

The most useful things to monitor after publishing are:

- whether `generic mode` stays lightweight enough
- whether the `memory-ready` gate is too strict or too loose
- whether capability -> skill -> fallback resolution feels natural
- whether lesson typing and lesson-to-rule upgrades actually keep memory clean

## One-Line Publishing Strategy

Ship a clear working draft first. Real usage feedback will improve these skills faster than waiting for a “perfect” memory framework.
