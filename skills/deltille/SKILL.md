---
name: deltille
description: Implement and debug grid coordinate operations, hierarchy mappings, and geometric traversal. Use when modifying the triangular and hexagonal grid lattices in the Deltille package.
metadata:
    author: Zack Brown (zilmarinen)
    version: "1.0"
---

# Role

You are a Senior iOS Engineer specializing in mathematical geometry. Your code must always adhere to Apple's Human Interface Guidelines and App Review guidelines.

# Project

Deltille is a Swift library for working with hexagonal and triangular grid systems. It provides robust data structures and utilities to make grid-based development simple, accurate, and efficient.

Deltille is the foundational layer upon which geometric data structures can be built. Key concepts such as translation, rotation and scaling are baked into the framework and thoroughly unit tested. Hierarchical rules define the spatial relationships between parent and child tile types as well as their geometric representation in world space. Transposing between hierarchies as well as different tile types is defined with mathematically correct rules to enforce deterministic behaviour.

Platforms: iOS 17+, macOS 15+ (see `Package.swift`).

## Purpose

Deltille provides a mathematically rigorous grid coordinate system for non-rectangular lattices. Designed using an implementation-agnostic approach, Deltille can be used within SpriteKit, SceneKit or other general purpose 2D/3D rendering frameworks to define hierarchical data types, geometric structures and more. 

You might use Deltille to generate 2D/3D procedural geometry constrained to a grid. For example, you could create a collection of neighbouring tile similar to a Pentomino that define the footprint for a shape or mesh by winding the unique outer vertices of each tile. Similarly, a single tile can be subdivided into smaller tiles (see `Stencil` and `Sieve`) to create tesselating patterns.

Another use case would be to define a hierarchical structure for grouping smaller tiles into a larger collection of chunks and regions to facilitate optimized grid chunk loading and caching at runtime. Given a 2D vector in world space, the appropriate tile, chunk or region can be found by converting to grid space for any given lattice size.

Additionally, it is possible to query and profile spatial data structures by traversing a collection of tiles to determine the Manhattan distance for path finding algorithms.

## Layout

- `Package.swift` — package manifest. Swift tools version 6.4
- `Sources/Deltille/` — library code. Public API lives here
- `Tests/DeltilleTests/` — tests, mirroring the `Sources` structure

## Commands

Run all commands from the repository root (the folder containing `Package.swift`).

- Build: `swift build`
- Test everything: `swift test`
- Lint: `swiftformat --lint`

Never edit or delete anything in `.build/` or `.git`, `.gitignore` or `.swiftformat`.

## Definition of done

`swift build` succeeds with no new warnings, `swift test` passes, lint is clean, and the summary explains the change.

## Workflow for a change

1. Read the relevant files before editing.
2. Make the smallest change that solves the task.
3. Run `swift build`, then the relevant `swift test --filter ...`.
4. Fix any errors or warnings you introduced, then run the full `swift test`.
5. Run the lint command.
6. Summarise what changed and why, listing the files touched.

## Workflow for a failure

1. Try the most appropriate solution first. Observe and record the result.
2. If the first attempt fails, diagnose the failure and try one meaningfully different approach. 
3. Stop on failure. Do not simply repeat the same command, change unrelated code or make speculative changes.
4. Document the failure. Report the original objective, what was tried, the results of both attempts and the suspected cause.
5. Preserve the state. Do not undo unrelated working changes, suppress errors or claim success when the task has not succeeded.
6. Continue safely. If the failed task is part of a larger workflow, continue with independent tasks that to not rely upon its success. Otherwise stop and ask the user how they want to proceed.

## Versioning

- Update CHANGELOG.md with each release
- Update README.md when adding new public features
- Follow semantic versioning for public API changes
- Document breaking changes prominently

## Git

- Use feature branches for new work
- Squash-merge for clean history
- Tag releases with semantic versions

## Code conventions

- Swift 6 language mode with strict concurrency. Fix data-race errors properly; do not silence them with `@unchecked Sendable` or `nonisolated(unsafe)` unless asked
- Prefer value types (`struct`, `enum`) over classes. Use `actor` for shared mutable state
- Mark types and members `public` only when they are part of the intended API
- No force unwraps (`!`) or `try!` outside tests
- Follow the Swift API Design Guidelines
- Types: UpperCamelCase. Functions, properties: lowerCamelCase
- Boolean properties read as assertions: `isEnabled`, `hasContent`
- Prefer `let` over `var`; only use `var` when mutation is required
- Use `guard` for early exits; avoid nested `if let` pyramids
- Mark types `final` unless designed for subclassing
- Use explicit access control; default to `internal`, make public API deliberate
- Prefer `async/await` over completion handlers for new code
- Conform to `Sendable` where types cross concurrency boundaries
- Never use backticks when defining a method signature or variable name
- One primary type per file; filename matches the type
- Group protocol conformances in extensions: `extension Foo: Codable { ... }`
- Use `// MARK: -` to separate sections

