---
name: feature-implementation
description: Implement, refactor, and test Swift features while preserving existing project architecture and coding conventions. Use when adding functionality or changing existing Swift code.
---

# Swift Feature Implementation

## Objective

Implement small, correct, maintainable Swift changes
that integrate with the existing project.

## Before changing code

1. Inspect the relevant source files and tests.
2. Identify the existing types, protocols, and abstractions.
3. Check deployment targets and supported Swift versions.
4. Understand the current architecture before proposing a replacement.
5. Identify edge cases and required tests.

## Implementation rules

1. Prefer the smallest change that correctly solves the task.
2. Follow existing naming, formatting, and access-control conventions.
3. Prefer strong typing and value semantics where appropriate.
4. Avoid unnecessary dependencies, abstractions, and duplicated logic.
5. Preserve existing public APIs unless a change is requested.
6. Do not modify unrelated files.
7. Do not introduce SwiftUI into AppKit-only projects.

## Testing

1. Add or update tests for the changed behaviour.
2. Cover normal cases, boundary conditions, and invalid inputs.
3. Run the relevant tests using the project's existing workflow.
4. Build the affected target when possible.
5. Never claim a build or test succeeded without actually
   running it and checking the result.

## Completion report

Summarise:

- What changed and why.
- Files modified.
- Tests and builds executed.
- Any failures, assumptions, or remaining limitations.
