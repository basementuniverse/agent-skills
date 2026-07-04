# API Reference

This reference summarizes the public API exposed by `@basementuniverse/intersection-helpers`.

## Entry points

```ts
import * as intersection2d from '@basementuniverse/intersection-helpers/2d';
import * as intersection3d from '@basementuniverse/intersection-helpers/3d';
import * as intersectionUtilities from '@basementuniverse/intersection-helpers/utilities';
```

## 2D

Use `@basementuniverse/intersection-helpers/2d` for planar geometry in screen-space coordinates.

### Types

```ts
type Point = vec2;

type Ray = {
  origin: Point;
  direction: vec2;
};

type Line = {
  start: Point;
  end: Point;
};

type Circle = {
  position: Point;
  radius: number;
};

type AABB = {
  position: Point;
  size: vec2;
};

type Rectangle = {
  position: Point;
  size: vec2;
  rotation?: number;
};

type Polygon = {
  vertices: Point[];
};
```

### Core utilities

```ts
distance(a: Point, b: Point): number

angle(a: Point, b: Point): number

angleBetween(a: Line | Ray, b: Line | Ray): number

pointsAreCollinear(a: Point, b: Point, c: Point): boolean

lineToRay(line: Line): Ray

rayToLine(ray: Ray, length?: number): Line
```

### AABB helpers

```ts
aabb(o: Line | Rectangle | Circle | Polygon): AABB | null

aabbToRectangle(aabb: AABB): Rectangle

aabbsOverlap(a: AABB, b: AABB): {
  intersects: boolean;
  overlap?: AABB;
}

pointInAABB(point: Point, aabb: AABB): {
  intersects: boolean;
  closestPoint: Point;
  distance: number;
  normal?: vec2;
}

encloseAABBs(...aabbs: AABB[]): AABB
```

### Rectangle helpers

```ts
rectangleIsRotated(rectangle: Rectangle): boolean

rectangleVertices(rectangle: Rectangle): Point[]
```

### Polygon helpers

```ts
verticesToEdges(vertices: Point[]): Line[]

polygonIsConvex(polygon: Polygon): boolean | null

polygonSelfIntersects(polygon: Polygon): boolean

polygonIsValid(polygon: Polygon): boolean

polygonWindingOrder(
  polygon: Polygon,
  options?: { coordinateSystem?: 'cartesian' | 'screen' }
): 'clockwise' | 'counter-clockwise' | null

polygonArea(polygon: Polygon): number | null

polygonCentroid(polygon: Polygon): Point | null

polygonConvexHull(
  polygon: Polygon,
  options?: { keepWindingOrder?: boolean }
): Polygon | null

optimisePolygon(polygon: Polygon): Polygon | null

decomposePolygon(
  polygon: Polygon,
  options?: {
    mode?: 'fast' | 'optimal';
    keepWindingOrder?: boolean;
  }
): Polygon[] | null
```

### Point tests

```ts
pointOnRay(point: Point, ray: Ray): {
  intersects: boolean;
  closestPoint: Point;
  distance: number;
  normal?: vec2;
}

pointOnLine(point: Point, line: Line): {
  intersects: boolean;
  closestPoint: Point;
  distance: number;
  normal?: vec2;
}

pointInCircle(point: Point, circle: Circle): {
  intersects: boolean;
  closestPoint: Point;
  distance: number;
  normal?: vec2;
}

pointInRectangle(point: Point, rectangle: Rectangle): {
  intersects: boolean;
  closestPoint: Point;
  distance: number;
  normal?: vec2;
}

pointInPolygon(point: Point, polygon: Polygon): {
  intersects: boolean;
  closestPoint: Point;
  distance: number;
  normal?: vec2;
} | null
```

### Ray helpers

