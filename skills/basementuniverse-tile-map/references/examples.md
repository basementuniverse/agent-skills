# Usage Examples

## Basic Static Tile Map

Create a simple tile map with predefined tiles and data.

```typescript
import { TileMap } from '@basementuniverse/tile-map';
import { vec2 } from '@basementuniverse/vec';

// Load tile images
const grassImage = new Image();
grassImage.src = 'grass.png';
const waterImage = new Image();
waterImage.src = 'water.png';

// Create tile map
const tileMap = new TileMap({
  tileSize: 32,
  layers: [
    {
      name: 'ground',
      tiles: [
        { name: 'grass', image: grassImage, solid: false },
        { name: 'water', image: waterImage, solid: false }
      ],
      data: [
        [0, 0, 0, 1, 1],
        [0, 0, 1, 1, 1],
        [0, 0, 0, 1, 1],
        [0, 0, 0, 0, 1]
      ]
    }
  ]
});

// Render in game loop
function render(context, camera) {
  tileMap.draw(context, camera);
}
```

## Procedural Generation

Generate tiles algorithmically using hooks.

```typescript
import { TileMap } from '@basementuniverse/tile-map';
import { vec2 } from '@basementuniverse/vec';

// Simple noise function (use a real noise library in practice)
function noise(x: number, y: number): number {
  return Math.sin(x * 0.1) * Math.cos(y * 0.1);
}

const tileMap = new TileMap({
  tileSize: 16,
  chunkSize: 16,
  layers: [
    {
      name: 'terrain',
      tiles: [
        { name: 'grass', image: grassImage },
        { name: 'stone', image: stoneImage },
        { name: 'dirt', image: dirtImage }
      ]
    }
  ],

  // Generate chunk procedurally
  preGenerateChunk: (context, tileMap, tileBounds, chunkPosition) => {
    const layer = tileMap.options.layers[0];

    // Generate tile data based on noise
    for (let ty = tileBounds.topLeft.y; ty < tileBounds.bottomRight.y; ty++) {
      for (let tx = tileBounds.topLeft.x; tx < tileBounds.bottomRight.x; tx++) {
        const value = noise(tx, ty);
        let tileIndex = 0;  // grass

        if (value > 0.5) tileIndex = 1;   // stone
        else if (value > 0.2) tileIndex = 2; // dirt

        const tile = layer.tiles[tileIndex];
        if (tile) {
          const pixelX = (tx - tileBounds.topLeft.x) * tileMap.options.tileSize;
          const pixelY = (ty - tileBounds.topLeft.y) * tileMap.options.tileSize;

          context.drawImage(
            tile.image,
            pixelX,
            pixelY,
            tileMap.options.tileSize,
            tileMap.options.tileSize
          );
        }
      }
    }

    // Return chunk with canvas, skip default generation
    return [{
      chunkPosition,
      image: context.canvas
    }, false];
  }
});
```

## Collision Detection

Use rectangle decomposition for efficient collision checks.

```typescript
import { TileMap } from '@basementuniverse/tile-map';
import { vec2 } from '@basementuniverse/vec';

// Create tile map with collision data
const tileMap = new TileMap({
  layers: [
    {
      name: 'collision',
      tiles: [
        { name: 'empty', image: emptyImage, solid: false },
        { name: 'wall', image: wallImage, solid: true }
      ],
      data: [
        [1, 1, 1, 1, 1],
        [1, 0, 0, 0, 1],
        [1, 0, 1, 0, 1],
        [1, 0, 0, 0, 1],
        [1, 1, 1, 1, 1]
      ]
    }
  ]
});

// Get collision rectangles for solid tiles
const collisionRects = tileMap.getLayerRectangles('collision', 'solid');

// Check collision with player
function checkCollision(playerRect: Rectangle): boolean {
  for (const rect of collisionRects) {
    if (rectanglesIntersect(playerRect, rect)) {
      return true;
    }
  }
  return false;
}

function rectanglesIntersect(a: Rectangle, b: Rectangle): boolean {
  return (
    a.position.x < b.position.x + b.size.x &&
    a.position.x + a.size.x > b.position.x &&
    a.position.y < b.position.y + b.size.y &&
    a.position.y + a.size.y > b.position.y
  );
}
```

## Multi-Layer Rendering

Create complex scenes with multiple layers.

```typescript
const tileMap = new TileMap({
  tileSize: 16,
  layers: [
    // Background layer
    {
      name: 'background',
      tiles: [{ name: 'sky', image: skyImage }],
      data: createFilledArray(50, 50, 0)  // Fill with sky
    },

    // Ground layer
    {
      name: 'ground',
      tiles: [
        { name: 'grass', image: grassImage },
        { name: 'dirt', image: dirtImage }
      ],
      data: groundData  // Your ground tile data
    },

    // Objects layer (with transparency)
    {
      name: 'objects',
      tiles: [
        { name: 'tree', image: treeImage },
        { name: 'rock', image: rockImage }
      ],
      data: objectData,
      opacity: 1.0
    },

    // Foreground layer (partially transparent)
    {
      name: 'fog',
      tiles: [{ name: 'fog', image: fogImage }],
      data: fogData,
      opacity: 0.5
    }
  ]
});

function createFilledArray(width: number, height: number, value: number): number[][] {
  return Array(height).fill(null).map(() => Array(width).fill(value));
}
```

