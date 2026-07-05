# Type Reference

## Core Types

### Measurement

```typescript
type Measurement = 'auto' | `${number}px` | `${number}%`;
```

Represents a size or spacing measurement. Supports three formats:
- `'auto'` - Fills available space
- `'100px'` - Absolute pixels
- `'50%'` - Percentage of parent dimension

**Examples:**
```typescript
const measurements: Measurement[] = [
  'auto',
  '100px',
  '50%',
  '1.5px',
  '33.33%',
];
```

---

### LayoutVec2

```typescript
type LayoutVec2 = {
  x: Measurement;
  y: Measurement;
};
```

Two-dimensional measurement vector for size, offset, or padding.

**Example:**
```typescript
const size: LayoutVec2 = { x: '200px', y: 'auto' };
const padding: LayoutVec2 = { x: '10px', y: '10px' };
const offset: LayoutVec2 = { x: '5%', y: '0px' };
```

---

## Layout Configuration Types

### LayoutOptions

```typescript
type LayoutOptions = {
  root: LayoutNodeOptions;
};
```

Top-level layout configuration. Contains the root node definition.

**Example:**
```typescript
const options: LayoutOptions = {
  root: {
    id: 'root',
    type: 'leaf',
  },
};
```

---

### LayoutNodeOptions

```typescript
type LayoutNodeOptions = {
  id: string;
  type: 'dock' | 'stack' | 'leaf';
  offset?: LayoutVec2;
  padding?: LayoutVec2;
  size?: Partial<LayoutVec2>;
  minSize?: Partial<LayoutVec2>;
  maxSize?: Partial<LayoutVec2>;
  aspectRatio?: number;
  visible?: boolean;
} & (DockLayoutNodeOptions | StackLayoutNodeOptions | LeafLayoutNodeOptions);
```

Base node configuration with type-specific extensions.

**Common Properties:**
- `id` - Unique identifier (required)
- `type` - Node type: `'dock'`, `'stack'`, or `'leaf'` (required)
- `offset` - Position offset from calculated position
- `padding` - Inner padding (reduces content area by `padding * 2`)
- `size` - Explicit size (can specify only x or y)
- `minSize` - Minimum size constraints
- `maxSize` - Maximum size constraints
- `aspectRatio` - Width/height ratio (calculated after initial sizing)
- `visible` - Initial visibility state (default: `true`)

---

## Node Type Definitions

### DockLayoutNodeOptions

```typescript
type DockLayoutNodeOptions = {
  type: 'dock';
  topLeft?: LayoutNodeOptions;
  topCenter?: LayoutNodeOptions;
  topRight?: LayoutNodeOptions;
  leftCenter?: LayoutNodeOptions;
  center?: LayoutNodeOptions;
  rightCenter?: LayoutNodeOptions;
  bottomLeft?: LayoutNodeOptions;
  bottomCenter?: LayoutNodeOptions;
  bottomRight?: LayoutNodeOptions;
};
```

Dock layout positions children at specific anchor points.

**Anchor Points:**
```
topLeft     topCenter     topRight
leftCenter    center      rightCenter
bottomLeft  bottomCenter  bottomRight
```

**Example:**
```typescript
const dockNode: LayoutNodeOptions = {
  id: 'hud',
  type: 'dock',
  topLeft: {
    id: 'score',
    type: 'leaf',
    size: { x: '150px', y: '40px' },
  },
  bottomCenter: {
    id: 'controls',
    type: 'leaf',
    size: { x: '300px', y: '60px' },
  },
};
```

---

### StackLayoutNodeOptions

```typescript
type StackLayoutNodeOptions = {
  type: 'stack';
  direction: 'vertical' | 'horizontal';
  align?: 'start' | 'center' | 'end' | 'stretch';
  gap?: Measurement;
  children: LayoutNodeOptions[];
};
```

Stack layout arranges children sequentially with optional alignment and spacing.

**Properties:**
- `direction` - Stack direction (required)
  - `'vertical'` - Children stacked top to bottom
  - `'horizontal'` - Children stacked left to right
- `align` - Cross-axis alignment (optional)
  - `'start'` - Align to start (default)
  - `'center'` - Center alignment
  - `'end'` - Align to end
  - `'stretch'` - Stretch to fill cross-axis
