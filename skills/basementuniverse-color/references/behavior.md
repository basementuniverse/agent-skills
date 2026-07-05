# Behavior and Edge Cases

## Parsing Defaults

- Parsing is case-insensitive and trims surrounding whitespace.
- Supported inputs include named colors, hex forms, `rgb(a)`, and `hsl(a)`.
- `stringToRGBA` returns `{ r: 0, g: 0, b: 0, a: 1 }` when parsing fails.
- `stringToHSLA` parses through RGBA then converts.

## Range Handling and Clamping

- Numeric channels are clamped to legal ranges in many paths.
- Alpha channels are clamped to `[0, 1]`.
- Lightness and saturation operations clamp to `[0, 100]`.
- Blend ratio is clamped to `[0, 1]`.

## Rounding Rules

- Many RGB/HSL channels are rounded to integers for output objects.
- Some alpha outputs are rounded to 2 decimals (notably random generation and certain blending/interpolation paths).
- String formatting rounds integer channels but preserves alpha precision in string output.

## Manipulation Semantics

- `lighten`/`darken` and `saturate`/`desaturate` use amount as a normalized fraction (0.1 = 10%).
- RGBA manipulation converts to HSLA internally (except direct alpha ops and direct RGBA blend/invert behavior).
- HSLA `invert` converts to RGBA, inverts channels, and converts back.
- `fadeIn`/`fadeOut` keep color channels and adjust only alpha.
- Manipulation helpers preserve the input color type (`RGBAColor` in -> `RGBAColor` out, likewise for HSLA).

## Random Generation Contracts

- Optional `rng` allows deterministic results.
- `RandomRange` supports either:
  - uniform via `min` + `max`
  - normal via `mean` + `stddev`
- Supplying only one of a required pair (`min` without `max`, etc.) throws.
- `randomGradient` throws if no seed colors are provided.
- Gradient sampling clamps position to `[0, 1]`.

## Mixed-Type Blend Behavior

- `blend` expects both colors to be same model (both RGBA or both HSLA).
- If models differ, it returns the first color unchanged.
