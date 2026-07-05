---
name: basementuniverse-frame-timer
description: >
  Use this skill when integrating or modifying code that relies on
  @basementuniverse/frame-timer for per-frame delta time and FPS tracking in
  browser game loops.
---

# Basement Universe Frame Timer

Use this skill when working with `@basementuniverse/frame-timer`.

## When To Use

Use this library when you need:
- Frame-to-frame elapsed time (delta time) in seconds for simulation updates.
- A lightweight FPS reading for diagnostics and performance display.
- Protection against large simulation jumps after frame drops via clamped delta time.

Typical scenarios:
- Browser games using `requestAnimationFrame`.
- Canvas/WebGL update loops.
- Any real-time UI or simulation that needs stable time-step input.

## Core Behavior

- Call `update()` once per rendered frame.
- Read `elapsedTime` after `update()` and use it as delta time (seconds).
- Read `frameRate` as a rolling per-second FPS value.
- Delta time is clamped to `1 / minFPS` so one very slow frame does not cause a huge logic jump.

## Quick Example

```ts
import FrameTimer from '@basementuniverse/frame-timer';

const timer = new FrameTimer({ minFPS: 30 });

function loop() {
  timer.update();

  // Use elapsedTime for movement, animation, and simulation steps.
  const dt = timer.elapsedTime;

  // Example: position += velocity * dt
  // HUD/debug: timer.frameRate

  requestAnimationFrame(loop);
}

loop();
```

## Integration Notes For Agents

- This package targets browser-like runtimes and uses `performance.now()`.
- Prefer a single shared `FrameTimer` per loop context.
- Ensure `update()` is called exactly once per frame before consuming timing values.
- `frameRate` updates once per second, so do not expect per-frame smoothness.
- Tune `minFPS` based on gameplay tolerance for slow-frame catch-up.

## References

- Public API surface: [references/api.md](references/api.md)
