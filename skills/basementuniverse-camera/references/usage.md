# Usage Patterns And Pitfalls

## Frame Order

Recommended frame flow:

1. Update camera with current canvas size.
2. Save context.
3. Apply camera transforms.
4. Render world-space content.
5. Restore context.

```ts
camera.update({ x: canvas.width, y: canvas.height });

context.save();
camera.setTransforms(context);
renderWorld(context);
context.restore();
```

## Input Mapping

For pointer interactions in world space:

```ts
const worldPoint = camera.screenToWorld({ x: mouseX, y: mouseY });
```

## Easing vs Immediate

Use eased setters for smooth camera motion:

```ts
camera.position = player.position;
camera.scale = 1.5;
```

Use immediate setters for teleports, respawns, or cutscenes:

```ts
camera.positionImmediate = spawnPoint;
camera.scaleImmediate = 1;
```

## Bounds Guidance

- Provide `bounds` when you need to keep the camera inside the game world.
- The implementation expands bounds internally when the scaled viewport would be larger than bounds to avoid edge jitter.

## Deprecated `draw()`

Avoid using `camera.draw(context, screen)` in new code. Use explicit `update()` + `setTransforms()` for clarity and future compatibility.
