---
name: basementuniverse-camera
description: >
  Use this skill when implementing, integrating, or debugging camera movement, zoom,
  coordinate conversion, and viewport bounds with @basementuniverse/camera in 2D
  canvas-based browser games.
---

# Basement Universe Camera

Use this skill when working with `@basementuniverse/camera`.

## When To Use

Use this skill when you need to:

- Add camera movement with smoothing/easing in a 2D canvas game.
- Apply camera transforms before world rendering.
- Convert mouse or pointer positions between screen-space and world-space.
- Restrict camera movement to world bounds.
- Configure camera zoom limits and instant vs eased updates.
- Migrate from the deprecated `draw()` convenience method.

## Quick Start

```ts
import Camera from '@basementuniverse/camera';

const camera = new Camera(
  { x: 0, y: 0 },
  {
    minScale: 0.5,
    maxScale: 4,
    moveEaseAmount: 0.1,
    scaleEaseAmount: 0.1,
  }
);

function update() {
  camera.update({ x: canvas.width, y: canvas.height });
}

function render(context: CanvasRenderingContext2D) {
  context.save();
  camera.setTransforms(context);

  // Render world-space objects here

  context.restore();
}
```

## Implementation Guidance

- Always call `camera.update(screen)` before `camera.setTransforms(context)` each frame.
- Use `camera.position` and `camera.scale` for eased movement/zoom.
- Use `camera.positionImmediate` and `camera.scaleImmediate` for instant snaps.
- Read `camera.actualPosition` and `camera.actualScale` for post-easing values.
- Use `camera.bounds` to query the visible world rectangle.

## Coordinate Conversion Pattern

Use these for input and hit testing:

```ts
const worldMouse = camera.screenToWorld({ x: mouseX, y: mouseY });
const screenEnemy = camera.worldToScreen(enemy.position);
```

## Bounds Behavior Notes

- If `options.bounds` is provided, target camera position is clamped each update.
- If the scaled viewport is larger than configured bounds, bounds are expanded internally to avoid jitter.
- Scale is always clamped to `[minScale, maxScale]`.

## Deprecated API

The `camera.draw(context, screen)` method still works, but it is a compatibility method that only calls:

1. `camera.update(screen)`
2. `camera.setTransforms(context)`

Prefer calling those methods explicitly in new code.

## References

- Public API surface: [references/api.md](references/api.md)
- Usage patterns and pitfalls: [references/usage.md](references/usage.md)
- Type reference: [references/types.md](references/types.md)