- `gap` - Spacing between children (not at edges)
- `children` - Array of child nodes (required)

**Example:**
```typescript
const stackNode: LayoutNodeOptions = {
  id: 'menu',
  type: 'stack',
  direction: 'horizontal',
  align: 'center',
  gap: '10px',
  children: [
    { id: 'button1', type: 'leaf', size: { x: '100px', y: '40px' } },
    { id: 'button2', type: 'leaf', size: { x: '100px', y: '40px' } },
    { id: 'button3', type: 'leaf', size: { x: '100px', y: '40px' } },
  ],
};
```

---

### LeafLayoutNodeOptions

```typescript
type LeafLayoutNodeOptions = {
  type: 'leaf';
};
```

Leaf nodes are terminal nodes with no children. Used for actual content placement.

**Example:**
```typescript
const leafNode: LayoutNodeOptions = {
  id: 'content',
  type: 'leaf',
  size: { x: '400px', y: '300px' },
};
```

---

## Result Types

### CalculatedNode

```typescript
type CalculatedNode = {
  // Center
  center: vec2;

  // Corners
  topLeft: vec2;
  topRight: vec2;
  bottomLeft: vec2;
  bottomRight: vec2;

  // Edge centers
  topCenter: vec2;
  bottomCenter: vec2;
  leftCenter: vec2;
  rightCenter: vec2;

  // Edges
  top: number;
  bottom: number;
  left: number;
  right: number;

  // Size
  width: number;
  height: number;

  // Other
  aspectRatio: number;
  activated: boolean;
  visible: boolean;
};
```

Result of layout calculation for a node. Contains all calculated positions, dimensions, and state.

**Usage Pattern:**
```typescript
const node = layout.get('myNode');
if (node) {
  // All coordinates are in pixels
  ctx.fillRect(node.left, node.top, node.width, node.height);

  // Access any anchor point
  drawIcon(node.topRight.x, node.topRight.y);

  // Check state
  if (node.visible && node.activated) {
    renderContent();
  }
}
```

---

## Type Examples

### Complete Layout Configuration

```typescript
import { LayoutOptions } from '@basementuniverse/layout';

const config: LayoutOptions = {
  root: {
    id: 'root',
    type: 'stack',
    direction: 'vertical',
    padding: { x: '10px', y: '10px' },
    children: [
      {
        id: 'header',
        type: 'dock',
        size: { y: '60px' },
        topLeft: {
          id: 'logo',
          type: 'leaf',
          size: { x: '120px', y: '40px' },
          offset: { x: '10px', y: '10px' },
        },
        topRight: {
          id: 'menu',
          type: 'stack',
          direction: 'horizontal',
          gap: '5px',
          offset: { x: '-10px', y: '10px' },
          children: [
            { id: 'home', type: 'leaf', size: { x: '80px', y: '30px' } },
            { id: 'about', type: 'leaf', size: { x: '80px', y: '30px' } },
          ],
        },
      },
      {
        id: 'content',
        type: 'stack',
        direction: 'horizontal',
        gap: '10px',
        children: [
          {
            id: 'sidebar',
            type: 'leaf',
            size: { x: '200px' },
            minSize: { x: '150px' },
            maxSize: { x: '300px' },
          },
          {
            id: 'main',
            type: 'leaf',
            aspectRatio: 16 / 9,
          },
        ],
      },
      {
        id: 'footer',
        type: 'leaf',
        size: { y: '40px' },
      },
    ],
  },
};
```

### Partial Configuration

```typescript
// size, minSize, maxSize can be partial
const node: LayoutNodeOptions = {
  id: 'panel',
  type: 'leaf',
  size: { x: '200px' }, // y will be 'auto'
  minSize: { y: '100px' }, // Only constrain height
};
```

### Type-Safe Node Creation

```typescript
function createButton(id: string, label: string): LayoutNodeOptions {
  return {
    id,
    type: 'leaf',
    size: { x: '120px', y: '40px' },
  };
}

function createMenu(items: string[]): LayoutNodeOptions {
  return {
    id: 'menu',
    type: 'stack',
    direction: 'horizontal',
    gap: '10px',
    children: items.map(item => createButton(item, item)),
  };
}
```
