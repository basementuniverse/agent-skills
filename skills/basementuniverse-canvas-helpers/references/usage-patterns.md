# Usage Patterns

## 1) Bind Context Once

Use `withContext` when repeatedly drawing with a single context.

```ts
import { withContext, line, rectangle, circle } from '@basementuniverse/canvas-helpers';

const drawLine = withContext(context, line);
const [drawRect, drawCircle] = withContext(context, rectangle, circle);

drawLine(vec2(10, 10), vec2(80, 40), { strokeColor: '#333' });
```

## 2) Batch Path Construction

Compatible helpers can avoid immediate fill/stroke by setting `batch: true`.

```ts
context.beginPath();
line(context, vec2(0, 0), vec2(20, 20), { batch: true });
line(context, vec2(20, 20), vec2(40, 0), { batch: true });
context.stroke();
```

Notes:

- `arrow` is not batch-compatible.
- Helpers still call `save/restore`; batching affects path begin/fill/stroke behavior.

## 3) Choose Path Interpolation Intentionally

- `linear`: deterministic polyline behavior.
- `bezier`: grouped by order (`1..3`) and sampled at fixed intervals.
- `catmull-rom`: smooth interpolation with tension; requires at least 4 points.

```ts
path(context, points, {
  pathType: 'catmull-rom',
  catmullRomTension: 0.5,
  strokeColor: '#0aa'
});
```

## 4) Image Layout + Repeat Strategy

Prefer pattern-based repeat for frequent redraw of the same source.

```ts
image(context, sprite, vec2(0, 0), vec2(512, 512), {
  image: {
    fillMode: 'contain',
    repeatMode: 'pattern',
    opacity: 0.9,
    clip: true,
  },
  rounded: true,
  borderRadius: 8,
});
```

Then clear stale entries when needed:

```ts
clearPatternCache(sprite);
// or clearPatternCache(); for all
```

## 5) Anchor-Driven Placement

Use `rectangleAnchor` to treat `position` as center/corner/etc.

```ts
rectangle(context, vec2(320, 180), vec2(200, 120), {
  rectangleAnchor: 'center',
  fill: true,
  fillColor: '#222',
  strokeColor: '#fff'
});
```
