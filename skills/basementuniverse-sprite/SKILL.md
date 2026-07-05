---
name: basementuniverse-sprite
description: >
  Use this skill when working with @basementuniverse/sprite - a 2D sprite component for games
  with animations, directions, attachment points, and debug visualization. Invoke when creating
  or modifying game sprites, implementing sprite animations, setting up directional sprites
  (e.g., walk-left, walk-right), working with attachment points for composite sprites, or
  integrating with Content Manager for asset loading.
---

# Basement Universe Sprite

Use this skill when working with `@basementuniverse/sprite`.

## Overview

`@basementuniverse/sprite` is a TypeScript library for creating and managing 2D animated sprites in canvas-based games. It provides:

- **Multi-directional animations**: Define animations that vary by direction (e.g., walk-left, walk-right, walk-up, walk-down)
- **Flexible animation modes**: Loop indefinitely, play once and stop, or play once and reset
- **Attachment points**: Define named points on sprites that can be animated and queried in world space (useful for attaching weapons, effects, etc.)
- **Transform support**: Position, rotation, scale, and origin offset
- **Debug visualization**: Built-in rendering for bounding boxes, transforms, and attachment points
- **Content Manager integration**: Optional integration with `@basementuniverse/content-manager` for asset loading

## Installation

```bash
npm install @basementuniverse/sprite
```

## Basic Usage

### Creating a Sprite

```typescript
import { Sprite } from '@basementuniverse/sprite';

const sprite = new Sprite({
  directions: ['left', 'right', 'up', 'down'],
  defaultDirection: 'down',
  animations: {
    idle: {
      '*': {  // '*' means available for all directions
        name: 'idle',
        frameCount: 1,
        frameRate: 1,
        images: [idleImage],
      }
    },
    walk: {
      left: {
        name: 'walk',
        frameCount: 4,
        frameRate: 8,
        mode: 'repeat',
        images: [walkLeft1, walkLeft2, walkLeft3, walkLeft4],
      },
      right: {
        name: 'walk',
        frameCount: 4,
        frameRate: 8,
        mode: 'repeat',
        images: [walkRight1, walkRight2, walkRight3, walkRight4],
      },
      // ... other directions
    }
  },
  defaultAnimation: 'idle',
});
```

### Game Loop Integration

```typescript
// Update (called every frame)
sprite.update(dt);  // dt in seconds

// Render (called every frame)
sprite.draw(context);  // CanvasRenderingContext2D
```

### Controlling Sprites

```typescript
// Position and transform
sprite.position = vec(100, 100);
sprite.rotation = Math.PI / 4;  // radians
sprite.scale = 1.5;

// Animation and direction
sprite.animation = 'walk';
sprite.direction = 'right';

// Animation control
sprite.playAnimation();
sprite.pauseAnimation();
sprite.resetAnimation();
```

## Key Concepts

### Direction Fallbacks

When looking up an animation for a specific direction:
1. First tries the exact direction name
2. Falls back to `'*'` (wildcard) if defined
3. Falls back to the first available direction
4. Throws error if animation doesn't exist

### Animation Repeat Modes

- `'repeat'`: Loop indefinitely (default)
- `'play-once-and-stop'`: Play once and remain on last frame
- `'play-once-and-reset'`: Play once and return to first frame

### Attachment Points

Attachment points are named positions on a sprite that can be:
- Defined with a default offset from the sprite origin
- Animated per-frame using keyframes in animations
- Retrieved in world space (accounting for sprite transforms)

```typescript
const sprite = new Sprite({
  attachmentPoints: [
    { name: 'hand', offset: vec(10, 5) }
  ],
  animations: {
    attack: {
      '*': {
        name: 'attack',
        frameCount: 3,
        frameRate: 10,
        attachmentPointKeyframes: {
          hand: [
            { frame: 0, offset: vec(10, 5) },
            { frame: 1, offset: vec(15, 3) },  // swing forward
            { frame: 2, offset: vec(10, 5) },  // return
          ]
        }
      }
    }
  }
});

// Get world-space position of attachment point
const handPosition = sprite.getAttachmentPoint('hand');
if (handPosition) {
  // Attach weapon sprite at this position
}
```

### Content Manager Integration

For projects using `@basementuniverse/content-manager`, use `SpriteOptionsData` with asset names instead of loaded images:

```typescript
import { spriteOptionsContentProcessor } from '@basementuniverse/sprite';

// Register processor with Content Manager
ContentManager.registerProcessor('sprite', spriteOptionsContentProcessor);

// Define sprite data with image names
const spriteData: SpriteOptionsData = {
  imageName: 'base-sprite',
  animations: {
    walk: {
      left: {
        name: 'walk',
        frameCount: 4,
        imageNames: ['walk-left-1', 'walk-left-2', 'walk-left-3', 'walk-left-4'],
      }
    }
  }
};
```

## Common Patterns

### Multi-directional Character Sprite

```typescript
const character = new Sprite({
  directions: ['up', 'down', 'left', 'right'],
  defaultDirection: 'down',
  animations: {
    idle: {
      '*': { name: 'idle', frameCount: 1, images: [idleImage] }
    },
    walk: {
      up: { name: 'walk', frameCount: 4, frameRate: 8, images: walkUpFrames },
      down: { name: 'walk', frameCount: 4, frameRate: 8, images: walkDownFrames },
      left: { name: 'walk', frameCount: 4, frameRate: 8, images: walkLeftFrames },
      right: { name: 'walk', frameCount: 4, frameRate: 8, images: walkRightFrames },
    }
  },
  defaultAnimation: 'idle',
});

// Control based on input
if (movementDetected) {
  character.animation = 'walk';
  character.direction = movementDirection;
} else {
  character.animation = 'idle';
}
```

### Composite Sprites with Attachments

```typescript
const body = new Sprite({
  attachmentPoints: [
    { name: 'leftHand', offset: vec(-10, 0) },
    { name: 'rightHand', offset: vec(10, 0) },
  ],
  // ... animations with attachment point keyframes
});

const weapon = new Sprite({ /* ... */ });

// Update weapon position to follow hand attachment
const handPos = body.getAttachmentPoint('rightHand');
if (handPos) {
  weapon.position = handPos;
}
```

### Debug Visualization

```typescript
const sprite = new Sprite({
  // ... sprite config
  debug: {
    showSpriteTransforms: true,      // Show X/Y axes at origin
    showSpriteBoundingBox: true,     // Show bounding box
    showAttachmentPoints: true,      // Show attachment point markers
  }
});

// Or enable all debug options
const sprite = new Sprite({
  // ...
  debug: true
});
```

## Important Notes

- **Coordinates**: Uses `@basementuniverse/vec` for 2D vectors (vec2)
- **Rotation**: Measured in radians
- **Frame indexing**: Zero-based (frame 0 is the first frame)
- **Image fallback**: If animation frame image is missing, falls back to base image
- **Empty sprites**: Sprites can exist without images (useful as attachment point hosts)
- **Transform order**: Translate(position) → Scale(scale) → Rotate(rotation)

## References

- [API Reference](references/api.md) - Complete type and class documentation
- [Content Manager Integration](references/content-manager.md) - Asset loading integration details
- [Examples](references/examples.md) - Common usage examples and patterns