## Post-Processing Effects

Apply effects after tile rendering.

```typescript
const tileMap = new TileMap({
  layers: [
    {
      name: 'terrain',
      tiles: terrainTiles,
      data: terrainData,

      // Apply tile blending effect
      postRenderTile: (canvas, context, tileMap, layer, chunkPos, tilePos) => {
        // Blend with neighboring tiles for smooth transitions
        context.globalCompositeOperation = 'multiply';
        context.fillStyle = 'rgba(0, 0, 0, 0.1)';

        // Add shadow on right and bottom edges
        const tileSize = tileMap.options.tileSize;
        const localX = (tilePos.x % tileMap.options.chunkSize) * tileSize;
        const localY = (tilePos.y % tileMap.options.chunkSize) * tileSize;

        context.fillRect(
          localX + tileSize - 2,
          localY,
          2,
          tileSize
        );

        context.fillRect(
          localX,
          localY + tileSize - 2,
          tileSize,
          2
        );

        context.globalCompositeOperation = 'source-over';
      }
    }
  ]
});
```

## Camera Integration

Use with @basementuniverse/camera component.

```typescript
import { TileMap } from '@basementuniverse/tile-map';
import { Camera } from '@basementuniverse/camera';

const camera = new Camera({
  // Camera configuration
});

const tileMap = new TileMap({
  bounds: {
    topLeft: vec2(0, 0),
    bottomRight: vec2(100, 100)
  },
  clampPositionToBounds: true,
  // ... other options
});

// In render loop
function render(context: CanvasRenderingContext2D) {
  // Draw camera (applies transforms)
  camera.draw(context);

  // Draw tile map (uses camera's transforms)
  tileMap.draw(context, camera);

  // Draw other game objects...
}
```

## Debug Visualization

Enable debug overlays during development.

```typescript
const tileMap = new TileMap({
  // ... configuration

  debug: {
    showOrigin: true,         // Show origin point
    showChunkBorders: true,   // Outline chunks
    showChunkLabels: true,    // Label chunk coordinates
    showTileBorders: true     // Outline individual tiles
  }
});

// Or enable all debug options
const tileMapDebug = new TileMap({
  // ... configuration
  debug: true  // Enables all debug options
});
```

## RLE Compression

Use RLE-compressed data for efficient storage.

```typescript
// Encode tile data (command-line tool)
// $ node encode-rle.js '[[0,0,1,1,1],[0,0,0,1,1]]'
// Output: [2,0,3,1,3,0,2,1]

// In your data file
const mapData: TileMapLayerOptionsData = {
  name: 'ground',
  tiles: [
    { name: 'grass', imageName: 'grass' },
    { name: 'water', imageName: 'water' }
  ],
  width: 5,  // Required for decompression
  data: [2, 0, 3, 1, 3, 0, 2, 1]  // RLE encoded
};

// Use content processor to decompress
import { tileMapOptionsContentProcessor } from '@basementuniverse/tile-map';

// In content manager
{
  'myMap': {
    type: 'tilemap',
    content: { layers: [mapData] },
    processor: (content, data) =>
      tileMapOptionsContentProcessor(content, data, {
        decompressData: true  // Decompress RLE and reshape to 2D
      })
  }
}
```

## Query Tiles at Position

Get tile information at a specific world position.

```typescript
// Get tile from specific layer at pixel position (100, 200)
const tile = tileMap.getTileAtPosition(vec2(100, 200), 'ground');

if (tile) {
  console.log(`Tile: ${tile.name}`);
  if (tile.solid) {
    console.log('This tile is solid!');
  }
}

// Get tiles from all layers
const allTiles = tileMap.getTilesAtPosition(vec2(100, 200));

for (const [layerName, tile] of Object.entries(allTiles)) {
  if (tile) {
    console.log(`Layer ${layerName}: ${tile.name}`);
  }
}
```

## Bounded Tile Map

Create a tile map with specific bounds.

```typescript
const tileMap = new TileMap({
  bounds: {
    topLeft: vec2(-50, -50),    // Start at negative coordinates
    bottomRight: vec2(50, 50)   // End at positive coordinates
  },
  clampPositionToBounds: true,  // Prevent camera from leaving bounds
  minScale: 0.5,
  maxScale: 4.0,
  // ... rest of configuration
});

// The camera will be clamped to stay within these bounds
```
