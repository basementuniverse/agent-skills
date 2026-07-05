# Scene Graph Usage Examples

## Basic Setup

### Creating a Simple Scene Graph

```typescript
import { SceneNode } from '@basementuniverse/scene-graph';
import { vec2 } from '@basementuniverse/vec';

// Extend SceneNode to create a basic drawable node
class SimpleNode extends SceneNode {
  constructor(name: string, public color: string = 'white') {
    super(name);
  }

  override draw(ctx: CanvasRenderingContext2D): void {
    if (!this.visible) return;

    ctx.save();
    ctx.setTransform(
      this.worldTransform.scale.x, 0,
      0, this.worldTransform.scale.y,
      this.worldTransform.position.x,
      this.worldTransform.position.y
    );
    ctx.rotate(this.worldTransform.rotation);

    // Draw a simple rectangle
    ctx.fillStyle = this.color;
    ctx.fillRect(-25, -25, 50, 50);

    ctx.restore();

    // Draw children
    for (const child of this.children) {
      child.draw(ctx);
    }
  }
}

// Create scene graph
const root = new SimpleNode('root');
const player = new SimpleNode('player', 'blue');
const weapon = new SimpleNode('weapon', 'red');

// Build hierarchy
player.addChild(weapon);
root.addChild(player);

// Position nodes
player.localTransform.position = vec2(400, 300);
weapon.localTransform.position = vec2(30, 0);  // 30 pixels to the right of player

// Game loop
function gameLoop(dt: number) {
  // Update
  root.update(dt);

  // Prepare for drawing
  root.updateWorldTransform();

  // Draw
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  root.draw(ctx);
}
```

## Sprite Rendering

### Creating a Sprite Node

```typescript
import { SceneNode } from '@basementuniverse/scene-graph';

interface Sprite {
  image: HTMLImageElement;
  width: number;
  height: number;
  draw(ctx: CanvasRenderingContext2D): void;
}

class SpriteNode extends SceneNode {
  constructor(name: string, public sprite: Sprite) {
    super(name);
  }

  override draw(ctx: CanvasRenderingContext2D): void {
    if (!this.visible) return;

    ctx.save();

    // Apply world transform
    const t = this.worldTransform;
    ctx.setTransform(
      t.scale.x * Math.cos(t.rotation),
      t.scale.x * Math.sin(t.rotation),
      -t.scale.y * Math.sin(t.rotation),
      t.scale.y * Math.cos(t.rotation),
      t.position.x,
      t.position.y
    );

    // Draw sprite centered
    this.sprite.draw(ctx);

    ctx.restore();

    // Draw children
    const sortedChildren = [...this.children].sort((a, b) => a.zIndex - b.zIndex);
    for (const child of sortedChildren) {
      child.draw(ctx);
    }
  }
}
```

## Transform Manipulation

### Parent-Child Transform Inheritance

```typescript
// Create player and weapon
const player = new SpriteNode('player', playerSprite);
const weapon = new SpriteNode('weapon', weaponSprite);

// Attach weapon to player
player.addChild(weapon);

// Position weapon relative to player
weapon.localTransform.position = vec2(20, -5);  // 20px right, 5px up from player

// Rotate player - weapon rotates with it
player.localTransform.rotation = Math.PI / 4;  // 45 degrees

// Scale player - weapon scales with it
player.localTransform.scale = vec2(2, 2);  // 2x size

// Before drawing, update transforms
root.updateWorldTransform();

// Now weapon.worldTransform contains absolute position/rotation/scale
console.log(weapon.worldTransform.position);  // Affected by player's transform
```

### Animating Transforms

```typescript
class RotatingNode extends SceneNode {
  constructor(
    name: string,
    private sprite: Sprite,
    private rotationSpeed: number = 1
  ) {
    super(name);
  }

  override update(dt: number): void {
    // Rotate continuously
    this.localTransform.rotation += this.rotationSpeed * dt;

    // Update children
    super.update(dt);
  }

  override draw(ctx: CanvasRenderingContext2D): void {
    if (!this.visible) return;

    ctx.save();
    ctx.setTransform(this.worldTransform.getMatrix());
    this.sprite.draw(ctx);
    ctx.restore();

    for (const child of this.children) {
      child.draw(ctx);
    }
  }
}

// Create rotating planet with orbiting moon
const planet = new RotatingNode('planet', planetSprite, 0.5);
const moon = new RotatingNode('moon', moonSprite, 2.0);

moon.localTransform.position = vec2(100, 0);  // 100px from planet
planet.addChild(moon);

// Moon will orbit planet AND rotate on its own axis
```

## Visibility and Z-Index

### Controlling Draw Order

```typescript
// Create layers
const background = new SpriteNode('background', bgSprite);
const midground = new SpriteNode('midground', mgSprite);
const foreground = new SpriteNode('foreground', fgSprite);

// Set z-index (lower draws first)
background.zIndex = 0;
midground.zIndex = 10;
foreground.zIndex = 20;

root.addChild(background);
root.addChild(foreground);  // Added second, but...
root.addChild(midground);   // ...will draw between bg and fg due to zIndex
```

### Toggling Visibility

```typescript
// Hide weapon temporarily
weapon.visible = false;

// Hide player and all children (including weapon)
player.visible = false;

// Conditional visibility
class HealthBarNode extends SceneNode {
  override update(dt: number): void {
    // Only show if player is damaged
    this.visible = player.health < player.maxHealth;
    super.update(dt);
  }
}
```

## Finding Nodes

### Search by Name

