---
name: setup-caveman
description: Enables caveman mode by default for all Claude conversations by writing an instruction into the global ~/.claude/CLAUDE.md. Run once to activate ultra-compressed communication globally. Use when user wants to set up caveman globally, enable terse mode by default, or reduce token usage across all sessions.
disable-model-invocation: true
---

# Setup Caveman

Enable caveman mode globally by writing a communication style instruction into `~/.claude/CLAUDE.md`.

## Process

### 1. Check

Read `~/.claude/CLAUDE.md`. Caveman is already enabled if the file contains any instruction to use caveman / ultra-compressed / terse communication style — look for phrases like "caveman", "ultra-compressed", or "terse like smart caveman".

If already enabled, tell the user and stop.

### 2. Confirm

Ask the user:

> "Want me to enable caveman mode by default for all Claude conversations? It cuts token usage ~75% by dropping filler while keeping full technical accuracy. You can always turn it off with 'stop caveman'."

If the user declines, stop.

### 3. Write

Append the following block to `~/.claude/CLAUDE.md` (create the file if it doesn't exist):

```markdown

## Communication style

Respond terse like smart caveman. All technical substance stay. Only fluff die. See caveman skill for full rules. Off only when user says "stop caveman" or "normal mode".
```

### 4. Done

Tell the user caveman mode is now active for all future conversations.