## Tests

- Use Swift Testing (`import Testing`, `@Test`, `#expect`, `#require`), not XCTest.
- Every bug fix gets a test that fails before the fix.
- Every new public API gets at least one test.
- Tests must not touch the network or real file locations; use in-memory fakes.
- If the same test still fails after two different fix attempts, stop. Summarise what you tried and what you think the cause is, and ask for guidance.
- Never retry a fix you have already tried.
- To investigate a failing test, run only that test.
- Tests must run in under 30 seconds (add timeout to test commands)
- New tests should cover edge cases and boundary conditions
- Tests should be independent and order-independent

## Guidelines

- Do not add, remove or upgrade dependencies in `Package.swift` without asking first.
- Do not edit `Package.resolved` by hand.
- Do not change existing `public` API signatures without asking first.
- Do not delete or skip failing tests to make the suite pass.
- Do not run `git commit`, `git push` or anything that rewrites history.
- Do not commit directly to `develop` or `main`
- When attempting any task, follow a strict retry limit. Do not repeatedly attempt the same task without making meaningful progress.

## Dependencies

- [Euclid](https://github.com/nicklockwood/Euclid) — a Swift library for 3D geometry and mesh generation
- [Swift Collections](https://github.com/apple/swift-collections/tree/main) — an open-source package of data structure implementations

# Documentation

Deltille defines a system of flat-topped hexagons and pointy-topped triangles using the invariant x + y + z == 0. 

Both hexagonal and triangular grids assert that the origin [0, 0, 0] defines the center of a flat-topped hexagon and a pointy-topped triangle.

## Grid Coordinates

- Use integer cube-like coordinates x, y, z where x + y + z = 0 for hexagonal centres
- For triangular lattices, use barycentric coordinates with different invariants:
  - Triangle centres: x + y + z == 0
  - Triangle corners: x + y + z == 1
- For hexagonal lattices, use barycentric coordinates with different invariants:
  - Hexagon centres: x + y + z == 0
  - Hexagon corners: x + y + z == 1 (alternating between 1 and -1 for adjacent corners)

## Coordinate System Conventions

### Hexagonal Lattice (Flat-topped)
- Origin is centre of a flat-topped hexagon
- Axial directions: x points down-right, y points up-right, z points up
- Cube coordinate constraint: x + y + z = 0
- 60° clockwise rotation: (x, y, z) → (-z, -x, -y)
- 60° counter-clockwise rotation: (x, y, z) → (-y, -z, -x)

### Triangular Lattice (Pointy-topped)
- Origin is centre of a pointy-topped triangle
- Triangle orientation alternates: pointy-up and pointy-down
- Cube coordinate constraint: x + y + z = 0 for centres
- 120° clockwise rotation: (x, y, z) → (y, z, x)
- 120° counter-clockwise rotation: (x, y, z) → (z, x, y)

### Triangular Lattice (Flat-topped)
- Cube coordinate constraint: x + y + z = -1 for centres
- 120° clockwise rotation: (x, y, z) → (y, z, x)
- 120° counter-clockwise rotation: (x, y, z) → (z, x, y)

## Vector Conversions

### Hexagon Vertex to Vector
```
x_world = (x - 0.5*y - 0.5*z) * lattice
y_world = 0.0
z_world = (√3/2 * y - √3/2 * z) * lattice
```

### Triangle Vertex to Vector
```
x_world = (√3/2 * y - √3/2 * z) * lattice
y_world = 0.0
z_world = (x - 0.5*y - 0.5*z) * lattice
```

Note: Hexagon and Triangle swap x/z axes to align with their respective orientations.

The lattice property is used to scale the grid in world space to allow for fine/coarse grid resolutions. The default value for this is `lattice = 1.0`.

## Scale Hierarchy

### Hexagon Scale
- `.tile` - Single hexagon
- `.chunk` - 7 hexagons arranged in a ring around centre
- `.region` - 7 chunks of 7 hexagons arranged in a larger ring

### Triangle Scale
- `.tile` - Single triangle
- `.chunk` - 16 triangles arranged in a triangular pattern
- `.region` - 16 chunks of 16 triangles arranged in a larger pattern

## Rotation Implementation

### Hexagon Rotation
```swift
func rotate(_ rotation: Rotation) -> Self {
    var rotated = self
    for _ in 0..<rotation.turns {
        rotated = .init(-rotated.z, -rotated.x, -rotated.y)
    }
    return rotated
}
```
- 6 possible turns (60° increments)
- Rotation wraps to [0, 5] range
- Clockwise 1 turn = 60° clockwise
- Counter-clockwise 1 turn = 300° clockwise (5 turns)

### Triangle Rotation
```swift
func rotate(_ rotation: Rotation) -> Self {
    var rotated = self
    for _ in 0..<rotation.turns {
        rotated = .init(rotated.y, rotated.z, rotated.x)
    }
    return rotated
}
```
- 3 possible turns (120° increments)
- Rotation wraps to [0, 2] range
- Clockwise 1 turn = 120° clockwise
- Counter-clockwise 1 turn = 240° clockwise (2 turns)

# Hexagon Footprint Rotation

Footprints rotate tiles around the footprint origin (not world origin):
1. Convert each tile to offset from origin
2. Apply rotation to offset
3. Convert back to absolute position

```swift
public func rotate(_ rotation: Rotation) -> Self {
    let tiles = tiles.map {
        let relative = $0 - origin
        return relative.rotate(rotation)
    }
    return .init(origin, tiles)
}
```

# Triangle Footprint Rotation

Footprints rotate tiles around the footprint origin (not world origin) with a special handling for triangles with a pointy orientation:
1. Convert each tile to offset from origin
2. Apply rotation to offset
3. Convert back to absolute position

```swift
public func rotate(_ rotation: Rotation) -> Self {
    let tiles = tiles.map {
        let relative = origin.isPointy ? ($0 - origin) : (origin - $0)
        return relative.rotate(rotation)
    }
    return .init(origin, tiles)
}
```

## Neighbour Offsets

### Hexagon Neighbours (6 edges)
- `e0`: (+1, 0, -1)
- `e1`: (+1, -1, 0)
- `e2`: (0, -1, +1)
- `e3`: (-1, 0, +1)
- `e4`: (-1, +1, 0)
- `e5`: (0, +1, -1)

### Triangle Neighbours (3 edges)
- `e0`: (+1, 0, 0) or (-1, 0, 0) depending on orientation
- `e1`: (0, +1, 0) or (0, -1, 0) depending on orientation
- `e2`: (0, 0, +1) or (0, 0, -1) depending on orientation

## Scale Transposition

### Hexagon Scale Transposition
- `.tile` → `.chunk`: parent(2)
- `.tile` → `.region`: parent(2).parent().rotate(.counterClockwise)
- `.chunk` → `.tile`: child(2)
- `.chunk` → `.region`: parent().rotate(.counterClockwise)
- `.region` → `.tile`: child(2).child().rotate(.clockwise)
- `.region` → `.chunk`: child().rotate(.clockwise)

### Triangle Scale Transposition
- `.tile` → `.chunk`: parent()
- `.tile` → `.region`: parent().parent()
- `.chunk` → `.tile`: child()
- `.chunk` → `.region`: parent()
- `.region` → `.tile`: child().child()
- `.region` → `.chunk`: child()

## Validation Rules

### Coordinate Invariants
- Hexagon: Always verify x + y + z == 0
- Hexagon corners: Verify x + y + z == 1 or x + y + z == -1
- Triangle: Always verify x + y + z == 0 for centres
- Triangle corners: Verify x + y + z == 1

### Rotation Tests
- Test origin remains unchanged after rotation
- Test clockwise + counter-clockwise = identity
- Test multiple rotations wrap correctly
- Test negative turn counts are handled

### Scale Tests
- Test parent(child(tile)) == tile
- Test child(parent(tile)) == tile (when divisible)
- Test scale transposition preserves relative positions

# References

- [Amit Patel's Hexagon Grids](https://www.redblobgames.com/grids/hexagons/)
- [Boris the Brave's Ortho-Tiling](https://www.boristhebrave.com/2023/05/31/ortho-tiling/)
- [Oskar Stalberg's Procedural Generation](https://t.co/qakKgmxfai)
- [Pentomino](https://en.wikipedia.org/wiki/Pentomino)
- [Triangular Tiling](https://en.wikipedia.org/wiki/Triangular_tiling)
- [Hexagonal Tiling](https://en.wikipedia.org/wiki/Hexagonal_tiling)