# API Reference

Library import:

```ts
import { ColorUtils, type RGBAColor, type HSLAColor } from '@basementuniverse/color';
```

For direct browser usage, load `build/index.js` with a `<script>` tag and use
the namespaced global:

```html
<script src="build/index.js"></script>
<script>
	const color = BasementUniverseColor.ColorUtils.stringToRGBA('#ff0000');
</script>
```

The UMD build supports CommonJS `require()` and bundlers, but native ESM
imports require a separate ESM build that this package does not currently ship.

## Exported Types

```ts
type RGBAColor = { r: number; g: number; b: number; a?: number };
type HSLAColor = { h: number; s: number; l: number; a?: number };

type RandomSource = () => number;

type RandomRange = {
  min?: number;
  max?: number;
  mean?: number;
  stddev?: number;
};

type RandomRGBAOptions = {
  r?: RandomRange;
  g?: RandomRange;
  b?: RandomRange;
  a?: RandomRange;
  rng?: RandomSource;
};

type RandomHSLAOptions = {
  h?: RandomRange;
  s?: RandomRange;
  l?: RandomRange;
  a?: RandomRange;
  rng?: RandomSource;
};

type RandomGradientOptions = {
  position?: RandomRange;
  space?: 'rgb' | 'hsl';
  rng?: RandomSource;
};
```

## Runtime API via ColorUtils

### Unit conversion

- `toUnit(rgba: RGBAColor): RGBAColor`
- `toUnit(hsla: HSLAColor): HSLAColor`
- `fromUnit(rgba: RGBAColor): RGBAColor`
- `fromUnit(hsla: HSLAColor): HSLAColor`

### Random generation

- `randomRGBA(options?: RandomRGBAOptions): RGBAColor`
- `randomHSLA(options?: RandomHSLAOptions): HSLAColor`
- `randomGradient(colors: Array<RGBAColor | HSLAColor>, options?: RandomGradientOptions): RGBAColor | HSLAColor`

### Parsing

- `stringToRGBA(colorString: string): RGBAColor`
- `stringToHSLA(colorString: string): HSLAColor`

### Color-space conversion

- `rgbaToHSLA(rgba: RGBAColor): HSLAColor`
- `hslaToRGBA(hsla: HSLAColor): RGBAColor`

### String formatting

- `rgbaToString(rgba: RGBAColor, options?: { mode?: 'rgb' | 'hex'; alpha?: boolean; convert?: 'fromUnit' | 'toUnit' }): string`
- `hslaToString(hsla: HSLAColor, options?: { mode?: 'hsl' | 'hex'; alpha?: boolean; convert?: 'fromUnit' | 'toUnit' }): string`

### Manipulation (type-preserving)

- `lighten<T extends RGBAColor | HSLAColor>(color: T, amount = 0.1): T`
- `darken<T extends RGBAColor | HSLAColor>(color: T, amount = 0.1): T`
- `saturate<T extends RGBAColor | HSLAColor>(color: T, amount = 0.1): T`
- `desaturate<T extends RGBAColor | HSLAColor>(color: T, amount = 0.1): T`
- `fadeIn<T extends RGBAColor | HSLAColor>(color: T, amount = 0.1): T`
- `fadeOut<T extends RGBAColor | HSLAColor>(color: T, amount = 0.1): T`
- `invert<T extends RGBAColor | HSLAColor>(color: T): T`
- `blend<T extends RGBAColor | HSLAColor>(color1: T, color2: T, ratio = 0.5): T`
- `hue<T extends RGBAColor | HSLAColor>(color: T, amount: number): T`

## Type Guards

The package exports runtime type guards, available both as named exports and on `ColorUtils`:

- `isRGBAColor(color: any): color is RGBAColor`
- `isHSLAColor(color: any): color is HSLAColor`
