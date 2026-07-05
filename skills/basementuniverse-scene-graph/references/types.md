# Scene Graph Type Reference

## Transform

Represents a 2D transformation with position, rotation, and scale.

```typescript
type Transform = {
  position: vec2;   // 2D position vector {x: number, y: number}
  rotation: number; // Rotation angle in radians
  scale: vec2;      // 2D scale factor {x: number, y: number}
}
```

### Properties

#### `position: vec2`

2D position vector from `@basementuniverse/vec`.

**Type:**
```typescript
type vec2 = {
  x: number;
  y: number;
}
```

**Usage:**
```typescript
import { vec2 } from '@basementuniverse/vec';

node.localTransform.position = vec2(100, 50);
node.localTransform.position.x = 100;
node.localTransform.position.y = 50;
```

#### `rotation: number`

Rotation angle in **radians**.

**Range:** Typically `-π` to `π`, but can be any value

**Common conversions:**
```typescript
// Degrees to radians
const radians = degrees * (Math.PI / 180);

// Radians to degrees
const degrees = radians * (180 / Math.PI);

// Common angles
const angle90deg = Math.PI / 2;    // 90 degrees
const angle180deg = Math.PI;       // 180 degrees
const angle45deg = Math.PI / 4;    // 45 degrees
```

**Usage:**
```typescript
// Rotate 90 degrees clockwise
node.localTransform.rotation = -Math.PI / 2;

// Rotate continuously
node.localTransform.rotation += 0.01 * dt;
```

#### `scale: vec2`

2D scale factors for x and y axes.

**Default:** `{x: 1, y: 1}` (no scaling)

**Values:**
- `1.0` = normal size
- `> 1.0` = larger
- `< 1.0` = smaller
- `negative` = flip/mirror

**Usage:**
```typescript
import { vec2 } from '@basementuniverse/vec';

// Uniform scale (2x size)
node.localTransform.scale = vec2(2, 2);

// Non-uniform scale (stretch horizontally)
node.localTransform.scale = vec2(2, 1);

// Flip horizontally
node.localTransform.scale.x = -1;
```

## vec2 Type

Provided by `@basementuniverse/vec` package.

```typescript
type vec2 = {
  x: number;
  y: number;
}
```

### Common Operations

While `vec2` is from a separate package, here are common operations you'll use:

```typescript
import { vec2 } from '@basementuniverse/vec';

// Creation
const v = vec2(10, 20);
const v2 = vec2.zero();  // {x: 0, y: 0}

// Addition
const sum = vec2.add(v1, v2);

// Multiplication (component-wise)
const scaled = vec2.mul(v, scaleVec);

// Rotation
const rotated = vec2.rot(v, angle);

// Length/magnitude
const length = vec2.len(v);

// Normalization
const normalized = vec2.nor(v);
```

See `@basementuniverse/vec` documentation for complete API.

## Transform Hierarchy Math

Understanding how transforms combine in the hierarchy:

### Position Inheritance

Child world position is computed as:

```typescript
worldPosition = parentWorldPosition +
                rotate(localPosition, parentWorldRotation) *
                parentWorldScale
```

**Key insight:**
- Local position is first rotated by parent's world rotation
- Then scaled by parent's world scale
- Finally added to parent's world position

**Example:**
- Parent at world position (100, 100), rotation 90°, scale (1, 1)
- Child at local position (10, 0)
- Child's rotated position: (0, 10) [rotated 90°]
- Child's world position: (100, 110)

### Rotation Inheritance

Child world rotation is the sum:

```typescript
worldRotation = parentWorldRotation + localRotation
```

**Example:**
- Parent rotated 45°
- Child rotated 45° locally
- Child's world rotation: 90°

### Scale Inheritance

Child world scale is component-wise multiplication:

```typescript
worldScale = parentWorldScale * localScale
```

**Example:**
- Parent scale (2, 2)
- Child scale (0.5, 1)
- Child's world scale: (1, 2)

## Node Lifecycle States

While not explicitly typed, nodes have implicit states:

### Attached State

```typescript
// Detached (no parent)
node.parent === null

// Attached (has parent)
node.parent !== null
```

### Visibility State

```typescript
// Visible (will draw)
node.visible === true

// Hidden (will not draw)
node.visible === false
```

Note: If a parent is hidden, all children are hidden regardless of their own `visible` flag.

### Transform Sync State

```typescript
// Out of sync (world transform outdated)
// - Happens after modifying localTransform
// - Or after parent's worldTransform changes

// In sync (world transform up to date)
// - After calling updateWorldTransform()
```

Always call `updateWorldTransform()` before `draw()` to ensure sync.
