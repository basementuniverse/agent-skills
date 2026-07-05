# Usage Patterns

## Basic Parse -> Transform -> Stringify

```ts
import { ColorUtils } from '@basementuniverse/color';

const parsed = ColorUtils.stringToRGBA('rgba(255, 0, 0, 0.7)');
const adjusted = ColorUtils.lighten(parsed, 0.1);
const css = ColorUtils.rgbaToString(adjusted);
```

## Convert Between Color Spaces

```ts
import { ColorUtils } from '@basementuniverse/color';

const rgba = { r: 32, g: 120, b: 220, a: 0.8 };
const hsla = ColorUtils.rgbaToHSLA(rgba);
const roundtrip = ColorUtils.hslaToRGBA(hsla);
```

## Hex Output Variants

```ts
import { ColorUtils } from '@basementuniverse/color';

ColorUtils.rgbaToString({ r: 255, g: 0, b: 0, a: 1 }, { mode: 'hex' });
// -> #ff0000

ColorUtils.rgbaToString({ r: 255, g: 0, b: 0, a: 1 }, { mode: 'hex', alpha: true });
// -> #ff0000ff
```

## Work in Unit Space

```ts
import { ColorUtils } from '@basementuniverse/color';

const unit = ColorUtils.toUnit({ r: 255, g: 128, b: 0, a: 0.5 });
const restored = ColorUtils.fromUnit(unit);
```

## Deterministic Random Color for Tests

```ts
import { ColorUtils } from '@basementuniverse/color';

const seq = [0.25, 0.5, 0.75];
let idx = 0;
const rng = () => seq[idx++ % seq.length];

const color = ColorUtils.randomRGBA({
  r: { min: 10, max: 20 },
  g: { min: 20, max: 40 },
  b: { min: 100, max: 200 },
  rng,
});
```

## Gradient Sampling

```ts
import { ColorUtils } from '@basementuniverse/color';

const gradientColor = ColorUtils.randomGradient(
  [
    { r: 255, g: 0, b: 0, a: 1 },
    { r: 255, g: 255, b: 0, a: 1 },
    { r: 0, g: 255, b: 0, a: 1 },
  ],
  {
    position: { mean: 0.5, stddev: 0.15 },
    space: 'rgb',
  }
);
```
