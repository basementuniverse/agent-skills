---
name: basementuniverse-canvas-helpers
description: >
  Use this skill when implementing or reviewing HTML5 Canvas rendering code with
  @basementuniverse/canvas-helpers, including shape drawing, style configuration,
  path interpolation, image layout/repeat modes, and context binding via withContext.
---

# Basement Universe Canvas Helpers

Use this skill when working with `@basementuniverse/canvas-helpers`.

This library is a lightweight set of drawing helpers for `CanvasRenderingContext2D`.
It is intentionally unopinionated: you can use helpers for repetitive operations,
then drop down to native canvas APIs whenever needed.

## When To Use This Skill

- You need to draw common primitives (`line`, `circle`, `rectangle`, `polygon`, `path`, `grid`, `image`, `arrow`, `cross`).
- You want consistent styling with `StyleOptions` while still using native canvas semantics.
- You need to bind a context once using `withContext` and reuse helper calls.
- You are tuning path behavior (`linear`, `bezier`, `catmull-rom`) or image fit/repeat behavior.
- You need memory/performance guidance for image pattern rendering and cache lifecycle.

## When Not To Use This Skill

- You need a scene graph, retained-mode renderer, or game engine architecture.
- You need full canvas API abstraction instead of targeted helper utilities.
- You are working outside 2D canvas rendering.

## Core Concepts

- All draw helpers accept a `CanvasRenderingContext2D` as first argument.
- Most draw functions support `Partial<StyleOptions>`.
- Style options are merged with internal defaults.
- Helpers generally use `context.save()` / `context.restore()` internally.
- `batch: true` avoids `beginPath` and immediate stroke/fill for compatible helpers, enabling manual batching.
- `arrow` explicitly does not support batch drawing because it renders the line and arrowhead in separate path steps.

## Typical Workflow

1. Choose helper(s) for the geometry to draw.
2. Define base style (stroke/fill/line characteristics).
3. Apply geometry-specific options (`rectangleAnchor`, `arrow`, `grid`, `image`, `pathType`).
4. For repeated calls against one context, pre-bind with `withContext`.
5. If using image pattern repeat modes over long sessions, clear stale patterns with `clearPatternCache`.

## Notes And Caveats

- `withContext` expects variadic function arguments, not an array wrapper.
- `path` with `catmull-rom` needs at least 4 vertices; it falls back to linear otherwise.
- `path` with `bezier` clamps order to 1..3.
- `image` throws if width/height cannot be derived from the source.
- `lineDash` handling is nuanced:
  - Explicit `lineDash` in style wins.
  - If omitted but `lineStyle` is provided, default dash pattern is used.
  - If both are omitted, an empty dash array is applied.
- `repeatMode` supports loop-based repeat and pattern-based repeat; pattern-based modes use an internal cache.

## References

- Public API surface: [references/api.md](references/api.md)
- Type reference: [references/types.md](references/types.md)
- Style defaults and merge behavior: [references/style-defaults.md](references/style-defaults.md)
- Usage patterns and recipes: [references/usage-patterns.md](references/usage-patterns.md)
