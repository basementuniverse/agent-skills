# Type Reference

## `Color`

```ts
type Color = {
  r: number;
  g: number;
  b: number;
  a?: number;
};
```

- Object color values are converted to `rgba(r,g,b,a)` internally.
- Alpha defaults to `1` when omitted.

## `StyleOptions`

Main style contract accepted by helpers as `Partial<StyleOptions>`.

```ts
type StyleOptions = {
  batch?: boolean;

  fill: boolean;
  fillColor: Color | string | null;
  gradient: {
    type: 'linear' | 'radial';
    start: vec2;
    end: vec2;
    colorStops: {
      color: Color | string;
      position: number;
    }[];
  } | null;

  stroke: boolean;
  strokeColor: Color | string | null;
  lineWidth: number | null;
  lineStyle: 'solid' | 'dashed' | 'dotted';
  lineDash: number[] | null;

  crossStyle?: '+' | 'x';

  rounded?: boolean;
  borderRadius?: number;

  arrow?: {
    type?:
      | 'caret'
      | 'chevron'
      | ((context: CanvasRenderingContext2D, ...args: any[]) => void);
    size?: number;
  } | null;

  pathType?: 'linear' | 'bezier' | 'catmull-rom';
  bezierOrder?: number;
  catmullRomTension?: number;

  rectangleAnchor?:
    | 'top-left'
    | 'top-center'
    | 'top-right'
    | 'center-left'
    | 'center'
    | 'center-right'
    | 'bottom-left'
    | 'bottom-center'
    | 'bottom-right';

  image?: {
    fillMode?: 'center' | 'stretch' | 'contain' | 'fill' | 'fit-x' | 'fit-y';
    clip?: boolean;
    repeatMode?:
      | 'repeat'
      | 'repeat-x'
      | 'repeat-y'
      | 'pattern'
      | 'pattern-x'
      | 'pattern-y'
      | 'no-repeat';
    opacity?: number;
    scale?: number | vec2;
    offset?: vec2;
    offsetRelative?: boolean;
  };

  grid?: {
    cellSize: number | vec2;
  };

  [key: string]: any;
};
```

## Type Usage Notes

- All drawing functions accept `style?: Partial<StyleOptions>`.
- `fillColor` and `strokeColor` allow:
  - CSS color string
  - `Color` object
  - `null` to avoid setting the corresponding context style property
- `lineDash: null` means do not set line dash at all.
- `lineDash` omitted with `lineStyle` provided means helper-derived dash defaults.
- `lineDash` and `lineStyle` omitted means an empty dash pattern.
- `image.scale` is applied after fill-mode sizing.
- `image.offset` can be absolute or relative via `offsetRelative`.