```ts
rayTraverseGrid(
  ray: Ray,
  cellSize: number,
  gridTopLeft: vec2,
  gridBottomRight: vec2,
  maxCells?: number
): { cells: Point[] }

rayIntersectsRay(rayA: Ray, rayB: Ray): {
  intersects: boolean;
  intersectionPoint?: Point;
}

rayIntersectsLine(ray: Ray, line: Line): {
  intersects: boolean;
  intersectionPoint?: Point;
}

rayIntersectsCircle(ray: Ray, circle: Circle): {
  intersects: boolean;
  intersectionPoints?: Point[];
}

rayIntersectsRectangle(ray: Ray, rectangle: Rectangle): {
  intersects: boolean;
  intersectionPoints?: Point[];
}

rayIntersectsPolygon(ray: Ray, polygon: Polygon): {
  intersects: boolean;
  intersectionPoints?: Point[];
} | null
```

### Line helpers

```ts
lineIntersectsRay(line: Line, ray: Ray): {
  intersects: boolean;
  intersectionPoint?: Point;
}

lineIntersectsLine(lineA: Line, lineB: Line): {
  intersects: boolean;
  intersectionPoint?: Point;
}

lineIntersectsCircle(line: Line, circle: Circle): {
  intersects: boolean;
  intersectionPoints?: Point[];
}

lineIntersectsRectangle(line: Line, rectangle: Rectangle): {
  intersects: boolean;
  intersectionPoints?: Point[];
}

lineIntersectsPolygon(line: Line, polygon: Polygon): {
  intersects: boolean;
  intersectionPoints?: Point[];
} | null
```

### Shape intersections

```ts
circleIntersectsCircle(circleA: Circle, circleB: Circle): {
  intersects: boolean;
  intersectionPoints?: Point[];
  minimumSeparation?: vec2;
}

circleIntersectsRectangle(circle: Circle, rectangle: Rectangle): {
  intersects: boolean;
  intersectionPoints?: Point[];
  minimumSeparation?: vec2;
}

circleIntersectsPolygon(
  circle: Circle,
  polygon: Polygon,
  options?: { findMinimumSeparation?: boolean }
): {
  intersects: boolean;
  intersectionPoints?: Point[];
  minimumSeparation?: vec2;
} | null

rectangleIntersectsRectangle(rectangleA: Rectangle, rectangleB: Rectangle): {
  intersects: boolean;
  intersectionPoints?: Point[];
  minimumSeparation?: vec2;
}

rectangleIntersectsPolygon(rectangle: Rectangle, polygon: Polygon): {
  intersects: boolean;
  intersectionPoints?: Point[];
} | null

polygonIntersectsPolygon(polygonA: Polygon, polygonB: Polygon): {
  intersects: boolean;
  intersectionPoints?: Point[];
} | null
```

## 3D

Use `@basementuniverse/intersection-helpers/3d` for 3D geometry. The upstream project notes that this namespace is largely untested.

### Types

```ts
type Point = vec3;

type Ray = {
  origin: Point;
  direction: vec3;
};

type Line = {
  start: Point;
  end: Point;
};

type Sphere = {
  position: Point;
  radius: number;
};

type AABB = {
  position: Point;
  size: vec3;
};

type Cuboid = {
  position: Point;
  size: vec3;
  rotation?: vec3;
};

type Plane = {
  point: Point;
  normal: vec3;
};

type Polygon = {
  vertices: [Point, Point, Point];
};

type Mesh = {
  vertices: Point[];
  indices: number[];
};
```

### Core utilities

```ts
distance(a: Point, b: Point): number

angle(a: Point, b: Point): vec3

angleBetween(a: Line | Ray, b: Line | Ray): number

pointsAreCollinear(a: Point, b: Point, c: Point): boolean

lineToRay(line: Line): Ray

rayToLine(ray: Ray, length?: number): Line
```

### AABB, cuboid, polygon, and mesh helpers

