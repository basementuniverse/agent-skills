---
name: basementuniverse-scene-graph
description: >
  Use this skill when implementing hierarchical scene graphs for 2D games, managing game object parent-child relationships with transform inheritance, or implementing actors/entities that need positional hierarchy (e.g., weapons attached to players, UI elements in containers).
---

# Basement Universe Scene Graph

Use this skill when working with `@basementuniverse/scene-graph`.

## When to Use This Skill

- Building 2D game scene hierarchies with parent-child relationships
- Implementing game objects where child transforms inherit from parents (e.g., a weapon following a player's rotation and position)
- Managing game object updates and rendering in a structured tree
- Organizing game entities with visibility control and z-index ordering
- Creating composite game objects from simpler components
- Implementing UI systems with nested elements

## Core Concepts

### Scene Graph Hierarchy

The scene graph is a tree structure where each node:
- Has one parent (or null if root)
- Can have multiple children
- Maintains both local and world transforms
- Propagates updates and transforms down to children

### Transform Inheritance

Transforms cascade through the hierarchy:
- **Local Transform**: Position, rotation, and scale relative to parent
- **World Transform**: Absolute position, rotation, and scale in world space
- Child world transforms automatically computed from parent world transform and child local transform
- Rotation applied before position offset; positions scaled by parent scale

### Node Lifecycle

1. **Creation**: Extend `SceneNode` to create custom node types
2. **Hierarchy**: Add nodes to parents with `addChild()`
3. **Update**: Call `root.update(dt)` to recursively update all nodes
4. **Transform**: Call `root.updateWorldTransform()` before drawing (required!)
5. **Draw**: Call `root.draw(ctx)` to recursively render all visible nodes

## Key Classes and Types

- `SceneNode` (abstract): Base class for all scene graph nodes - must be extended
- `Transform`: Object with `position` (vec2), `rotation` (radians), and `scale` (vec2)

## Common Patterns

### Basic Scene Graph Setup

```typescript
import { SceneNode } from '@basementuniverse/scene-graph';

// Create root node
const root = new CustomNode("root");

// Create child nodes
const player = new SpriteNode("player");
const weapon = new SpriteNode("weapon");

// Build hierarchy
player.addChild(weapon);  // weapon follows player
root.addChild(player);

// Game loop
function update(dt: number) {
  root.update(dt);  // Recursively updates all nodes
}

function draw(ctx: CanvasRenderingContext2D) {
  root.updateWorldTransform();  // MUST call before draw!
  root.draw(ctx);  // Recursively draws all visible nodes
}
```

### Creating Custom Nodes

Extend `SceneNode` to create specialized node types:

```typescript
class SpriteNode extends SceneNode {
  constructor(name: string, private sprite: Sprite) {
    super(name);
  }

  override draw(ctx: CanvasRenderingContext2D): void {
    if (!this.visible) return;

    ctx.save();
    // Use worldTransform for absolute positioning
    ctx.setTransform(this.worldTransform.getMatrix());
    this.sprite.draw(ctx);
    ctx.restore();

    // Draw children
    for (const child of this.children) {
      child.draw(ctx);
    }
  }

  override update(dt: number): void {
    // Custom update logic here
    this.localTransform.rotation += 0.01 * dt;

    // Call parent implementation to update children
    super.update(dt);
  }
}
```

### Transform Manipulation

```typescript
// Modify local transform (relative to parent)
node.localTransform.position = vec2(10, 20);
node.localTransform.rotation = Math.PI / 4;  // 45 degrees
node.localTransform.scale = vec2(2, 2);  // 2x scale

// Read world transform (absolute)
const worldPos = node.worldTransform.position;
const worldRot = node.worldTransform.rotation;
```

### Controlling Visibility and Draw Order

```typescript
// Hide a node and its children
node.visible = false;

// Control draw order (lower values draw first)
background.zIndex = 0;
player.zIndex = 10;
foreground.zIndex = 20;
```

### Finding Nodes

```typescript
// Recursive search by name
const weapon = root.findChildByName("weapon");
if (weapon) {
  weapon.localTransform.position = vec2(5, 0);
}
```

## Important Notes

- **Always call `updateWorldTransform()` before drawing** - world transforms must be computed before rendering
- **Extend `SceneNode`** - it's an abstract class, not meant to be instantiated directly
- **Use `worldTransform` in custom draw methods** - not `localTransform`
- **No circular reference protection** - avoid adding ancestor nodes as children
- **Children are sorted by zIndex during draw()** - no need to manually sort
- **Update propagates automatically** - calling `update()` on parent updates all children

## Common Pitfalls

1. **Forgetting to call `updateWorldTransform()`**: World transforms will be outdated, causing incorrect rendering
2. **Using `localTransform` in custom draw methods**: Should use `worldTransform` for absolute positioning
3. **Modifying world transform directly**: Always modify `localTransform`; world transform is computed
4. **Not calling `super.update(dt)` in overridden update methods**: Children won't update

## References

- Public API surface: [references/api.md](references/api.md)
- Type definitions: [references/types.md](references/types.md)
- Usage examples: [references/examples.md](references/examples.md)
