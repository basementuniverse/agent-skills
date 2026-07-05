# Content Manager Integration

Integration with `@basementuniverse/content-manager` for asset loading.

## Overview

When using `@basementuniverse/content-manager`, you can define sprite configurations that reference assets by name rather than passing loaded image objects. The content processor automatically resolves these references to actual images.

## Installation

```bash
npm install @basementuniverse/sprite @basementuniverse/content-manager
```

## Setup

Register the sprite options content processor with Content Manager:

```typescript
import { ContentManager } from '@basementuniverse/content-manager';
import { spriteOptionsContentProcessor } from '@basementuniverse/sprite';

ContentManager.registerProcessor('sprite', spriteOptionsContentProcessor);
```

## Types

### SpriteOptionsData

Similar to `SpriteOptions` but uses asset names instead of loaded images.

```typescript
type SpriteOptionsData = Partial<
  Omit<SpriteOptions, 'image' | 'preRender' | 'postRender' | 'debug'>
> & {
  imageName?: string;  // Name of base image asset
  animations?: {
    [name: string]: {
      [direction: string]: SpriteAnimationOptionsData;
    };
  };
};
```

**Key Differences from SpriteOptions:**

- `imageName` (string) instead of `image` (HTMLImageElement | HTMLCanvasElement)
- Cannot specify `preRender`, `postRender`, or `debug` in data files
- `animations` use `SpriteAnimationOptionsData` instead of `SpriteAnimationOptions`

### SpriteAnimationOptionsData

Similar to `SpriteAnimationOptions` but uses asset names for images.

```typescript
type SpriteAnimationOptionsData = Omit<SpriteAnimationOptions, 'images'> & {
  imageNames?: string[];  // Names of image assets for each frame
};
```

**Key Difference:**

- `imageNames` (string[]) instead of `images` (HTMLImageElement[] | HTMLCanvasElement[])

## Content Processor

### spriteOptionsContentProcessor

```typescript
async function spriteOptionsContentProcessor(
  content: Record<string, {
    name: string;
    type: string;
    content: any;
    status: string;
  }>,
  data: {
    name: string;
    type: string;
    content: any;
    status: string;
  }
): Promise<void>
```

Converts `SpriteOptionsData` to `SpriteOptions` by resolving image names to loaded images.

**Parameters:**

- `content`: Content Manager's content registry
- `data`: The sprite data item being processed

**Behavior:**

1. Validates that `data.content` is valid `SpriteOptionsData`
2. Resolves `imageName` to loaded image from content registry
3. For each animation and direction:
   - Resolves `imageNames` array to loaded images
   - Converts `SpriteAnimationOptionsData` to `SpriteAnimationOptions`
4. Converts `SpriteOptionsData` to `SpriteOptions`
5. Updates `data.content` with processed `SpriteOptions`

**Throws:**
- Error if `data.content` is not valid `SpriteOptionsData`
- Error if referenced image asset is not found in content registry

## Usage Example

### Define Sprite Data

Create a JSON file for your sprite configuration:

```json
// assets/sprites/player.json
{
  "imageName": "player-base",
  "directions": ["up", "down", "left", "right"],
  "defaultDirection": "down",
  "animations": {
    "idle": {
      "*": {
        "name": "idle",
        "frameCount": 1,
        "frameRate": 1,
        "imageNames": ["player-idle"]
      }
    },
    "walk": {
      "up": {
        "name": "walk",
        "frameCount": 4,
        "frameRate": 8,
        "mode": "repeat",
        "imageNames": [
          "player-walk-up-1",
          "player-walk-up-2",
          "player-walk-up-3",
          "player-walk-up-4"
        ]
      },
      "down": {
        "name": "walk",
        "frameCount": 4,
        "frameRate": 8,
        "mode": "repeat",
        "imageNames": [
          "player-walk-down-1",
          "player-walk-down-2",
          "player-walk-down-3",
          "player-walk-down-4"
        ]
      }
    }
  },
  "defaultAnimation": "idle",
  "attachmentPoints": [
    {
      "name": "weapon",
      "offset": { "x": 10, "y": 5 }
    }
  ]
}
```

### Load and Use

```typescript
import { ContentManager } from '@basementuniverse/content-manager';
import { Sprite, spriteOptionsContentProcessor } from '@basementuniverse/sprite';

// Register processor
ContentManager.registerProcessor('sprite', spriteOptionsContentProcessor);

// Load assets
await ContentManager.load({
  // Images
  'player-base': { type: 'image', path: 'assets/images/player-base.png' },
  'player-idle': { type: 'image', path: 'assets/images/player-idle.png' },
  'player-walk-up-1': { type: 'image', path: 'assets/images/player-walk-up-1.png' },
  // ... more images

  // Sprite configuration
  'player-sprite': { type: 'sprite', path: 'assets/sprites/player.json' },
});

// Get processed sprite options
const playerOptions = ContentManager.get('player-sprite');

// Create sprite
const player = new Sprite(playerOptions);
```

## Type Validation

Use the `isSpriteOptionsData` type guard to validate data before processing:

```typescript
import { isSpriteOptionsData } from '@basementuniverse/sprite';

if (isSpriteOptionsData(data)) {
  // Safe to use as SpriteOptionsData
  const sprite = new Sprite(processData(data));
}
```

## Important Notes

- **Asset Loading Order**: Images must be loaded before sprite configurations that reference them
- **Missing Images**: Processor throws error if referenced image is not found
- **Runtime Configuration**: `preRender`, `postRender`, and `debug` options must be set at runtime when creating the Sprite instance, not in data files
- **Performance**: Content processor runs asynchronously during asset loading, not at sprite creation time

## Complete Example

```typescript
import { ContentManager } from '@basementuniverse/content-manager';
import { Sprite, spriteOptionsContentProcessor } from '@basementuniverse/sprite';

// Setup
ContentManager.registerProcessor('sprite', spriteOptionsContentProcessor);

// Load all assets
await ContentManager.load({
  // All images
  'char-idle': { type: 'image', path: 'char-idle.png' },
  'char-walk-1': { type: 'image', path: 'char-walk-1.png' },
  'char-walk-2': { type: 'image', path: 'char-walk-2.png' },

  // Sprite data (JSON file containing SpriteOptionsData)
  'character': { type: 'sprite', path: 'character.json' },
});

// Create sprite with runtime options
const character = new Sprite({
  ...ContentManager.get('character'),
  debug: true,
  preRender: (ctx, sprite) => {
    // Custom pre-render logic
  },
});

// Use sprite
character.update(dt);
character.draw(context);
```