```typescript
// Deep hierarchy
const root = new SimpleNode('root');
const level1 = new SimpleNode('level1');
const level2 = new SimpleNode('level2');
const target = new SimpleNode('target');

root.addChild(level1);
level1.addChild(level2);
level2.addChild(target);

// Find deeply nested node
const found = root.findChildByName('target');
if (found) {
  found.localTransform.position = vec2(100, 100);
}

// Handle not found
const missing = root.findChildByName('nonexistent');
if (!missing) {
  console.log('Node not found');
}
```

### Caching Node References

```typescript
class GameScene {
  private player: SpriteNode;
  private enemies: SpriteNode[] = [];
  private root: SceneNode;

  constructor() {
    this.root = new SimpleNode('root');
    this.player = new SpriteNode('player', playerSprite);

    // Cache reference instead of searching each frame
    this.root.addChild(this.player);

    // Create enemies and cache references
    for (let i = 0; i < 10; i++) {
      const enemy = new SpriteNode(`enemy_${i}`, enemySprite);
      this.enemies.push(enemy);
      this.root.addChild(enemy);
    }
  }

  update(dt: number): void {
    // Use cached references
    this.player.localTransform.rotation += 0.1 * dt;

    for (const enemy of this.enemies) {
      // Direct access without searching
      enemy.localTransform.position = vec2(
        Math.random() * 800,
        Math.random() * 600
      );
    }

    this.root.update(dt);
  }
}
```

## Advanced Patterns

### Camera System

```typescript
class CameraNode extends SceneNode {
  override update(dt: number): void {
    // Follow player
    const player = this.findChildByName('player');
    if (player) {
      // Smooth camera follow
      const target = player.worldTransform.position;
      const current = this.localTransform.position;

      this.localTransform.position = vec2(
        current.x + (target.x - current.x) * 0.1,
        current.y + (target.y - current.y) * 0.1
      );
    }

    super.update(dt);
  }

  override draw(ctx: CanvasRenderingContext2D): void {
    if (!this.visible) return;

    ctx.save();

    // Apply inverse camera transform (scroll world)
    ctx.translate(-this.localTransform.position.x, -this.localTransform.position.y);
    ctx.rotate(-this.localTransform.rotation);
    ctx.scale(1 / this.localTransform.scale.x, 1 / this.localTransform.scale.y);

    // Draw children
    for (const child of this.children) {
      child.draw(ctx);
    }

    ctx.restore();
  }
}
```

### UI Container with Relative Positioning

```typescript
class UIContainer extends SceneNode {
  constructor(name: string, private padding: number = 10) {
    super(name);
  }

  override update(dt: number): void {
    // Auto-layout children vertically
    let currentY = this.padding;

    for (const child of this.children) {
      child.localTransform.position = vec2(this.padding, currentY);
      currentY += 50 + this.padding;  // Assuming 50px height per child
    }

    super.update(dt);
  }
}

// Create UI panel with buttons
const panel = new UIContainer('panel', 20);
panel.localTransform.position = vec2(10, 10);

const button1 = new ButtonNode('button1');
const button2 = new ButtonNode('button2');
const button3 = new ButtonNode('button3');

panel.addChild(button1);
panel.addChild(button2);
panel.addChild(button3);

// Buttons automatically positioned in vertical stack
```

### Particle System Node

```typescript
class ParticleSystemNode extends SceneNode {
  private particles: ParticleNode[] = [];

  spawnParticle(): void {
    const particle = new ParticleNode(`particle_${Date.now()}`);
    particle.localTransform.position = vec2(
      Math.random() * 20 - 10,
      Math.random() * 20 - 10
    );
    this.particles.push(particle);
    this.addChild(particle);
  }

  override update(dt: number): void {
    // Remove dead particles
    for (let i = this.particles.length - 1; i >= 0; i--) {
      if (this.particles[i].isDead) {
        const particle = this.particles[i];
        this.removeChild(particle);
        this.particles.splice(i, 1);
      }
    }

    super.update(dt);
  }
}
```

## Complete Game Example

```typescript
import { SceneNode } from '@basementuniverse/scene-graph';
import { vec2 } from '@basementuniverse/vec';

class Game {
  private root: SceneNode;
  private canvas: HTMLCanvasElement;
  private ctx: CanvasRenderingContext2D;

  constructor(canvas: HTMLCanvasElement) {
    this.canvas = canvas;
    this.ctx = canvas.getContext('2d')!;
    this.root = new SimpleNode('root');

    this.setupScene();
    this.start();
  }

  private setupScene(): void {
    // Create game objects
    const player = new SpriteNode('player', playerSprite);
    const weapon = new SpriteNode('weapon', weaponSprite);
    const healthBar = new HealthBarNode('healthBar');

    // Build hierarchy
    player.addChild(weapon);
    player.addChild(healthBar);

    // Position nodes
    player.localTransform.position = vec2(400, 300);
    weapon.localTransform.position = vec2(30, 0);
    healthBar.localTransform.position = vec2(0, -40);
    healthBar.zIndex = 100;  // Draw on top

    this.root.addChild(player);
  }

  private start(): void {
    let lastTime = performance.now();

    const loop = (currentTime: number) => {
      const dt = (currentTime - lastTime) / 1000;
      lastTime = currentTime;

      // Update
      this.root.update(dt);

      // Render
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.root.updateWorldTransform();
      this.root.draw(this.ctx);

      requestAnimationFrame(loop);
    };

    requestAnimationFrame(loop);
  }
}

// Start game
const canvas = document.getElementById('game') as HTMLCanvasElement;
const game = new Game(canvas);
```
