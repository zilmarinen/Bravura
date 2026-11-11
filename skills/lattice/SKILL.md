---
name: lattice
description: Implement and debug data storage and persistence, hierarchy mappings, and geometric representations of triangular and hexagonal grid lattices in the Lattice package.
metadata:
    author: Zack Brown (zilmarinen)
    version: "1.0"
---

# Role

You are a Senior iOS Engineer specializing in mathematical geometry. Your code must always adhere to Apple's Human Interface Guidelines and App Review guidelines.

# Project

Lattice is a Swift library for modeling hexagonal and triangular grid lattices as efficient, hierarchical data structures. It is designed to persist values at each vertex of a uniform grid, making it ideal for simulations, procedural generation and spatial data processing.

Platforms: iOS 17+, macOS 15+ (see `Package.swift`).

## Purpose

Build on top of Deltille and SpriteKit, Lattice provides the hierarchical data structures needed to efficiently query and render data persisted and constrained to a grid.

The built-in concepts are baked into three categories:
- Data persistence keyed by a grid vertex
- Hierarchical spatial grid chunking
- Efficient rendering through dirty propagation

The key concept is to provide a separation between the data persistence and scene geometry whilst loosely coupling the two together into a performant, topological graph constrained by a fixed grid. Lattice offers data structures for both hexagonal and triangular grids with the option to store data at either the tile center or its corner vertices.

You could use the provided data types to model a scene graph within SpriteKit defining a hierarchical structure for grouping smaller tiles into a larger collection of chunks and regions to facilitate optimized grid chunk loading and caching at runtime. For example, in a game engine you could model terrain using a height map with values stored at the shared corner vertices of a triangular grid which is then efficiently queried and rendered into chunks of a fixed size.

A lattice could be used to store movement costs between tile vertices which can be profiled and traversed to draw a heat map of the possible connections to be used for optimized path finding. This could represent a traversal graph, or topological structure that can be efficiently queried at runtime.

Lattices which store tile-based data can also be used to define an occupancy grid such as placing buildings of a predefined footprint within a grid to determine if the proposed space is free or occupied and thus intersecting with another building footprint.

Lattices which store vertex-based data can be utilised to define continuous surfaces or terrain features where values at shared vertices are interpolated across adjacent tiles.

## Layout

- `Package.swift` — package manifest (Swift tools version 6.4)
- `Sources/Lattice/` — library code. Public API lives here
- `Tests/LatticeTests/` — tests, mirroring the Sources structure

## Commands

Run all commands from the repository root (the folder containing `Package.swift`).

- Build: `swift build`
- Test everything: `swift test` (note: tests require Swift Testing framework)

Never edit or delete anything in `.build/`, `.git`, `.gitignore` or `.swiftformat`.

## Definition of done

`swift build` succeeds, and tests pass.

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
- Complex algorithms need inline comments explaining the approach
- 4-space indentation, max line length 140
- Trailing commas in multi-line collection literals and argument lists
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

- Use Swift Testing (`import Testing`, `@Test`, `#expect`, `#require`), not XCTest
- Every bug fix gets a test that fails before the fix
- Every new public API gets at least one test
- Tests must not touch the network or real file locations; use in-memory fakes
- If the same test still fails after two different fix attempts, stop. Summarize what you tried and what you think the cause is, and ask for guidance
- Never retry a fix you have already tried
- To investigate a failing test, run only that test
- Tests must run in under 30 seconds (add timeout to test commands)
- New tests should cover edge cases and boundary conditions
- Tests should be independent and order-independent

## Guidelines

- Do not add, remove, or upgrade dependencies in `Package.swift` without asking first.
- Do not edit `Package.resolved` by hand.
- Do not change existing `public` API signatures without asking first.
- Do not delete or skip failing tests to make the suite pass.
- Do not run `git commit`, `git push`, or anything that rewrites history.
- Do not commit directly to `develop` or `main`
- When attempting any task, follow a strict retry limit. Do not repeatedly attempt the same task without making meaningful progress.

## Dependencies

