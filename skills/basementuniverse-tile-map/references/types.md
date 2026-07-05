# Type Definitions

## Core Types

### TileMapOptions<T>

Main configuration object for TileMap.

```typescript
type TileMapOptions<T extends object = any> = {
  bounds?: Bounds;
  clampPositionToBounds: boolean;  // Default: false
  minScale?: number;
  maxScale?: number;
  tileSize: number;                // Default: 16
  layers: TileMapLayerOptions<T>[];
  chunkSize: number;               // Default: 8
  chunkBorder: number;             // Default: 1
  chunkBufferMaxSize: number;      // Default: 64

  preRender?: (
    context: CanvasRenderingContext2D,
    tileMap: TileMap<T>,
    screen: vec2,
    position: vec2,
    scale?: number
  ) => void;

  postRender?: (
    context: CanvasRenderingContext2D,
    tileMap: TileMap<T>,
    screen: vec2,
    position: vec2,
    scale?: number
  ) => void;

  preGenerateChunk?: (
    context: CanvasRenderingContext2D,
    tileMap: TileMap<T>,
    tileBounds: Bounds,
    chunkPosition: vec2
  ) => TileMapChunk | [TileMapChunk, boolean];

  postGenerateChunk?: (
    canvas: HTMLCanvasElement,
    context: CanvasRenderingContext2D,
    tileMap: TileMap<T>,
    tileBounds: Bounds,
    chunkPosition: vec2
  ) => TileMapChunk;

  debug?: Partial<TileMapDebugOptions> | boolean;
};
```

**Fields:**

- `bounds` - Tile map bounds in tile coordinates. Defines the top-left and bottom-right corners. If undefined, layer data starts at (0, 0).

- `clampPositionToBounds` - If true, camera position is clamped to bounds. Set false for infinite tile maps. Ignored if no bounds defined.

- `minScale`, `maxScale` - Optional zoom constraints.

- `tileSize` - Size of each tile in pixels.

- `layers` - Array of layer configurations. Rendered in order (index 0 first, then 1 on top, etc.).

- `chunkSize` - Size of each render chunk in tiles. Chunks are `chunkSize × chunkSize` tiles.

- `chunkBorder` - Buffer area around viewport in chunks. Chunks within this border are loaded/rendered even if not visible. Negative value = only render fully visible chunks.

- `chunkBufferMaxSize` - Maximum number of chunks in LRU cache.

- `preRender` - Hook called before rendering the entire tile map.

- `postRender` - Hook called after rendering the entire tile map.

- `preGenerateChunk` - Hook called before generating a chunk. Return `[chunk, false]` to skip default generation. If skipped, `postGenerateChunk` is also skipped.

- `postGenerateChunk` - Hook called after generating a chunk. Receives the chunk's canvas for post-processing.

- `debug` - Debug visualization options. Can be boolean (enables/disables all) or object for granular control.

### TileMapLayerOptions<T>

Configuration for a single layer.

```typescript
type TileMapLayerOptions<T extends object = any> = {
  name: string;
  tiles?: TileDefinition<T>[];
  data?: number[][];
  opacity?: number;               // Default: 1.0
  clip?: boolean;                 // Default: false
  alignment?: TileAlignment;      // Default: TileAlignment.Center

  preRenderTile?: (
    context: CanvasRenderingContext2D,
    tileMap: TileMap<T>,
    layer: TileMapLayerOptions<T>,
    chunkPosition: vec2,
    tilePosition: vec2
  ) => void;

  postRenderTile?: (
    canvas: HTMLCanvasElement,
    context: CanvasRenderingContext2D,
    tileMap: TileMap<T>,
    layer: TileMapLayerOptions<T>,
    chunkPosition: vec2,
    tilePosition: vec2
  ) => void;
};
```

**Fields:**

- `name` - Layer identifier.

- `tiles` - Array of tile definitions. Layer `data` contains indices into this array.

- `data` - 2D array of tile indices. `data[y][x]` = index into `tiles` array. Value of `-1` or `undefined` means no tile at that position.

- `opacity` - Layer opacity (0.0 = fully transparent, 1.0 = fully opaque).

- `clip` - If true, clip tiles to tile size boundaries.

- `alignment` - How tile images align within tile boundaries.

- `preRenderTile` - Hook called before rendering each tile.

- `postRenderTile` - Hook called after rendering each tile (skipped if no tile data/definition exists).

### TileDefinition<T>

Defines a tile type with image and custom properties.

```typescript
type TileDefinition<T extends object = any> = {
  name: string;
  image: HTMLImageElement | HTMLCanvasElement;
  [key: string]: any;
} & T;
```

**Fields:**

- `name` - Tile identifier.

- `image` - Image or canvas to render for this tile.

- `...` - Additional custom properties can be added and accessed via generic type `T`.

**Example:**
```typescript
interface MyTileProps {
  solid: boolean;
  damage?: number;
}

const tile: TileDefinition<MyTileProps> = {
  name: 'lava',
  image: lavaImage,
  solid: false,
  damage: 10
};
```

### TileAlignment

Enum for tile image alignment.

```typescript
enum TileAlignment {
  TopLeft = 'top-left',
  Top = 'top',
  TopRight = 'top-right',
  Left = 'left',
  Center = 'center',
  Right = 'right',
  BottomLeft = 'bottom-left',
  Bottom = 'bottom',
  BottomRight = 'bottom-right'
}
```

### Bounds

Defines a rectangular region in tile coordinates.

```typescript
type Bounds = {
  topLeft: vec2;      // Top-left corner in tiles
  bottomRight: vec2;  // Bottom-right corner in tiles
};
```

### Rectangle

Defines a rectangle for collision detection.

```typescript
type Rectangle = {
  position: vec2;     // Top-left corner
  size: vec2;         // Width and height
};
```

### TileMapDebugOptions

Debug visualization settings.

```typescript
type TileMapDebugOptions = {
  showOrigin: boolean;
  showChunkBorders: boolean;
  showChunkLabels: boolean;
  showTileBorders: boolean;
};
```

## Data Types for Serialization

### TileMapOptionsData<T>

Serializable version of TileMapOptions (excludes hooks, uses image names).

```typescript
type TileMapOptionsData<T extends object = any> = Partial<
  Omit<TileMapOptions, 'preRender' | 'postRender' | 'preGenerateChunk' | 'postGenerateChunk' | 'debug'>
> & {
  layers?: TileMapLayerOptionsData<T>[];
};
```

### TileMapLayerOptionsData<T>

Serializable version of TileMapLayerOptions.

```typescript
type TileMapLayerOptionsData<T extends object = any> = Omit<
  TileMapLayerOptions,
  'preRenderTile' | 'postRenderTile'
> & {
  tiles?: (Omit<TileDefinition<T>, 'image'> & { imageName: string })[];
  width?: number;    // Required for RLE decompression
  data: number[];    // 1D array (RLE compressed or flat)
};
```

**Note:** `width` is used by content processor to reshape 1D data array into 2D array.

## Camera Interface

Optional interface for camera integration.

```typescript
interface Camera {
  position: vec2;
  readonly actualPosition: vec2;
  scale: number;
  readonly actualScale: number;
  bounds: {
    top: number;
    bottom: number;
    left: number;
    right: number;
  };
}
```

**See:** [@basementuniverse/camera](https://www.npmjs.com/package/@basementuniverse/camera)
