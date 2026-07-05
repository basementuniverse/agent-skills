---
name: basementuniverse-layout
description: >
  Use this skill when working with @basementuniverse/layout for HTML5 Canvas game UI layouts.
  Invoke for questions about flexible layout systems, dock/stack/leaf node hierarchies,
  measurement units (px, %, auto), node positioning, visibility/activation control, or
  layout caching. Also use when implementing responsive UI elements, creating nested layouts,
  or troubleshooting layout calculations in Canvas-based games.
---

# Basement Universe Layout

Use this skill when working with `@basementuniverse/layout`.

## Overview

`@basementuniverse/layout` is a TypeScript library for managing flexible, nested layouts in HTML5 Canvas games and applications. It provides a declarative API for defining UI component positions and sizes that automatically adapt to canvas dimensions.

## Key Concepts

### Node Types

The library supports three types of layout nodes:

1. **Dock Layout** - Positions children at specific anchor points (topLeft, center, bottomRight, etc.)
2. **Stack Layout** - Arranges children sequentially in vertical or horizontal stacks with alignment and spacing
3. **Leaf Layout** - Terminal nodes with no children, used for actual content placement

### Measurement System

All size and spacing values support three measurement types:
- `'100px'` - Absolute pixel values
- `'50%'` - Percentage of parent dimension
- `'auto'` - Fills available space (or distributes equally in stacks)

### Activation vs Visibility

- **Activation** - Controls whether a node participates in layout calculations. Deactivated nodes are excluded entirely and don't affect space distribution.
- **Visibility** - Controls rendering state. Invisible nodes still participate in layout and occupy space.

## Common Use Cases

### Creating a Basic Layout

```typescript
import { Layout } from '@basementuniverse/layout';

const layout = new Layout({
  root: {
    id: 'root',
    type: 'stack',
    direction: 'vertical',
    children: [
      { id: 'header', type: 'leaf', size: { y: '60px' } },
      { id: 'content', type: 'leaf' },
      { id: 'footer', type: 'leaf', size: { y: '40px' } },
    ],
  },
});

// Calculate layout for canvas size
layout.update({ x: 1024, y: 768 });

// Get calculated positions
const header = layout.get('header');
// Use header.topLeft, header.width, header.height for rendering
```

### Nested Layouts

```typescript
const layout = new Layout({
  root: {
    id: 'root',
    type: 'stack',
    direction: 'vertical',
    padding: { x: '10px', y: '10px' },
    children: [
      {
        id: 'content',
        type: 'stack',
        direction: 'horizontal',
        gap: '10px',
        children: [
          { id: 'sidebar', type: 'leaf', size: { x: '200px' } },
          { id: 'main', type: 'leaf' }, // auto-fills remaining width
        ],
      },
    ],
  },
});
```

### Dock Layout with HUD Elements

```typescript
const layout = new Layout({
  root: {
    id: 'root',
    type: 'dock',
    topLeft: { id: 'score', type: 'leaf', size: { x: '150px', y: '40px' } },
    topRight: { id: 'timer', type: 'leaf', size: { x: '100px', y: '40px' } },
    bottomCenter: {
      id: 'controls',
      type: 'leaf',
      size: { x: '300px', y: '60px' }
    },
  },
});
```

### Dynamic Visibility Control

```typescript
// Toggle sidebar visibility
layout.setVisibility('sidebar', false); // Hide but keep space
layout.setActivated('sidebar', false);  // Remove from layout entirely

// Get current state
const node = layout.get('sidebar');
if (node?.visible) {
  // Render the node
}
```

## Important Behaviors

### Stack Auto-Sizing
In stack layouts, `'auto'` sized children equally share remaining space after fixed-size children are calculated:

```typescript
{
  type: 'stack',
  direction: 'vertical',
  children: [
    { id: 'fixed', type: 'leaf', size: { y: '100px' } }, // Fixed 100px
    { id: 'auto1', type: 'leaf' }, // Gets 50% of remaining space
    { id: 'auto2', type: 'leaf' }, // Gets 50% of remaining space
  ],
}
```

### Padding Behavior
Padding is applied to a node's content area, reducing available space for children by `padding * 2`:

```typescript
{
  id: 'container',
  type: 'stack',
  padding: { x: '10px', y: '10px' }, // 10px on all sides
  // Children have 20px less width and height available
}
```

### Aspect Ratio Constraints
When `aspectRatio` is specified, one dimension can be calculated from the other:

```typescript
{
  id: 'portrait',
  type: 'leaf',
  size: { x: '200px' },
  aspectRatio: 0.75, // 3:4 ratio, height = 200 / 0.75 = 266.67px
}
```

### Caching System
The layout caches calculated results based on:
- Canvas size and offset
- Activation state of all nodes

Cache is invalidated when:
- `setActivated()` is called
- `clearCache()` is explicitly called

Visibility changes do NOT invalidate cache.

## Workflow

1. **Define Layout Structure** - Create nested node hierarchy with IDs
2. **Update Layout** - Call `update(canvasSize)` to calculate positions
3. **Retrieve Nodes** - Use `get(id)` to get calculated positions/sizes
4. **Render** - Draw UI elements using calculated coordinates
5. **Handle Interactions** - Toggle visibility/activation as needed

## Common Patterns

### Responsive Sidebar
```typescript
// Collapse sidebar on narrow screens
if (canvasWidth < 800) {
  layout.setActivated('sidebar', false);
}
```

### Centered Modal
```typescript
{
  type: 'dock',
  center: {
    id: 'modal',
    type: 'leaf',
    size: { x: '400px', y: '300px' },
  },
}
```

### Menu Bar with Icons
```typescript
{
  type: 'stack',
  direction: 'horizontal',
  gap: '10px',
  align: 'center',
  children: [
    { id: 'icon1', type: 'leaf', size: { x: '32px', y: '32px' } },
    { id: 'icon2', type: 'leaf', size: { x: '32px', y: '32px' } },
    { id: 'icon3', type: 'leaf', size: { x: '32px', y: '32px' } },
  ],
}
```

## Performance Considerations

- Layout calculations are cached automatically
- Use `setActivated()` instead of `setVisibility()` when nodes shouldn't affect layout
- Deactivated nodes are excluded from calculations entirely
- Cache is reused for identical size/offset/activation combinations

## Troubleshooting

### Nodes not appearing in expected positions
- Ensure `update()` is called before `get()`
- Check that nodes are activated: `node.activated === true`
- Verify parent size is sufficient for child constraints

### Layout not updating after changes
- Call `setActivated()` to mark layout dirty
- Manually call `clearCache()` if needed

### Unexpected sizing behavior
- Remember padding reduces content area by `padding * 2`
- Auto-sizing only applies in stack direction
- Min/max constraints override calculated sizes

## References

- Public API: [references/api.md](references/api.md)
- Type Definitions: [references/types.md](references/types.md)
- Examples: [references/examples.md](references/examples.md)