```ts
aabb(o: Line | Sphere | Cuboid | Polygon | Mesh): AABB | null

aabbToCuboid(aabb: AABB): Cuboid

aabbsOverlap(a: AABB, b: AABB): {
  intersects: boolean;
  overlap?: AABB;
}

pointInAABB(point: Point, aabb: AABB): {
  intersects: boolean;
  closestPoint: Point;
  distance: number;
}

encloseAABBs(...aabbs: AABB[]): AABB

cuboidIsRotated(cuboid: Cuboid): boolean

cuboidVertices(cuboid: Cuboid): Point[]

cuboidToPolygons(cuboid: Cuboid): Polygon[]

verticesToEdges(vertices: Point[]): Line[]

polygonIsValid(polygon: Polygon): boolean

polygonWindingOrder(
  polygon: Polygon,
  options?: { handedness?: 'right' | 'left'; normal?: Point }
): 'clockwise' | 'counter-clockwise' | null

polygonArea(polygon: Polygon): number | null

polygonCentroid(polygon: Polygon): Point | null

polygonToPlane(polygon: Polygon): Plane | null

polygonsToMesh(polygons: Polygon[]): Mesh

meshToPolygons(mesh: Mesh): Polygon[]

meshToEdges(mesh: Mesh): Line[]

meshCentroid(mesh: Mesh): Point

meshIsWatertight(mesh: Mesh): boolean
```

### Point tests

```ts
pointOnRay(point: Point, ray: Ray): {
  intersects: boolean;
  closestPoint?: Point;
  distance: number;
}

pointOnLine(point: Point, line: Line): {
  intersects: boolean;
  closestPoint: Point;
  distance: number;
}

pointInSphere(point: Point, sphere: Sphere): {
  intersects: boolean;
  closestPoint: Point;
  distance: number;
}

pointInCuboid(point: Point, cuboid: Cuboid): {
  intersects: boolean;
  closestPoint: Point;
  distance: number;
}

pointOnPolygon(point: Point, polygon: Polygon): {
  intersects: boolean;
  closestPoint: Point;
  distance: number;
} | null
```

### Ray and line helpers

```ts
rayTraverseGrid(
  ray: Ray,
  cellSize: number,
  gridTopLeftFront: vec3,
  gridBottomRightBack: vec3,
  maxCells?: number
): { cells: Point[] }

rayIntersectsRay(rayA: Ray, rayB: Ray): {
  intersects: boolean;
  intersectionPoint?: Point;
}

rayIntersectsLine(ray: Ray, line: Line): {
  intersects: boolean;
  intersectionPoint?: Point;
}

rayIntersectsSphere(ray: Ray, sphere: Sphere): {
  intersects: boolean;
  intersectionPoints?: Point[];
}

rayIntersectsPlane(ray: Ray, plane: Plane): {
  intersects: boolean;
  intersectionPoint?: Point;
}

rayIntersectsCuboid(ray: Ray, cuboid: Cuboid): {
  intersects: boolean;
  intersectionPoints?: Point[];
}

rayIntersectsPolygon(ray: Ray, polygon: Polygon): {
  intersects: boolean;
  intersectionPoint?: Point;
} | null

rayIntersectsMesh(ray: Ray, mesh: Mesh): {
  intersects: boolean;
  intersectionPoints?: Point[];
}

lineIntersectsRay(line: Line, ray: Ray): {
  intersects: boolean;
  intersectionPoint?: Point;
}

lineIntersectsLine(lineA: Line, lineB: Line): {
  intersects: boolean;
  intersectionPoint?: Point;
}

lineIntersectsSphere(line: Line, sphere: Sphere): {
  intersects: boolean;
  intersectionPoints?: Point[];
}

lineIntersectsPlane(line: Line, plane: Plane): {
  intersects: boolean;
  intersectionPoint?: Point;
}

lineIntersectsCuboid(line: Line, cuboid: Cuboid): {
  intersects: boolean;
  intersectionPoints?: Point[];
}

lineIntersectsPolygon(line: Line, polygon: Polygon): {
  intersects: boolean;
  intersectionPoint?: Point;
}

lineIntersectsMesh(line: Line, mesh: Mesh): {
  intersects: boolean;
  intersectionPoints?: Point[];
}
```

