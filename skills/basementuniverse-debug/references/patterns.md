# Usage Patterns And Pitfalls

## Single-Pass Frame Pattern

Use this for the common game loop where debug is drawn once per frame.

```ts
Debug.value('fps', fps);
Debug.marker('player', player.id, player.position);
Debug.draw(context); // auto-clears non-chart entries
```

## Multi-Pass Tagged Rendering

Use this when drawing separate debug layers/canvases in one frame.

```ts
Debug.value('fps', fps, { tags: ['hud'] });
Debug.marker('player', player.id, player.position, { tags: ['world'] });

Debug.draw(hudContext, ['hud'], false);
Debug.draw(worldContext, ['world'], false);

Debug.clear(); // once at end of frame
```

## World vs Screen Space

- `space: 'world'`: Draws in the active world transform (camera/zoom aware).
- `space: 'screen'`: Draws in identity transform (viewport anchored).

Use screen-space markers/borders for UI-like diagnostics, world-space for entity diagnostics.

## Border Requirements

`Debug.border(...)` silently ignores invalid shape data.

- Rectangle (default): requires `size`.
- Circle: requires `radius`.

If a border does not appear, verify these fields first.

## Chart Behavior

- Charts persist across draws unless explicitly removed/cleared.
- Reusing label appends into same chart history.
- Tune `valueBufferSize` for memory/history length.
- Use `valueBufferStride > 1` to smooth plotted bars.

## Tag Filtering Semantics

When `draw(..., tags)` is provided, an element renders only if at least one of its `tags` matches a requested tag.

Elements with no tags are skipped in tagged draws.

## Common Mistakes

- Calling `Debug.initialise` more than once.
- Forgetting to call `Debug.draw` every frame after queuing values.
- Expecting charts to auto-clear with `draw`.
- Using non-unique labels where separate entries are desired.
