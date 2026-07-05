# @basementuniverse/debug Type Reference

Primary types from the library's TypeScript surface.

## `DebugOptions`

```ts
type DebugOptions = {
  margin: number;
  padding: number;
  font: string;
  lineHeight: number;
  lineMargin: number;
  foregroundColour: string;
  backgroundColour: string;
  defaultValue: DebugValue;
  defaultChart: DebugChart;
  defaultMarker: DebugMarker;
  defaultBorder: DebugBorder;
};
```

Defaults:

- `margin`: `10`
- `padding`: `4`
- `font`: `'10pt Lucida Console, monospace'`
- `lineHeight`: `12`
- `lineMargin`: `0`
- `foregroundColour`: `'#fff'`
- `backgroundColour`: `'#333'`

## `DebugValue`

```ts
type DebugValue = {
  label?: string;
  value?: number | string;
  align: 'left' | 'right';
  showLabel: boolean;
  padding?: number;
  font?: string;
  foregroundColour?: string;
  backgroundColour?: string;
  tags?: string[];
};
```

## `DebugChart`

```ts
type DebugChart = {
  label?: string;
  values: number[];
  valueBufferSize: number;
  valueBufferStride: number;
  minValue: number;
  maxValue: number;
  barWidth: number;
  barColours?: {
    offset: number;
    colour: string;
  }[];
  align: 'left' | 'right';
  showLabel: boolean;
  padding?: number;
  font?: string;
  foregroundColour?: string;
  backgroundColour?: string;
  chartBackgroundColour?: string;
  tags?: string[];
};
```

Notable defaults in `defaultChart`:

- `valueBufferSize`: `60`
- `valueBufferStride`: `1`
- `minValue`: `0`
- `maxValue`: `100`
- `barWidth`: `2`
- `align`: `'left'`
- `showLabel`: `true`
- `chartBackgroundColour`: `'#222'`

## `DebugMarker`

```ts
type DebugMarker = {
  label?: string;
  value?: number | string;
  position?: vec2;
  showLabel: boolean;
  showValue: boolean;
  showMarker: boolean;
  markerSize: number;
  markerLineWidth: number;
  markerStyle: 'x' | '+' | '.';
  markerImage?: HTMLImageElement | HTMLCanvasElement;
  markerColour: string;
  space: 'world' | 'screen';
  padding?: number;
  font?: string;
  labelOffset: vec2;
  foregroundColour?: string;
  backgroundColour?: string;
  tags?: string[];
};
```

Notable defaults in `defaultMarker`:

- `showLabel`: `true`
- `showValue`: `true`
- `showMarker`: `true`
- `markerSize`: `6`
- `markerLineWidth`: `2`
- `markerStyle`: `'x'`
- `markerColour`: `'#ccc'`
- `space`: `'world'`
- `labelOffset`: `vec2(10)`

## `DebugBorder`

```ts
type DebugBorder = {
  label?: string;
  value?: number | string;
  position?: vec2;
  size?: vec2;
  radius?: number;
  showLabel: boolean;
  showValue: boolean;
  showBorder: boolean;
  borderWidth: number;
  borderStyle: 'solid' | 'dashed' | 'dotted';
  borderShape: 'rectangle' | 'circle';
  borderColour: string;
  borderDashSize: number;
  space: 'world' | 'screen';
  padding?: number;
  font?: string;
  labelOffset: vec2;
  foregroundColour?: string;
  backgroundColour?: string;
  tags?: string[];
};
```

Notable defaults in `defaultBorder`:

- `showLabel`: `true`
- `showValue`: `true`
- `showBorder`: `true`
- `borderWidth`: `1`
- `borderStyle`: `'solid'`
- `borderShape`: `'rectangle'`
- `borderColour`: `'#ccc'`
- `borderDashSize`: `5`
- `space`: `'world'`
- `labelOffset`: `vec2(10)`

## External Type Dependency

`vec2` is imported from `@basementuniverse/vec` and is used for positions, sizes, and label offsets.