### Shape, plane, polygon, and mesh intersections

```ts
sphereIntersectsSphere(sphereA: Sphere, sphereB: Sphere): {
  intersects: boolean;
  intersectionPoint?: Point;
  penetrationDepth?: number;
  normal?: Point;
  contactPoints?: { sphereA: Point; sphereB: Point };
}

sphereIntersectsPlane(sphere: Sphere, plane: Plane): {
  intersects: boolean;
  intersectionPoint?: Point;
  penetrationDepth?: number;
  intersectionRadius?: number;
}

sphereIntersectsCuboid(sphere: Sphere, cuboid: Cuboid): {
  intersects: boolean;
  intersectionPoint?: Point;
  penetrationDepth?: number;
  normal?: Point;
  contactPoint?: Point;
}

sphereIntersectsPolygon(sphere: Sphere, polygon: Polygon): {
  intersects: boolean;
  intersectionPoint?: Point;
  penetrationDepth?: number;
  polygonIntersectionPoints?: Point[];
} | null

sphereIntersectsMesh(sphere: Sphere, mesh: Mesh): {
  intersects: boolean;
  intersectionPoints?: Point[];
  polygonIntersectionPoints?: Point[];
}

planeIntersectsPlane(planeA: Plane, planeB: Plane): {
  intersects: boolean;
  intersectionLine?: Line;
}

planeIntersectsMesh(plane: Plane, mesh: Mesh): {
  intersects: boolean;
  intersectionPoints?: Point[];
  penetrationDepth?: number;
}

cuboidIntersectsCuboid(cuboidA: Cuboid, cuboidB: Cuboid): {
  intersects: boolean;
  intersectionPoint?: Point;
  penetrationDepth?: number;
  normal?: Point;
  contactPoints?: { cuboidA: Point; cuboidB: Point };
}

cuboidIntersectsPlane(cuboid: Cuboid, plane: Plane): {
  intersects: boolean;
  intersectionPoints?: Point[];
  penetrationDepth?: number;
}

cuboidIntersectsPolygon(cuboid: Cuboid, polygon: Polygon): {
  intersects: boolean;
  intersectionPoints?: Point[];
} | null

cuboidIntersectsMesh(cuboid: Cuboid, mesh: Mesh): {
  intersects: boolean;
  intersectionPoints?: Point[];
}

polygonIntersectsPolygon(polygonA: Polygon, polygonB: Polygon): {
  intersects: boolean;
  intersectionPoints?: Point[];
} | null

polygonIntersectsPlane(polygon: Polygon, plane: Plane): {
  intersects: boolean;
  intersectionPoints?: Point[];
} | null

polygonIntersectsMesh(polygon: Polygon, mesh: Mesh): {
  intersects: boolean;
  intersectionPoints?: Point[];
} | null

meshIntersectsMesh(meshA: Mesh, meshB: Mesh): {
  intersects: boolean;
  intersectionPoints?: Point[];
}

meshIntersectsPlane(mesh: Mesh, plane: Plane): {
  intersects: boolean;
  intersectionPoints?: Point[];
  penetrationDepth?: number;
}
```

## Utilities

Use `@basementuniverse/intersection-helpers/utilities` for tolerance checks and interval operations.

### Types

```ts
type Interval = {
  min: number;
  minInclusive?: boolean;
  max: number;
  maxInclusive?: boolean;
};
```

### Functions

```ts
vectorAlmostZero(v: vec2 | vec3): boolean

vectorsAlmostEqual(a: vec2 | vec3, b: vec2 | vec3): boolean

valueInInterval(value: number, interval: Interval): boolean

intervalsOverlap(a: Interval, b: Interval): boolean

overlapInterval(a: Interval, b: Interval): Interval | null
```
