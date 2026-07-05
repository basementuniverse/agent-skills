# @basementuniverse/frame-timer API

## Installation

```bash
npm install @basementuniverse/frame-timer
```

## Import

```ts
import FrameTimer from '@basementuniverse/frame-timer';
```

## Types

```ts
type FrameTimerOptions = {
  minFPS: number;
};
```

### FrameTimerOptions.minFPS

- Type: `number`
- Default: `30`
- Purpose: clamps reported `elapsedTime` to at most `1 / minFPS` seconds.

Clamping formula:

$$
\text{elapsedTime} = \min\left(\frac{\text{now} - \text{lastFrameTime}}{1000}, \frac{1}{\text{minFPS}}\right)
$$

## Class: FrameTimer

### Constructor

```ts
new FrameTimer(options?: Partial<FrameTimerOptions>)
```

- `options.minFPS` is optional and merged with defaults.

### Properties

```ts
get elapsedTime(): number
```

- Seconds since the previous `update()` call (clamped by `minFPS`).

```ts
get frameRate(): number
```

- Frames per second counted over the most recent one-second window.
- Updated approximately once per second.

### Methods

```ts
update(): void
```

- Updates internal frame timing and FPS counters.
- Call once per rendered frame.

## Usage Pattern

```ts
const timer = new FrameTimer({ minFPS: 30 });

function gameLoop() {
  timer.update();

  const dt = timer.elapsedTime;
  const fps = timer.frameRate;

  // Update game logic with dt, optionally display fps

  requestAnimationFrame(gameLoop);
}

gameLoop();
```

## Runtime Notes

- Relies on `performance.now()`.
- Designed for browser/game-loop environments.
