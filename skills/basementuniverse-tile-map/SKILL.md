---
name: basementuniverse-tile-map
description: >
  Use this skill when working with 2D tile-based game rendering, implementing chunked tile map systems, procedural tile generation, tile-based collision detection, or optimizing tile rendering performance with the @basementuniverse/tile-map library.
---

# Basement Universe Tile Map

Use this skill when working with `@basementuniverse/tile-map`.

## When to Use This Skill

Invoke this skill when:
- Implementing or debugging 2D tile-based game rendering
- Setting up chunked tile map systems with layer support
- Working with procedural tile generation using hooks
- Implementing tile-based collision detection with rectangle decomposition
- Optimizing tile rendering performance with LRU caching
- Integrating tile maps with camera systems
- Configuring tile alignment, opacity, or clipping behavior
- Using RLE-compressed tile data for efficient storage

## Core Concepts

### Chunked Rendering
The library divides tile maps into fixed-size chunks (default 8×8 tiles) that are rendered to individual canvases and cached in an LRU buffer. This enables efficient rendering of large tile maps by only generating and drawing visible chunks.

### Layers
Tile maps support multiple layers rendered in order (bottom-to-top). Each layer has:
- Tile definitions (images and metadata)
- 2D data array mapping positions to tile indices
- Rendering options (opacity, alignment, clipping)
- Custom hooks for per-tile rendering logic

### Coordinate Systems
- **World position**: Position in pixels
- **Tile position**: Position in tiles (world position ÷ tile size)
- **Chunk position**: Position in chunks (tile position ÷ chunk size)

### Hooks System
The library provides hooks at multiple levels:
- **Tile map level**: `preRender`, `postRender`
- **Chunk level**: `preGenerateChunk`, `postGenerateChunk`
- **Tile level**: `preRenderTile`, `postRenderTile`

These enable procedural generation, custom effects, tile blending, marching squares, and other advanced techniques.

## Key Features

- **Efficient chunked rendering** with LRU caching
- **Multiple layers** with individual opacity and rendering options
- **Procedural generation** via hook system
- **Camera integration** with bounds clamping
- **Debug visualization** for development
- **Collision optimization** via rectangle decomposition
- **RLE compression** support for tile data
- **Flexible tile alignment** within tile boundaries

## Common Use Cases

### Basic Static Tile Map
Create a simple tile map with predefined tile data and images. Suitable for static levels or pre-designed maps.

### Procedural Generation
Use `preGenerateChunk` hook to generate tiles algorithmically (e.g., Perlin noise, cellular automata) as chunks come into view.

### Tile-Based Collision
Use `getLayerRectangles()` to get collision rectangles for a layer, reducing collision checks from thousands of tiles to dozens of rectangles.

### Post-Processing Effects
Use `postRenderTile` or `postGenerateChunk` to apply effects like tile blending, marching squares for smooth terrain, or dynamic lighting.

### Camera Integration
Pass a camera object directly to `draw()` for automatic viewport calculation and position/scale management.

## Performance Considerations

- **Chunk size**: Smaller chunks = more granular loading, but more overhead. Default 8×8 is balanced.
- **Chunk border**: Increase to pre-load chunks before they're visible (smoother panning), but more chunks rendered per frame.
- **Cache size**: Increase `chunkBufferMaxSize` if revisiting areas frequently to avoid regeneration.
- **Layer optimization**: Use fewer layers when possible; each layer is rendered separately per chunk.
- **Data compression**: Use RLE encoding for sparse tile data to reduce memory and transfer size.

## Integration Points

### Dependencies
- `@basementuniverse/vec` - Vector math library (vec2)
- `@basementuniverse/utils` - Utility functions (chunk, clamp)
- `@basementuniverse/camera` - Optional camera component for viewport management
- `fast-rle` - RLE compression/decompression
- `lru_map` - LRU cache implementation

### Content Manager
The library provides `tileMapOptionsContentProcessor` for integration with content management systems. This processor converts serialized tile map data (with image names) into runtime TileMapOptions (with loaded images) and optionally decompresses RLE-encoded tile data.

## References

- Public API: [references/api.md](references/api.md)
- Type definitions: [references/types.md](references/types.md)
- Usage examples: [references/examples.md](references/examples.md)
