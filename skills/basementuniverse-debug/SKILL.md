---
name: basementuniverse-debug
description: >
  Use this skill when integrating or modifying debug overlays built with
  @basementuniverse/debug on HTML5 canvas render loops, including values,
  charts, markers, borders, tags, and frame-clear behavior.
---

# Basement Universe Debug

Use this skill when working with `@basementuniverse/debug`.

## When To Use This Skill

Use this skill when you need to:

- Add real-time debug overlays to a canvas-based game or simulation.
- Display values in screen corners (`Debug.value`).
- Plot short rolling numeric history (`Debug.chart`).
- Draw spatial diagnostics such as markers and collision outlines (`Debug.marker`, `Debug.border`).
- Filter debug output with tags and/or draw in multiple passes.
- Configure defaults for style, spacing, and behavior via `Debug.initialise`.

## When Not To Use This Skill

- Non-canvas UI debugging (DOM overlays, terminal logging, browser devtools).
- 3D scene debugging APIs not based on `CanvasRenderingContext2D`.
- Persistent metric storage/telemetry (this library is frame-oriented visualization).

## Operational Rules

- Call `Debug.initialise(...)` exactly once before any `Debug.*` usage.
- Do not call `Debug.initialise` multiple times in one runtime; it throws.
- Expect `Debug.draw(...)` to clear values/markers/borders by default each call.
- Charts are persistent until removed (`Debug.removeChart`) or `Debug.clear(true)`.
- For multi-pass rendering in one frame, call `Debug.draw(context, tags, false)` and manually clear once per frame.

## Mental Model

- This is a singleton static API around one internal instance.
- Values/charts/markers/borders are keyed by `label`.
- Reusing a label updates existing state (charts append values).
- `space: 'world'` draws in current world transform.
- `space: 'screen'` draws after reset transform, pinned to viewport.

## Common Integration Pattern

```ts
import Debug from '@basementuniverse/debug';

Debug.initialise({
  margin: 12,
  defaultChart: { valueBufferSize: 120, maxValue: 240 },
});

function draw(context: CanvasRenderingContext2D) {
  // Your scene draw here...

  Debug.value('FPS', fps, { align: 'right' });
  Debug.chart('actors', actorCount, { align: 'right' });
  Debug.marker('player', player.name, player.position);
  Debug.border('player-hitbox', '', player.position, {
    size: player.size,
    borderStyle: 'dashed',
  });

  Debug.draw(context);
}
```

## Agent Checklist

- Verify initialization location is startup-only, not per-frame.
- Verify every frame that creates values/markers/borders also calls `Debug.draw`.
- Ensure border shape requirements are satisfied:
  - Rectangle: provide `size`.
  - Circle: provide `radius`.
- For tag filtering, ensure elements have `tags` and `draw` receives desired tag list.
- Avoid unbounded chart history by keeping sane `valueBufferSize`.

## References

- Public API surface: [references/api.md](references/api.md)
- Type reference: [references/types.md](references/types.md)
- Usage patterns and pitfalls: [references/patterns.md](references/patterns.md)
