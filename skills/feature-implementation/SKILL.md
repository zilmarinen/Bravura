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

## Workflow for a change

1. Read the relevant files before editing.
2. Make the smallest change that solves the task.
3. Run `swift build`, then the relevant `swift test --filter ...`.
4. Fix any errors or warnings you introduced, then run the full `swift test`.
5. Summarize what changed and why, listing the files touched.

## Workflow for a failure

1. Try the most appropriate solution first. Observe and record the result.
2. If the first attempt fails, diagnose the failure and try one meaningfully different approach.
3. Stop on failure. Do not simply repeat the same command, change unrelated code, or make speculative changes.
4. Document the failure. Report the original objective, what was tried, the results of both attempts, and the suspected cause.
5. Preserve the state. Do not undo unrelated working changes, suppress errors, or claim success when the task has not succeeded.
6. Continue safely. If the failed task is part of a larger workflow, continue with independent tasks that do not rely upon its success. Otherwise stop and ask the user how they want to proceed.

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

# Apple Developer Documentation

When implementing, modifying, or debugging code involving Apple frameworks, consult Apple's official developer documentation before making assumptions about API behaviour.

## Documentation requirements

1. Use the Xcode MCP `DocumentationSearch` tool to search Apple's documentation.
2. Prioritise official Apple documentation over model training knowledge or third-party tutorials.
3. Verify API declarations, method signatures, availability and deployment targets.
4. Check whether APIs are deprecated and identify their recommended replacements.
5. When working with AppKit, prioritise documentation for:
   - NSApplication
   - NSWindow and NSWindowController
   - NSView and NSViewController
   - NSDocument and NSDocumentController
   - NSOutlineView and NSTableView
   - NSResponder and the responder chain
   - Auto Layout and NSLayoutConstraint
   - NSMenu, NSMenuItem and target-action
6. When working with UIKit, prioritise the corresponding UIKit documentation.
7. Do not assume AppKit and UIKit APIs behave identically.
8. Use documented APIs and avoid inventing method signatures or properties.
9. If documentation search does not return sufficient information, report the limitation rather than guessing.

## Implementation workflow

Before implementing an unfamiliar Apple framework API:

1. Search Apple's documentation for the relevant symbol.
2. Read the declaration and associated documentation.
3. Check platform and OS availability.
4. Implement using the documented API.
5. Build the project and resolve compiler errors.
6. Consult the documentation again if the implementation behaves unexpectedly.

## Guidelines

- Do not add, remove, or upgrade dependencies without asking first.
- Do not change existing `public` API signatures without asking first.
- Do not delete or skip failing tests to make the suite pass.
- Do not run `git commit`, `git push`, or anything that rewrites history.
- Do not commit directly to `develop` or `main`
- When attempting any task, follow a strict retry limit. Do not repeatedly attempt the same task without making meaningful progress.