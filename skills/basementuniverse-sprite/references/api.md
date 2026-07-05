# API Reference

Complete API documentation for `@basementuniverse/sprite`.

## Table of Contents

- [Sprite Class](#sprite-class)
- [Types](#types)
- [Enums](#enums)
- [Type Guards](#type-guards)
- [Content Manager Integration](#content-manager-integration)

## Sprite Class

The main sprite class for creating and managing 2D animated sprites.

### Constructor

```typescript
new Sprite(options?: Partial<SpriteOptions>)
```

Creates a new sprite with the specified options. All options are optional and will fall back to defaults.

**Throws**: Error if invalid direction or animation name is specified.

### Public Properties

```typescript
sprite.position: vec2
```
Current position of the sprite. Defaults to `{x: 0, y: 0}`.

```typescript
sprite.size: vec2
```
Base size of the sprite. Defaults to base image size, or first animation frame size, or `{x: 0, y: 0}`.

```typescript
sprite.origin: vec2
```
Origin offset from top-left corner, used for rotation and scaling. Defaults to center of sprite based on size.

```typescript
sprite.scale: number
```
Scale factor applied to the sprite. Defaults to `1`.

```typescript
sprite.rotation: number
```
Rotation in radians. Defaults to `0`.

```typescript
sprite.direction: string
```
Current direction (getter/setter). Must be a valid direction name from the `directions` array.

```typescript
sprite.animation: string
```
Current animation name (getter/setter). Must be a valid animation name from the `animations` object.

### Public Methods

#### update(dt: number): void

Updates the sprite's animation state. Call this once per frame in your game loop.

**Parameters:**
- `dt`: Delta time in seconds since last frame

**Behavior:**
- Advances current animation frame based on frame rate
- Updates current image based on frame
- Updates attachment points based on current frame and keyframes
- Handles animation repeat modes (loop, stop, reset)

#### draw(context: CanvasRenderingContext2D): void

Renders the sprite to a canvas context. Call this once per frame in your render loop.

**Parameters:**
- `context`: Canvas 2D rendering context

**Behavior:**
1. Saves canvas state
2. Applies sprite transforms (position, scale, rotation)
3. Calls `preRender` hook if defined
4. Draws current image at origin offset
5. Calls `postRender` hook if defined
6. Draws debug visualizations if enabled
7. Restores canvas state

#### playAnimation(): void

Resumes animation playback. Animation will advance on subsequent `update()` calls.

#### pauseAnimation(): void

Pauses animation playback. Animation will remain on current frame until `playAnimation()` is called.

#### resetAnimation(): void

Resets animation to frame 0. Useful when switching animations or restarting.

#### getAttachmentPoint(name: string): vec2 | null

Gets the current world-space position of a named attachment point.

**Parameters:**
- `name`: Name of the attachment point

**Returns:**
- World-space position of the attachment point, or `null` if not found

**Note:** Accounts for sprite transforms (position, scale, rotation).

## Types

### SpriteOptions

Configuration options for creating a sprite.

```typescript
type SpriteOptions = {
  position?: vec2;
  size?: vec2;
  origin?: vec2;
  scale?: number;
  rotation?: number;
  directions: string[];
  defaultDirection: string;
  image?: HTMLImageElement | HTMLCanvasElement;
  animations: {
    [name: string]: {
      [direction: string]: SpriteAnimationOptions;
    };
  };
  defaultAnimation: string;
  attachmentPoints?: SpriteAttachmentPointOptions[];
  preRender?: (context: CanvasRenderingContext2D, sprite: Sprite) => void;
  postRender?: (context: CanvasRenderingContext2D, sprite: Sprite) => void;
  debug?: Partial<SpriteDebugOptions> | boolean;
};
```

**Properties:**

- `position`: Initial position. Default: `{x: 0, y: 0}`
- `size`: Base size. Default: base image size → first animation frame size → `{x: 0, y: 0}`
- `origin`: Origin offset from top-left. Default: center of size
- `scale`: Scale factor. Default: `1`
- `rotation`: Rotation in radians. Default: `0`
- `directions`: Array of valid direction names. Default: `['default']`
- `defaultDirection`: Initial direction. Default: `'default'`
- `image`: Optional base/fallback image
- `animations`: Dictionary of animations by name and direction
- `defaultAnimation`: Initial animation name. Default: `'default'`
- `attachmentPoints`: Array of attachment point definitions
- `preRender`: Hook called before drawing sprite image
- `postRender`: Hook called after drawing sprite image
- `debug`: Debug visualization options (boolean enables all, or object for specific options)

### SpriteAnimationOptions

Configuration for a single animation variant (for one direction).

```typescript
type SpriteAnimationOptions = {
  name: string;
  frameCount?: number;
  frameRate?: number;
  mode?: SpriteAnimationRepeatMode;
  images?: (HTMLImageElement | HTMLCanvasElement)[];
  attachmentPointKeyframes?: {
    [attachmentPointName: string]: SpriteAttachmentPointKeyframe[];
  };
};
```

**Properties:**

- `name`: Animation name
- `frameCount`: Number of frames. Default: `1`
- `frameRate`: Frames per second. Default: `1`
- `mode`: Repeat mode. Default: `SpriteAnimationRepeatMode.Repeat`
- `images`: Array of images for each frame (can be shorter than frameCount)
- `attachmentPointKeyframes`: Per-frame attachment point offsets

### SpriteAttachmentPointOptions

Defines a named attachment point on a sprite.

```typescript
type SpriteAttachmentPointOptions = {
  name: string;
  offset: vec2;
};
```

**Properties:**

- `name`: Unique attachment point name
- `offset`: Position offset from sprite origin

### SpriteAttachmentPointKeyframe

Defines an attachment point's offset at a specific animation frame.

```typescript
type SpriteAttachmentPointKeyframe = {
  frame: number;
  offset: vec2;
};
```

**Properties:**

- `frame`: Frame index (0-based)
- `offset`: Attachment point offset for this frame

**Note:** Values are interpolated between keyframes. Keyframes should be in ascending frame order.

### SpriteDebugOptions

Debug visualization options.

```typescript
type SpriteDebugOptions = {
  showSpriteTransforms: boolean;
  showSpriteBoundingBox: boolean;
  showAttachmentPoints: boolean;
};
```

**Properties:**

- `showSpriteTransforms`: Draw X/Y axes at sprite origin
- `showSpriteBoundingBox`: Draw sprite bounding box
- `showAttachmentPoints`: Draw markers for attachment points

### vec2

2D vector type from `@basementuniverse/vec`.

```typescript
type vec2 = {
  x: number;
  y: number;
};
```

## Enums

### SpriteAnimationRepeatMode

```typescript
enum SpriteAnimationRepeatMode {
  Repeat = 'repeat',
  PlayOnceAndStop = 'play-once-and-stop',
  PlayOnceAndReset = 'play-once-and-reset',
}
```

**Values:**

- `Repeat`: Loop animation indefinitely
- `PlayOnceAndStop`: Play once and remain on last frame
- `PlayOnceAndReset`: Play once and reset to first frame

## Type Guards

### isSpriteOptionsData(value: unknown): value is SpriteOptionsData

Type guard for validating `SpriteOptionsData` objects (used with Content Manager integration).

## Content Manager Integration

See [Content Manager Integration](content-manager.md) for details on:
- `SpriteOptionsData` type
- `SpriteAnimationOptionsData` type
- `spriteOptionsContentProcessor` function

## Default Values

When no options are provided, sprites are created with these defaults:

```typescript
{
  position: { x: 0, y: 0 },
  size: { x: 0, y: 0 },  // or derived from images
  origin: { x: size.x / 2, y: size.y / 2 },
  scale: 1,
  rotation: 0,
  directions: ['default'],
  defaultDirection: 'default',
  animations: {
    default: {
      '*': {
        name: 'default',
        frameCount: 1,
        frameRate: 1,
        mode: SpriteAnimationRepeatMode.PlayOnceAndStop,
      }
    }
  },
  defaultAnimation: 'default',
  attachmentPoints: [],
  debug: false
}
```
