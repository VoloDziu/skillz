---
name: setup
description: Create or update repo agent instructions. Use only when the user explicitly invokes `/setup` or directly asks to run the setup skill. Writes a fixed `## Agent skills` block into AGENTS.md and makes CLAUDE.md a symlink to it.
---

# Setup Skills

Create a shared repo instruction file.

## Process

### 1. Inspect

Check the repo root:

- Does `AGENTS.md` exist?
- Does `CLAUDE.md` exist?
- Is `CLAUDE.md` already a symlink to `AGENTS.md`?
- Does either file already contain an `## Agent skills` section?

### 2. Write

Use `AGENTS.md` as the source file. Create it if needed.

If `AGENTS.md` does not exist and `CLAUDE.md` is a regular file, move the `CLAUDE.md` contents into `AGENTS.md` first. Preserve non-agent content.

Add or replace this block. Do not append a duplicate section.

```markdown
## Agent skills

### Default writing style

Use the `tight-prose` skill as the default response style.

### Code editing rules

Before writing or modifying TypeScript or React code, use the `coding-rules` skill. Treat it as mandatory.

### PRD tracker

PRDs live as files under `.scratch/PRD/`:

- PRD: `.scratch/PRD/<prd-slug>.md`
- Track status with a `Status:` line at the very top of the file: `todo`, `in review`, or `done`

### Domain docs

Read `CONTEXT.md` at the repo root before exploring the codebase. Read any ADRs in `docs/adr/` that touch the area you're working in. If these files don't exist, proceed silently. Use the vocabulary from `CONTEXT.md` exactly; flag any contradiction with an existing ADR rather than silently overriding it.
```

### 3. Link

Make `CLAUDE.md` a relative symlink to `AGENTS.md`:

```bash
ln -s AGENTS.md CLAUDE.md
```

If `CLAUDE.md` already exists as a symlink to `AGENTS.md`, leave it alone.

If `CLAUDE.md` exists as a regular file, only replace it with the symlink after its contents have been migrated into `AGENTS.md`.

### 4. Done

Report that setup is complete and that `CLAUDE.md` points to `AGENTS.md`.
