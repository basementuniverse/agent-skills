# @basementuniverse/debug API

This reference documents the public API exported by the library.

## Import

```ts
import Debug from '@basementuniverse/debug';
```

## Lifecycle

### `Debug.initialise(options?: Partial<DebugOptions>): void`

Initializes the singleton debug renderer.

- Must be called before other API calls.
- Throws if called more than once.

### `Debug.draw(context: CanvasRenderingContext2D, tags?: string[], clear = true): void`

Renders all currently queued debug elements.

- Draw order:
  - World-space markers and borders.
  - Screen-space values and charts.
  - Screen-space markers and borders.
- `tags` filters elements by overlap with element `tags`.
- `clear=true` clears values/markers/borders after draw.
- Charts are not cleared by `draw`.

### `Debug.clear(clearCharts = false): void`

Manually clears values, markers, and borders.

- Pass `true` to also clear chart history.

## Elements

### `Debug.value(label: string, value: string | number, options?: Partial<DebugValue>): void`

Creates/updates a labeled text row in left or right corner stack.

### `Debug.chart(label: string, value: number, options?: Partial<DebugChart>): void`

Creates/updates a chart and appends a numeric sample.

- History is clipped to `valueBufferSize`.

### `Debug.removeChart(label: string): void`

Removes a chart and its history by label.

### `Debug.marker(label: string, value: string | number, position: vec2, options?: Partial<DebugMarker>): void`

Creates/updates a marker at position with optional label/value text.

- Marker style can be `'x'`, `'+'`, `'.'`, or custom image via `markerImage`.

### `Debug.border(label: string, value: string | number, position: vec2, options?: Partial<DebugBorder>): void`

Creates/updates a border at position with optional label/value text.

Validation behavior:

- If `borderShape === 'circle'` and `radius` is missing, call is ignored.
- For rectangle (default), if `size` is missing, call is ignored.

## Label Keying Behavior

All element maps are keyed by `label`.

- Reuse label to update existing element.
- Use unique labels to render multiple separate entries of same kind.
