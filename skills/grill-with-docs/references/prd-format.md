# PRD Format

PRDs live in `.scratch/PRD/` and use hyphenated slugs:

```text
.scratch/PRD/<prd-slug>.md
```

The first line must be:

```md
Status: todo
```

## Template

```md
Status: todo

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated quickly.

## Testing Decisions

A list of testing decisions that were made. Include:

- What makes a good test: test external behavior, not implementation details
- Which modules will be tested
- Prior art for the tests, such as similar tests in the codebase

## Out of Scope

A description of the things that are out of scope for this PRD.

## Open Questions

Unresolved questions, risks, or follow-ups that need resolution before or during implementation.
```
