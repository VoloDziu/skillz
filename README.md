# Skillz

Personal coding, planning, and writing workflows, forked from [mattpocock/skills](https://github.com/mattpocock/skills) and adapted to my workflow.

## What is shared

`AGENTS.md` is the source of truth for always-on behavior. The `skills/` directory contains workflow commands:

- `setup` creates a shared `AGENTS.md` file in a project and makes `CLAUDE.md` a symlink to it.
- `grill-me` stress-tests a plan against repo vocabulary, code, and ADRs.
- `engage` picks up a PRD, plans the implementation, and moves it to review.
- `done` is the explicit commit command. It closes the in-review PRD, stages all changes, and commits with the PRD slug.

## Codex

Codex reads the plugin manifest from `.codex-plugin/plugin.json` and skills from `skills/`.

Run `setup` once in each project. It writes reusable repo instructions to `AGENTS.md`, including writing style, coding rules, PRD tracker conventions, shared vocabulary, and decision-record guidance.

For Codex, persistent enforcement comes from:

- `AGENTS.md` for every-session instructions
- skill descriptions for automatic skill selection
- manually invoking a skill when you want the full workflow

Refresh the local Codex plugin cache after changing this repo:

```bash
scripts/update-codex-cache
```

The local Git `post-commit` hook can call this script so committed changes are copied into `~/.codex/plugins/cache/skillz/skillz/1.0.0`.

## Claude Code

Claude reads the plugin manifest from `.claude-plugin/plugin.json` and skills from `skills/`.

`setup` supports Claude by making `CLAUDE.md` a symlink to `AGENTS.md`, so Claude and Codex share the same project-level context.
