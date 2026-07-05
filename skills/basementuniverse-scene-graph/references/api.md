# Scene Graph API Reference

## Package Information

- **Package**: `@basementuniverse/scene-graph`
- **Version**: 0.0.1
- **Dependencies**: `@basementuniverse/vec` (provides `vec2` type and operations)

## SceneNode Class

Abstract base class for all scene graph nodes. Must be extended to create custom node types.

### Constructor

```typescript
constructor(name: string)
```

Creates a new scene node with the specified name identifier.

**Parameters:**
- `name: string` - Unique identifier for the node, used for searching

### Properties

#### `parent: SceneNode | null`

Reference to the parent node. `null` if this is a root node.

- Read-only in practice (managed by `addChild`/`removeChild`)

#### `children: SceneNode[]`

Array of child nodes attached to this node.

- Direct manipulation not recommended; use `addChild()`/`removeChild()` instead

#### `localTransform: Transform`

Transform relative to parent node.

**Default**: `{ position: {x: 0, y: 0}, rotation: 0, scale: {x: 1, y: 1} }`

Modify this to change the node's position/rotation/scale relative to its parent.

#### `worldTransform: Transform`

Computed absolute transform in world space.

- Automatically calculated by `updateWorldTransform()`
- **Read-only**: Do not modify directly; modify `localTransform` instead
- Must be updated before drawing with `updateWorldTransform()`

#### `visible: boolean`

Visibility flag controlling whether this node and its children are drawn.

**Default**: `true`

When `false`, `draw()` returns early without rendering this node or any children.

#### `zIndex: number`

Drawing order within siblings. Lower values are drawn first (back to front).

**Default**: `0`

- Children are automatically sorted by zIndex during `draw()`
- Siblings with same zIndex maintain insertion order

#### `name: string`

Identifier string for this node, used by `findChildByName()`.

- Passed to constructor, publicly accessible

### Methods

#### `addChild(child: SceneNode): void`

Adds a child node to this node's children array.

**Parameters:**
- `child: SceneNode` - The node to add as a child

**Behavior:**
- Sets `child.parent` to this node
- Appends child to `this.children` array
- Does not sort by zIndex (sorting happens during draw)

**Example:**
```typescript
const parent = new CustomNode("parent");
const child = new CustomNode("child");
parent.addChild(child);
```

#### `removeChild(child: SceneNode): void`

Removes the specified child node from this node's children array.

**Parameters:**
- `child: SceneNode` - The child node to remove

**Behavior:**
- Clears `child.parent` (sets to null)
- Removes child from `this.children` array
- No-op if child is not found in children array

**Example:**
```typescript
parent.removeChild(child);
```

#### `removeFromParent(): void`

Removes this node from its parent's children array.

**Behavior:**
- Calls `parent.removeChild(this)` if parent exists
- No-op if `parent` is null

**Example:**
```typescript
child.removeFromParent();
```

#### `findChildByName(name: string): SceneNode | null`

Recursively searches children for a node with the specified name.

**Parameters:**
- `name: string` - The name to search for

**Returns:**
- `SceneNode` - First matching node found
- `null` - If no matching node found

**Behavior:**
- Depth-first traversal
- Returns first match found
- Searches entire subtree rooted at this node

**Example:**
```typescript
const weapon = root.findChildByName("weapon");
if (weapon) {
  weapon.visible = false;
}
```

#### `updateWorldTransform(): void`

Computes `worldTransform` from `localTransform` and parent's `worldTransform`.

**Behavior:**

**If parent exists:**
```
worldPosition = parentWorldPosition + rotate(localPosition, parentWorldRotation) * parentWorldScale
worldRotation = parentWorldRotation + localRotation
worldScale = parentWorldScale * localScale
```

**If no parent:**
```
worldTransform = copy of localTransform
```

**Important:**
- Recursively updates all children's world transforms
- **MUST be called before `draw()`** to ensure correct rendering
- Call once per frame on the root node before drawing

**Example:**
```typescript
function draw(ctx: CanvasRenderingContext2D) {
  root.updateWorldTransform();  // Required!
  root.draw(ctx);
}
```

#### `update(dt: number): void`

Game loop update method. Override to add custom update logic.

**Parameters:**
- `dt: number` - Delta time in seconds since last update

**Default behavior:**
- Recursively calls `update(dt)` on all children

**Override pattern:**
```typescript
override update(dt: number): void {
  // Custom update logic
  this.localTransform.rotation += 0.01 * dt;

  // Call parent to update children
  super.update(dt);
}
```

#### `draw(ctx: CanvasRenderingContext2D): void`

Renders node and children to canvas context. Override to add custom rendering.

**Parameters:**
- `ctx: CanvasRenderingContext2D` - Canvas 2D rendering context

**Default behavior:**
1. Returns early if `visible === false`
2. Saves canvas state
3. Applies `localTransform` (translate, rotate, scale)
4. Sorts children by `zIndex` (ascending)
5. Recursively draws sorted children
6. Restores canvas state

**Override pattern:**
```typescript
override draw(ctx: CanvasRenderingContext2D): void {
  if (!this.visible) return;

  ctx.save();
  // Use worldTransform for absolute positioning
  ctx.setTransform(this.worldTransform.getMatrix());

  // Custom drawing code
  this.sprite.draw(ctx);

  ctx.restore();

  // Draw children
  for (const child of this.children) {
    child.draw(ctx);
  }
}
```

**Important:**
- When overriding, use `worldTransform` not `localTransform` for absolute positioning
- Must call `updateWorldTransform()` before this method
- Remember to draw children if needed

## Helper Function

### `getDefaultTransform()`

Internal helper function that returns a default Transform object.

**Returns:**
```typescript
{
  position: vec2(0, 0),
  rotation: 0,
  scale: vec2(1, 1)
}
```

Not exported from the package, but shown here for reference.
