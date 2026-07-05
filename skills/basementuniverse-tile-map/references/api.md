# API Reference

## TileMap Class

### Constructor

```typescript
new TileMap<T extends object = any>(options?: Partial<TileMapOptions<T>>)
```

Creates a new tile map instance with the specified options. Options are merged with defaults:
- `tileSize: 16`
- `chunkSize: 8`
- `chunkBorder: 1`
- `chunkBufferMaxSize: 64`
- `clampPositionToBounds: false`

**Generic Parameter:**
- `T` - Custom type for additional tile definition properties

### Methods

#### draw()

**Overload 1: With explicit parameters**
```typescript
draw(
  context: CanvasRenderingContext2D,
  screen: vec2,
  position: vec2,
  scale: number
): void
```

Renders the tile map with explicit viewport parameters. Applies canvas transforms (translate/scale).

**Parameters:**
- `context` - Canvas rendering context to draw into
- `screen` - Viewport size in pixels
- `position` - Camera position in world space (pixels)
- `scale` - Camera zoom level (1.0 = normal, 2.0 = 2x zoom)

**Overload 2: With camera object**
```typescript
draw(
  context: CanvasRenderingContext2D,
  camera: Camera
): void
```

Renders the tile map using a camera object. Skips canvas transforms (assumes camera already applied them). Call `camera.draw()` before this method.

**Parameters:**
- `context` - Canvas rendering context to draw into
- `camera` - Camera object with `position`, `actualPosition`, `scale`, `actualScale`, and `bounds` properties

#### getTileAtPosition()

```typescript
getTileAtPosition(
  position: vec2,
  layerName?: string
): TileDefinition<T> | null | { [name: string]: TileDefinition<T> | null }
```

Gets the tile at a world position.

**Parameters:**
- `position` - World position in pixels
- `layerName` - Optional layer name. If omitted, returns all layers

**Returns:**
- If `layerName` specified: Single `TileDefinition<T>` or `null` if no tile exists
- If `layerName` omitted: Object mapping layer names to `TileDefinition<T>` or `null`

**Example:**
```typescript
// Get tile from specific layer
const tile = tileMap.getTileAtPosition(vec2(100, 200), 'ground');

// Get tiles from all layers
const tiles = tileMap.getTileAtPosition(vec2(100, 200));
console.log(tiles['ground'], tiles['objects']);
```

#### getLayerRectangles()

```typescript
getLayerRectangles(
  layerName: string,
  fieldName?: keyof TileDefinition<T>,
  tileBounds?: Bounds
): Rectangle[]
```

Returns a roughly minimal set of rectangles covering tiles in a layer. Useful for collision detection.

**Parameters:**
- `layerName` - Name of the layer to analyze
- `fieldName` - Optional field name to check in tile definition. Only tiles where this field is truthy will be included. If omitted, all tiles are included.
- `tileBounds` - Optional bounds in tile coordinates. Relative to `options.bounds.topLeft` if defined, otherwise relative to (0, 0).

**Returns:**
- Array of `Rectangle` objects (each has `position` and `size` as vec2)

**Example:**
```typescript
// Get collision rectangles for solid tiles
const collisionRects = tileMap.getLayerRectangles('ground', 'solid');

// Get all tile rectangles in a specific area
const visibleRects = tileMap.getLayerRectangles(
  'ground',
  undefined,
  { topLeft: vec2(0, 0), bottomRight: vec2(50, 50) }
);
```

**Note:** The returned set is not guaranteed to be minimal but is optimized to reduce the number of rectangles compared to checking individual tiles.

## Utility Functions

### tileMapOptionsContentProcessor()

```typescript
async function tileMapOptionsContentProcessor<T extends object = any>(
  content: Record<string, { name: string; type: string; content: any; status: string }>,
  data: { name: string; type: string; content: any; status: string },
  options?: Partial<{ decompressData: boolean }>
): Promise<void>
```

Content manager processor that converts `TileMapOptionsData` to `TileMapOptions`.

**Conversions:**
- Replaces `imageName` fields in tile definitions with actual loaded images from content manager
- If `options.decompressData` is `true`: Decodes RLE-compressed data arrays and converts from 1D to 2D using `layer.width`

**Parameters:**
- `content` - Content manager's content registry
- `data` - The tile map data item being processed
- `options` - Processing options
  - `decompressData` - If true, decode RLE and reshape to 2D arrays

**Example:**
```typescript
// In content manager configuration
{
  'myTileMap': {
    type: 'tilemap',
    processor: (content, data) =>
      tileMapOptionsContentProcessor(content, data, { decompressData: true })
  }
}
```

### cameraBoundsToTileMapBounds()

```typescript
function cameraBoundsToTileMapBounds(bounds: Camera['bounds']): Bounds
```

Converts camera bounds format to tile map bounds format.

**Parameters:**
- `bounds` - Camera bounds object `{ top, bottom, left, right }`

**Returns:**
- Tile map `Bounds` object `{ topLeft, bottomRight }`

### cameraBoundsSize()

```typescript
function cameraBoundsSize(bounds: Camera['bounds']): vec2
```

Calculates the size of camera bounds.

**Parameters:**
- `bounds` - Camera bounds object

**Returns:**
- Size as vec2 (width, height)

## Bitmap Decomposition

### bitmapToRectangles()

```typescript
function bitmapToRectangles(bitmap: boolean[][]): Rectangle[]
```

Converts a 2D boolean bitmap into a roughly minimal set of rectangles. Used internally by `getLayerRectangles()`.

**Parameters:**
- `bitmap` - 2D array of booleans (true = filled, false = empty)

**Returns:**
- Array of `Rectangle` objects covering all true cells

**Note:** This is exported for advanced use cases but typically called via `getLayerRectangles()`.
