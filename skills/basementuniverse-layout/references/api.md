# API Reference

## Layout Class

### Constructor

```typescript
constructor(options?: Partial<LayoutOptions>)
```

Creates a new layout instance.

**Parameters:**
- `options.root` - Root node configuration (default: `{ id: 'default', type: 'leaf' }`)

**Example:**
```typescript
const layout = new Layout({
  root: {
    id: 'root',
    type: 'stack',
    direction: 'vertical',
    children: [/* ... */],
  },
});
```

---

### update()

```typescript
update(size: vec2, offset?: vec2): void
```

Calculates layout based on canvas/viewport size and optional offset.

**Parameters:**
- `size` - Canvas dimensions `{ x: number, y: number }`
- `offset` - Optional position offset (default: `{ x: 0, y: 0 }`)

**Behavior:**
- Must be called before `get()` to obtain calculated positions
- Uses cached results if size/offset/activation states unchanged
- Automatically skips deactivated nodes

**Example:**
```typescript
layout.update({ x: 1024, y: 768 });
layout.update({ x: 800, y: 600 }, { x: 10, y: 10 });
```

---

### get()

```typescript
get(id: string): CalculatedNode | null
```

Retrieves calculated node data by ID.

**Parameters:**
- `id` - Unique node identifier

**Returns:**
- `CalculatedNode` object with positions and dimensions, or `null` if not found

**Example:**
```typescript
const header = layout.get('header');
if (header) {
  console.log(`Size: ${header.width}x${header.height}`);
  console.log(`Position: (${header.left}, ${header.top})`);
}
```

---

### setVisibility()

```typescript
setVisibility(id: string, visible?: boolean): void
```

Sets visibility state for node and all descendants.

**Parameters:**
- `id` - Node identifier
- `visible` - `true` (visible), `false` (hidden), `undefined` (toggle current state)

**Behavior:**
- Affects rendering but NOT layout calculations
- Invisible nodes still occupy space
- Recursively applies to all children
- Does NOT invalidate cache

**Example:**
```typescript
layout.setVisibility('sidebar', false); // Hide
layout.setVisibility('modal', true);    // Show
layout.setVisibility('menu');           // Toggle
```

---

### setActivated()

```typescript
setActivated(id: string, activated?: boolean): void
```

Sets activation state for node and all descendants.

**Parameters:**
- `id` - Node identifier
- `activated` - `true` (active), `false` (inactive), `undefined` (toggle current state)

**Behavior:**
- Deactivated nodes excluded from layout calculations
- Affects space distribution among siblings
- Marks layout as dirty (forces recalculation)
- Recursively applies to all children
- Invalidates cache

**Example:**
```typescript
layout.setActivated('sidebar', false); // Remove from layout
layout.setActivated('panel', true);    // Include in layout
layout.setActivated('menu');           // Toggle
```

---

### hasNode()

```typescript
hasNode(id: string): boolean
```

Checks if a node exists in the layout.

**Parameters:**
- `id` - Node identifier

**Returns:**
- `true` if node exists, `false` otherwise

**Example:**
```typescript
if (layout.hasNode('sidebar')) {
  layout.setVisibility('sidebar', false);
}
```

---

### getNodeIds()

```typescript
getNodeIds(): string[]
```

Returns all node IDs in the layout.

**Returns:**
- Array of all node identifiers

**Example:**
```typescript
const ids = layout.getNodeIds();
console.log(`Layout contains ${ids.length} nodes`);
ids.forEach(id => console.log(id));
```

---

### clearCache()

```typescript
clearCache(): void
```

Clears calculation cache and marks layout as dirty.

**Behavior:**
- Forces recalculation on next `update()`
- Useful for debugging or memory management
- Rarely needed in normal usage

**Example:**
```typescript
layout.clearCache();
layout.update(size); // Will recalculate everything
```

---

### getCacheStats()

```typescript
getCacheStats(): { size: number; dirty: boolean }
```

Returns cache statistics for debugging and monitoring.

**Returns:**
- `size` - Number of cached calculations
- `dirty` - Whether recalculation is needed

**Example:**
```typescript
const stats = layout.getCacheStats();
console.log(`Cache has ${stats.size} entries`);
console.log(`Needs update: ${stats.dirty}`);
```

---

## CalculatedNode Properties

The `CalculatedNode` object returned by `get()` contains:

### Position Properties (vec2)

All position properties are `{ x: number, y: number }` vectors:

- `center` - Center point of the node
- `topLeft` - Top-left corner
- `topRight` - Top-right corner
- `bottomLeft` - Bottom-left corner
- `bottomRight` - Bottom-right corner
- `topCenter` - Center of top edge
- `bottomCenter` - Center of bottom edge
- `leftCenter` - Center of left edge
- `rightCenter` - Center of right edge

### Edge Properties (number)

- `top` - Y coordinate of top edge
- `bottom` - Y coordinate of bottom edge
- `left` - X coordinate of left edge
- `right` - X coordinate of right edge

### Size Properties (number)

- `width` - Node width in pixels
- `height` - Node height in pixels

### State Properties

- `aspectRatio` - Calculated width/height ratio (number)
- `activated` - Activation state (boolean)
- `visible` - Visibility state (boolean)

### Example Usage

```typescript
const node = layout.get('header');
if (node) {
  // Draw a rectangle
  ctx.fillRect(node.left, node.top, node.width, node.height);

  // Draw centered text
  ctx.fillText('Header', node.center.x, node.center.y);

  // Draw at specific corner
  ctx.drawImage(icon, node.topLeft.x, node.topLeft.y);

  // Check state
  if (node.visible && node.activated) {
    // Render content
  }
}
```

---

## Type Guards and Utilities

### Checking Node Types

```typescript
// No built-in type guards, but you can check options:
const node = layout.get('myNode');
if (node) {
  // Node options are not directly accessible from CalculatedNode
  // Use layout structure knowledge or maintain separate mapping
}
```

### Working with vec2

The library uses `@basementuniverse/vec` for 2D vectors:

```typescript
import { vec2 } from '@basementuniverse/vec';

// Create vectors
const size = vec2(1024, 768);
const offset = vec2(10, 10);

// Vector operations (if needed)
const center = vec2.add(position, vec2.scale(size, 0.5));
```