- [Deltille](https://github.com/zilmarinen/Deltille) - a Swift library for working with hexagonal and triangular grid systems
- [Euclid](https://github.com/nicklockwood/Euclid) — a Swift library for 3D geometry and mesh generation
- [SpriteKit](https://developer.apple.com/documentation/spritekit) - a general-purpose framework for drawing shapes, particles, text, images, and video in two dimensions
- [Swift Collections](https://github.com/apple/swift-collections/tree/main) — an open-source package of data structure implementations

# Documentation

Lattice builds upon the hexagonal and triangular grid framework provided by the Deltille package. All mathematical concepts relating to grid traversal, grouping, scaling, and rotation are defined within Deltille.

Data is persisted in a data store type which is appropriate to the lattice type. For example, to store values at the corner vertex of a tile you would use a `VertexDataStore`. Alternatively, to store values at the center point of a tile you would use a `TileDataStore`. These two types differ in how they are keyed by the data store. To reiterate: A vertex data store persists values on a grid's tile corner vertices. A tile data store persists values on a grid's tile center vertices.

Data stores partition and persist types conforming to `DataStoreValue` hierarchically using the `Coordinate` keyed to the appropriate grid type. A data store can be queried for a `Wedge` which contains a subset of the values present for a given set of keys. This allows for the store to be queried in chunks for a given scale. Data stores only persist data; they do not persist state used for dirty propagation.

A `TileGrid` and its children (see `TileRegion` and `TileChunk`) define a hierarchy of SpriteKit nodes which propagate changes to the data store to efficiently transform and render the data visually.

The `TileLattice` and `VertexLattice` types synchronize the data store and tile grids to provide updates to the geometry as the data is changed. When a data store value is added, removed, or updated, the lattice coordinates the dirty propagation from the data store bounds in grid space to the tile grid which is marked as dirty to be cleaned on the next rendering loop.

A lattice can be queried for a `Slice` which contains both the grid chunks and data store values for a given set of keys. You might write these to disk or otherwise manipulate and persist these elsewhere to be used as data in a level editor or document-based macOS application.

### Grid Type Mappings

One key rule is that the `TileGrid` and the `DataStore` types do not always share the same underlying grid type. This is a key concept of Lattice and is important for allowing conversion between grid types for partitioning.

| Lattice Type | Grid Type | Data Store Type | Lattice Size |
|--------------|-----------|-----------------|--------------|
| `TileLattice` for triangles | Triangular | Triangular `TileDataStore` | `1.0` |
| `VertexLattice` for triangle vertices | Hexagonal | Triangular `VertexDataStore` | `1.0` (data store uses `2.0`) |
| `TileLattice` for hexagons | Hexagonal | Hexagonal `TileDataStore` | `1.0` |
| `VertexLattice` for hexagon vertices | Triangular | Hexagonal `VertexDataStore` | `1.0` (data store uses `2.0`) |

**Important:** The lattice size parameter passed to the lattice initializer differs from the lattice size used by the underlying data store:
- For a `TileLattice`, the lattice size is `1.0` (tile-based)
- For a `VertexLattice`, the lattice size is `1.0` (which the data store internally maps to `2.0` for the dual grid)

## Architecture

Lattice follows a layered architecture with clear separation of concerns:

### Data Layer
- **DataStore**: Abstract protocol for persisting `DataStoreValue` instances
  - `TileDataStore`: Stores values at tile centers (keyed by tile coordinates)
  - `VertexDataStore`: Stores values at vertex coordinates (keyed by vertex coordinates)
- **DataStoreChunk**: Stores a collection of values for a specific chunk region
- **DataStoreRegion**: Container for multiple chunks, forming a larger region
- **DataStoreNode**: Base class for all data store nodes providing persistence support

### Grid Layer
- **Grid**: Abstract protocol for spatial partitioning and dirty propagation
  - `TileGrid`: Manages tile chunks organized by regions for dirty state tracking
- **TileRegion**: Spatial region containing chunks for rendering optimization
- **TileChunk**: Smallest renderable unit containing a tile's geometry and dirty state

### Lattice Layer
- **Lattice**: Main abstraction combining data store and grid for bidirectional updates
  - `TileLattice`: Tile-centered lattice (triangular/hexagonal grids)
  - `VertexLattice`: Vertex-centered lattice using dual grid transformations

### Core Protocols
- **DataStoreValue**: Represents a value anchored to a grid with a footprint (set of coordinates affected)
- **Wedge**: Read-only view of data within a specific region
- **Slice**: Complete view of grid chunks and their associated data values
- **Soilable**: Protocol for dirty state propagation (tracks dirty state and cleaning operations)

The key architectural principle is the separation between data persistence (`DataStore`) and spatial rendering (`Grid`), connected through the `Lattice` class which coordinates dirty state propagation between the two.

## Implementation

The end user of Lattice will not need to, nor should they, initialize a data store or grid type directly. Instead the developer will subclass the appropriate Lattice type (`TileLattice` or `VertexLattice`) dependent on their needs. For this reason, the internal workings of the grid and data store types should be encapsulated away from the user to provide as minimum boilerplate as possible.

An example would be to subclass the `VertexLattice` to define a Terrain type where elevation is stored at the triangle grid corner vertices.

```swift
private class Terrain: VertexLattice<TerrainChunk, TerrainHeight> {}

private class TerrainChunk: TileChunk<Triangle> {}

private struct TerrainHeight: DataStoreValue {
    
    internal typealias T = Hexagon
    
    internal let vertex: Triangle.Vertex
    
    internal let footprint: Set<Triangle.Vertex>
    
    internal let elevation: Int
    
    internal init(vertex: Triangle.Vertex,
                  elevation: Int) {
        
        self.vertex = vertex
        self.elevation = elevation
        self.footprint = [vertex]
    }
}
```

Here the `TerrainHeight` struct stores the `elevation` keyed by the `Triangle.Vertex`. The `Terrain` is a `VertexLattice` with a hexagonal grid type (inferred from `V.K.T == V.T.D.SI.T` where `D` is the dual grid transformation).

# References

- [Triangular Tiling](https://en.wikipedia.org/wiki/Triangular_tiling)
- [Hexagonal Tiling](https://en.wikipedia.org/wiki/Hexagonal_tiling)