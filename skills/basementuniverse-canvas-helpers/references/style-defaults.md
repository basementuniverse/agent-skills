# Style Defaults And Merge Behavior

This library merges incoming style values with internal defaults.

## Internal Defaults

```ts
{
  batch: false,
  fill: false,
  fillColor: null,
  gradient: null,
  stroke: true,
  strokeColor: null,
  lineWidth: 1,
  lineStyle: 'solid',
  lineDash: null,
  crossStyle: 'x',
  rounded: false,
  arrow: {
    type: 'caret',
    size: 5,
  },
  pathType: 'linear',
  bezierOrder: 3,
  catmullRomTension: 0.5,
  rectangleAnchor: 'top-left'
}
```

Additional defaults:

```ts
DEFAULT_LINE_DASHES = {
  solid: [],
  dashed: [5, 5],
  dotted: [1, 3]
}
```

```ts
DEFAULT_IMAGE_OPTIONS = {
  fillMode: 'center',
  clip: false,
  repeatMode: 'no-repeat',
  opacity: 1,
  scale: 1,
  offset: vec2(0, 0),
  offsetRelative: false
}
```

```ts
DEFAULT_GRID_OPTIONS = {
  cellSize: 10
}
```

## Merge Rules To Remember

- The style object is shallow-merged.
- `image` and `grid` are treated specially:
  - If omitted entirely, helper-level defaults are used.
  - If present, the provided object is accepted and then merged where needed by specific helpers.
- `lineDash` behavior:
  - If explicitly provided, use it directly.
  - Else if `lineStyle` is provided, derive from `DEFAULT_LINE_DASHES`.
  - Else fallback to `[]`.

## Practical Implication

If you want existing context values to remain unchanged for some properties:

- Set corresponding options to `null` (for properties that support `null`, such as `fillColor`, `strokeColor`, `lineWidth`, `lineDash`).
- Omit options that should receive helper defaults.
