# @basementuniverse/utils API Reference

This reference summarizes the exported API from `@basementuniverse/utils` and key behavior details verified from implementation and tests.

## Import patterns

Node/CommonJS:

```js
const utils = require('@basementuniverse/utils');
const { clamp, range } = require('@basementuniverse/utils');
```

TypeScript:

```ts
import * as utils from '@basementuniverse/utils';
```

## Exported functions

### Math and interpolation

- `memoize(f: Function): Function`
  - Memoizes by argument tuple stringification (`cache[args]`).
  - Best with primitive arguments.
- `floatEquals(a: number, b: number, p = Number.EPSILON): boolean`
- `clamp(a: number, min = 0, max = 1): number`
- `frac(a: number): number`
  - Handles negative values with sign-aware behavior.
- `round(n: number, d = 0): number`
- `lerp(a: number, b: number, i: number): number`
- `unlerp(a: number, b: number, i: number): number`
- `blerp(c00: number, c10: number, c01: number, c11: number, ix: number, iy: number): number`
- `remap(i: number, a1: number, a2: number, b1: number, b2: number): number`
  - Undefined behavior when `a1 === a2` (division by zero).
- `smoothstep(a: number, b: number, i: number): number`
- `radians(degrees: number): number`
- `degrees(radians: number): number`

### Random helpers

- `randomBetween(min: number, max: number): number`
  - Float in `[min, max)`.
- `randomIntBetween(min: number, max: number): number`
  - Integer in `[min, max]`.
- `cltRandom(mu = 0.5, sigma = 0.5, samples = 2): number`
  - Approximate normal distribution from uniform sampling.
- `cltRandomInt(min: number, max: number): number`
  - Integer derived from `cltRandom`.
- `weightedRandom(w: number[]): number`
  - Returns an index based on relative weights.

### Arrays, vectors, and indexing

- `lerpArray(a: number[], i: number, f = lerp): number`
  - Interpolates between adjacent values across normalized position `i`.
- `dot(a: number[], b: number[]): number`
- `times(f: (i: number) => any, n: number): any[]`
- `range(n: number): number[]`
- `zip(...a: any[][]): any[][]`
  - Output length equals longest input array length.
  - Missing values become `undefined`.
- `at(a: any[], i: number): any`
  - Positive and negative wrapped indexing.
- `peek<T>(a: T[]): T | undefined`
- `ind(x: number, y: number, w: number): number`
- `pos(i: number, w: number): [number, number]`
- `chunk(a: any[], n: number): any[][]`
  - Keeps a final short chunk when needed.
- `shuffle(a: any[]): any[]`
  - Shuffles a shallow copy, original input is not mutated.

### Combinatorics

- `factorial(a: number): number`
- `npr(n: number, r: number): number`
- `ncr(n: number, r: number): number`
- `permutations<T>(a: T[], r: number): T[][]`
  - Order-sensitive combinations of length `r`.
- `combinations<T>(a: T[], r: number): T[][]`
  - Order-insensitive combinations of length `r`.
- `cartesian<T extends any[][]>(...arr: T): MapCartesian<T>[]`
  - Cartesian product of input arrays.

### Object helpers

- `flat(o: object, concatenator = '.'): object`
  - Flattens nested structures into concatenated keys.
  - `Date` values are converted to ISO strings.
- `unflat(o: object, concatenator = '.'): object`
  - Reconstructs nested objects from concatenated keys.
  - Creates arrays when next segment appears numeric.
- `split<T>(array: T[], predicate: (value: T) => boolean): T[][]`
  - Starts a new chunk when predicate is true, including the matching item in the new chunk.
- `pluck<T, K extends keyof T>(o: T, ...keys: K[]): Pick<T, K>`
- `exclude<T>(o: T, ...keys): Omit<T, keys>`
- `transform(o: object, kf?, vf?): object`
  - Recursively transforms plain objects only.
  - Arrays are preserved as arrays.
  - `kf` can return `null` to omit a key.

## Callback signatures

- `InterpolationFunction`: `(a: number, b: number, i: number) => number`
- `TimesFunction`: `(i: number) => any`
- `SplitPredicate`: `(value: any) => boolean`
- `KeyTransformFunction`: `(o: object, path: string, key: string, value: any) => string | null`
- `ValueTransformFunction`: `(o: object, path: string, key: string, value: any) => any`

## Practical notes

- The package exports named functions from CommonJS `module.exports`.
- Most helpers are side-effect free.
- Random-related helpers rely on `Math.random()` and are not suitable for cryptographic use.
- Recursive combinatorics helpers can grow quickly; validate input size before using with large `n`.
