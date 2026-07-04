---
name: basementuniverse-intersection-helpers
description: >
  Use this skill when working with @basementuniverse/intersection-helpers to
  compute geometric distances, containment checks, and intersections between
  points, lines, rays, circles, polygons, rectangles, spheres, cuboids,
  planes, and meshes. Use it when choosing the correct 2D or 3D namespace,
  understanding result shapes such as closestPoint, distance,
  intersectionPoints, and minimumSeparation, or when validating polygon and
  mesh inputs before calling the library.
---

# Basement Universe Intersection Helpers

Use this skill when working with `@basementuniverse/intersection-helpers`.

This package is a focused geometry helper library, not a full collision system.
Prefer it when the task is about point tests, intersection checks, AABB helpers,
polygon and mesh utilities, or converting between related geometric
 representations.

## Package layout

The library exposes three public entry points:

- `@basementuniverse/intersection-helpers/2d`
- `@basementuniverse/intersection-helpers/3d`
- `@basementuniverse/intersection-helpers/utilities`

Choose the smallest namespace that matches the job:

- Use `2d` for points, lines, rays, circles, AABBs, rectangles, and polygons in screen-space geometry.
- Use `3d` for points, lines, rays, spheres, AABBs, cuboids, planes, triangles, and meshes.
- Use `utilities` for interval overlap helpers and floating-point tolerance helpers.

## Important conventions

## 2D conventions

- 2D uses the screen coordinate system by default, where positive Y points down.
- `Rectangle.position` is the rectangle center.
- `AABB.position` is the top-left corner.
- Polygon winding helpers default to the `screen` coordinate system unless overridden.

## 3D conventions

- The 3D namespace is present and broadly featured, but the upstream project documents it as largely untested.
- 3D `Polygon` means a triangle with exactly three vertices.
- `Cuboid.position` is the cuboid center.
- `AABB.position` is the top-left-front corner.

## Result patterns

Many functions return structured results rather than plain booleans. Agents should preserve these shapes when writing code against the library.

- Point containment helpers usually return `intersects`, `closestPoint`, and signed `distance`.
- A negative `distance` usually means the point lies inside the tested shape.
- Some 2D point helpers also return `normal` when an intersection occurs.
- Shape intersection helpers commonly return `intersectionPoint`, `intersectionPoints`, `minimumSeparation`, `penetrationDepth`, `normal`, or contact-point data depending on the geometry pair.
- Some polygon-based helpers return `null` for invalid polygon input rather than a normal result object.

## Choosing the right helper

- Use `pointIn...` and `pointOn...` helpers when you need proximity information, not just a yes/no answer.
- Use `...Intersects...` helpers when you need line, ray, shape, or mesh intersection checks.
- Use `aabb`, `aabbsOverlap`, and enclosure helpers for broad-phase checks or bounding-box workflows.
- Use polygon and mesh utility helpers before intersection tests when inputs may be malformed, concave, self-intersecting, or need decomposition.

## Guardrails

- Do not treat this package as a physics engine or general collision-resolution framework.
- Validate polygons before relying on polygon intersection results. In 2D, invalid polygons can produce `null`. In 3D, polygons must be triangles.
- Be explicit about coordinate-system assumptions when using winding-order logic in 2D.
- Be cautious with 3D behavior in high-risk code paths unless you also add or run targeted tests.

## References

- Public API surface: [references/api.md](references/api.md)
