---
name: basementuniverse-color
description: >
  Use this skill when working with @basementuniverse/color for parsing, converting,
  formatting, manipulating, and generating RGBA/HSLA colors.
---

# Basement Universe Color

Use this skill when working with `@basementuniverse/color`.

The package publishes a UMD build. In bundlers such as webpack or Vite, use
named imports from `@basementuniverse/color`. When loading the build with a
browser `<script>` tag, use the `BasementUniverseColor` global namespace,
for example `BasementUniverseColor.ColorUtils`. The package does not publish
a separate native ESM build.

## Use This Skill For

- Parsing user-provided color strings into structured RGBA or HSLA values.
- Converting between RGB(A) and HSL(A) spaces.
- Formatting colors into CSS-style `rgb(a)`, `hsl(a)`, or hex strings.
- Applying color manipulations such as lighten, darken, saturate, fade, invert, and blend.
- Generating random colors (including controlled ranges and gradients) for gameplay, procedural art, and test fixtures.

## Public API Shape

The package currently exports:

- Types: `RGBAColor`, `HSLAColor`, random option types.
- Type guards: `isRGBAColor`, `isHSLAColor` (available both as named exports and on `ColorUtils`).
- `ColorUtils` object containing all runtime helpers.

Preferred usage pattern:

```ts
import { ColorUtils, type RGBAColor, type HSLAColor } from '@basementuniverse/color';

const c1: RGBAColor = ColorUtils.stringToRGBA('#ff0000');
const c2: HSLAColor = ColorUtils.rgbaToHSLA(c1);
const css = ColorUtils.hslaToString(c2);
```

Important:

- Most runtime helpers are exposed through `ColorUtils` rather than as individual named exports.
- Type guards (`isRGBAColor`, `isHSLAColor`) are exported both as named exports and on `ColorUtils`.

## Practical Guidance

- Normalize external input early using `ColorUtils.stringToRGBA`.
- Preserve caller color space where needed: manipulation helpers are type-preserving for RGBA input vs HSLA input.
- For deterministic tests, pass a custom `rng` function to random utilities.
- Clamp assumptions: many functions clamp ranges internally rather than throwing.
- For invalid color strings, `stringToRGBA` falls back to opaque black, so validate upstream if this default could hide input errors.

## Common Recipes

### Parse and normalize to hex

```ts
const parsed = ColorUtils.stringToRGBA(userInput);
const hex = ColorUtils.rgbaToString(parsed, { mode: 'hex' });
```

### Fade and stringify for CSS

```ts
const next = ColorUtils.fadeOut({ r: 255, g: 120, b: 40, a: 1 }, 0.25);
const css = ColorUtils.rgbaToString(next);
```

### Deterministic random palette sample

```ts
const rngValues = [0.1, 0.5, 0.9];
let i = 0;
const rng = () => rngValues[i++ % rngValues.length];

const sampled = ColorUtils.randomGradient(
  [{ r: 255, g: 0, b: 0 }, { r: 0, g: 0, b: 255 }],
  { position: { min: 0, max: 1 }, rng }
);
```

## References

- Public API surface: [references/api.md](references/api.md)
- Behavioral notes and edge cases: [references/behavior.md](references/behavior.md)
- Usage patterns: [references/usage.md](references/usage.md)
