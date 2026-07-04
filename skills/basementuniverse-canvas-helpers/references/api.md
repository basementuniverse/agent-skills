# API Reference

This file summarizes the exported API from `@basementuniverse/canvas-helpers`.

## Exports

- `withContext(context, ...functions)`
- `line(context, start, end, style?)`
- `cross(context, position, size, style?)`
- `arrow(context, start, end, style?)`
- `circle(context, center, radius, style?)`
- `rectangle(context, position, size, style?)`
- `polygon(context, vertices, style?)`
- `path(context, vertices, style?)`
- `grid(context, position, size, style?)`
- `image(context, image, position, size?, style?)`
- `clearPatternCache(image?)`
- Types: `Color`, `StyleOptions`

## Function Details

### `withContext`

Binds one context to one or many helper functions and returns callable wrapper(s)
that no longer need the context parameter.

- Signature:
  - `withContext(context, ...functions)`
- Return:
  - Single bound function if one input function is provided.
  - Array of bound functions if multiple input functions are provided.

Example:

```ts
const [drawLine, drawRect] = withContext(context, line, rectangle);
const drawCircle = withContext(context, circle);
```

### Shape Helpers

All shape helpers draw immediately unless `batch: true` and the helper supports
batch semantics.

- `line`: draw one segment between `start` and `end`.
- `cross`: draw `+` or `x` style cross centered at `position`.
- `arrow`: draws shaft and optional arrowhead (`caret`, `chevron`, or custom). Not batch-compatible.
- `circle`: draw circle from center/radius.
- `rectangle`: draw rectangle using anchor-position semantics.
- `polygon`: draw closed polygon if at least 3 vertices.
- `path`: draw open path if at least 2 vertices, using `linear`, `bezier`, or `catmull-rom` interpolation.
- `grid`: draw evenly spaced vertical/horizontal lines within rectangle.

### `image`

Draw an image at position, optionally constrained by a target rectangle.

Supports:

- Fill modes: `center`, `stretch`, `contain`, `fill`, `fit-x`, `fit-y`
- Repeat modes:
  - Loop-based: `repeat`, `repeat-x`, `repeat-y`
  - Pattern-based (cached): `pattern`, `pattern-x`, `pattern-y`
  - `no-repeat`
- Optional clipping to target rect (including rounded clipping when enabled)
- Opacity, post-layout scaling, and offsets

### `clearPatternCache`

Cache lifecycle utility for pattern-based image drawing.

- `clearPatternCache()` clears all cached patterns.
- `clearPatternCache(image)` clears only one image source entry.

Use this when image sources become stale or to reduce memory usage in long-lived
rendering sessions.
