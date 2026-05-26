# Skillz

Personal coding, planning, and writing workflows, forked from [mattpocock/skills](https://github.com/mattpocock/skills) and adapted to my workflow.

## What is shared

The `skills/` directory is the source of truth for both agents:

- `setup` creates a shared `AGENTS.md` file in a project and makes `CLAUDE.md` a symlink to it.
- `grill-with-docs` stress-tests a plan against the repo's domain language and docs.
- `engage` picks up a PRD, plans the implementation, and moves it to review.
- `done` is the explicit commit command. It closes the in-review PRD, stages all changes, and commits with the PRD slug.
- `coding-rules` contains mandatory TypeScript/React coding standards.
- `tight-prose` contains the default writing style.

## Codex

Codex reads the plugin manifest from `.codex-plugin/plugin.json` and skills from `skills/`.

Run `setup` once in each project. It writes the reusable repo instructions to `AGENTS.md`, including the compact always-on reminders for `coding-rules` and `tight-prose`.

Codex does not currently use the Claude hook files in this repo. For Codex, persistent enforcement comes from:

- `AGENTS.md` for every-session instructions
- skill descriptions for automatic skill selection
- manually invoking a skill when you want the full workflow

## Claude Code

Claude reads the plugin manifest from `.claude-plugin/plugin.json`, skills from `skills/`, and hooks from `hooks/`.

The Claude hooks are intentionally Claude-only:

- `hooks/reinforce-style` injects the `tight-prose` reminder on every prompt.
- `hooks/inject-checklist` injects the `coding-rules` preflight checklist before code-mutating tools.

`setup` still supports Claude by making `CLAUDE.md` a symlink to `AGENTS.md`, so Claude and Codex share the same project-level context.
