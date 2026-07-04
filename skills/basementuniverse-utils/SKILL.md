---
name: basementuniverse-utils
description: >
  Use and troubleshoot the @basementuniverse/utils JavaScript utility library
  for math, random, array, combinatorics, and object-transform helpers.
  Use when implementing, debugging, or documenting behavior that depends on
  this package.
---

# Basement Universe Utils

Use this skill when working with `@basementuniverse/utils`.

This package provides small, mostly pure utility functions with no runtime
dependencies. It is commonly used for interpolation math, random sampling,
array reshaping, combinatorics, and object key/value transformations.

## When to use

Use this skill for:

- Choosing existing helpers instead of writing one-off utility code.
- Implementing interpolation or range mapping (`lerp`, `unlerp`, `remap`, `smoothstep`, `lerpArray`).
- Implementing random selection and weighted choice (`randomBetween`, `weightedRandom`, CLT helpers).
- Performing array/combinatorics operations (`zip`, `chunk`, `cartesian`, `permutations`, `combinations`).
- Flattening/unflattening or transforming object structures (`flat`, `unflat`, `transform`).
- Reviewing behavior around edge cases (inclusive/exclusive bounds, wrapping indices, predicate split behavior).

Do not use this skill for:

- Cryptographically secure randomness.
- Deep clone/immutability frameworks beyond the utility semantics provided here.
- Performance-critical large combinatorics generation without explicit size checks.

## Workflow

1. Identify the task category:
   - numeric/interpolation
   - random/sampling
   - array indexing/reshaping
   - combinatorics
   - object structure transforms
2. Pick the narrowest helper that matches behavior exactly (especially bounds and inclusivity).
3. Validate key semantics:
   - random interval: float `[min, max)`, int `[min, max]`
   - `split`: matching item begins the next chunk
   - `shuffle`: returns a shuffled shallow copy
   - `transform`: recurses plain objects, preserves arrays as arrays
4. Check type expectations against TypeScript declarations when writing TS usage.
5. Add tests for edge behavior where regressions are common (empty arrays, negative indices, omitted transforms, date flattening).

## Troubleshooting checklist

- Interpolation output looks wrong:
  - verify interpolation factor range (usually `0..1`)
  - verify `remap` source range is non-zero (`a1 !== a2`)
  - verify `lerpArray` input array has expected length/order
- Random values violate expected range:
  - use `randomBetween` for float upper-exclusive
  - use `randomIntBetween` for integer upper-inclusive
  - ensure `weightedRandom` weights are non-negative and sum to a useful total
- Array split/chunk behavior is unexpected:
  - confirm `split` keeps the predicate-match item in the new chunk
  - confirm `chunk` keeps a final short chunk when length is not divisible
- Object transform shape is unexpected:
  - `flat` converts `Date` to ISO string
  - `unflat` infers arrays when the next key segment is numeric-like
  - `transform` does not recurse into arrays; value transform handles array values as whole values
- Memoization appears to collide:
  - `memoize` uses argument tuple stringification as cache key; object arguments may collide

## References

- Public API reference: [references/api.md](references/api.md)
