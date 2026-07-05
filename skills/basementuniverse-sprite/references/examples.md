# Examples

Common usage patterns and examples for `@basementuniverse/sprite`.

## Basic Sprite

Simplest possible sprite with a single static image:

```typescript
import { Sprite } from '@basementuniverse/sprite';

const sprite = new Sprite({
  image: myImage,
  position: vec(100, 100),
});

// Game loop
function update(dt: number) {
  sprite.update(dt);
}

function render(context: CanvasRenderingContext2D) {
  sprite.draw(context);
}
```

## Animated Sprite

Simple looping animation:

```typescript
const sprite = new Sprite({
  animations: {
    default: {
      '*': {
        name: 'default',
        frameCount: 4,
        frameRate: 8,  // 8 FPS
        mode: 'repeat',
        images: [frame1, frame2, frame3, frame4],
      }
    }
  },
  defaultAnimation: 'default',
});
```

## Multi-Directional Character

Character with different animations for each direction:

```typescript
const character = new Sprite({
  directions: ['up', 'down', 'left', 'right'],
  defaultDirection: 'down',
  animations: {
    idle: {
      up: { name: 'idle', frameCount: 1, images: [idleUp] },
      down: { name: 'idle', frameCount: 1, images: [idleDown] },
      left: { name: 'idle', frameCount: 1, images: [idleLeft] },
      right: { name: 'idle', frameCount: 1, images: [idleRight] },
    },
    walk: {
      up: {
        name: 'walk',
        frameCount: 4,
        frameRate: 8,
        mode: 'repeat',
        images: [walkUp1, walkUp2, walkUp3, walkUp4],
      },
      down: {
        name: 'walk',
        frameCount: 4,
        frameRate: 8,
        mode: 'repeat',
        images: [walkDown1, walkDown2, walkDown3, walkDown4],
      },
      left: {
        name: 'walk',
        frameCount: 4,
        frameRate: 8,
        mode: 'repeat',
        images: [walkLeft1, walkLeft2, walkLeft3, walkLeft4],
      },
      right: {
        name: 'walk',
        frameCount: 4,
        frameRate: 8,
        mode: 'repeat',
        images: [walkRight1, walkRight2, walkRight3, walkRight4],
      },
    }
  },
  defaultAnimation: 'idle',
});

// Control character based on input
function handleInput(velocity: vec2) {
  if (vec2.len(velocity) > 0) {
    character.animation = 'walk';

    // Determine direction from velocity
    if (Math.abs(velocity.x) > Math.abs(velocity.y)) {
      character.direction = velocity.x > 0 ? 'right' : 'left';
    } else {
      character.direction = velocity.y > 0 ? 'down' : 'up';
    }
  } else {
    character.animation = 'idle';
  }
}
```

## Using Wildcard Direction

Animation available for all directions:

```typescript
const sprite = new Sprite({
  directions: ['north', 'south', 'east', 'west'],
  animations: {
    spin: {
      '*': {  // Available for all directions
        name: 'spin',
        frameCount: 8,
        frameRate: 12,
        mode: 'repeat',
        images: spinFrames,
      }
    },
    walk: {
      north: { name: 'walk', frameCount: 4, images: walkNorthFrames },
      // ... other specific directions
    }
  },
  defaultAnimation: 'spin',
});

// Can set any direction, 'spin' animation works for all
sprite.direction = 'north';  // Works
sprite.direction = 'west';   // Works
```

## One-Shot Animations

Animation that plays once and stops:

```typescript
const explosion = new Sprite({
  animations: {
    explode: {
      '*': {
        name: 'explode',
        frameCount: 6,
        frameRate: 12,
        mode: 'play-once-and-stop',
        images: explosionFrames,
      }
    }
  },
  defaultAnimation: 'explode',
});

// Check if animation finished
function update(dt: number) {
  explosion.update(dt);

  // If on last frame and not playing, animation is done
  if (explosion.currentFrame === 5 && !explosion.playing) {
    removeExplosion(explosion);
  }
}
```

## Sprite with Attachment Points

Character with weapon attachment:

```typescript
const body = new Sprite({
  attachmentPoints: [
    { name: 'rightHand', offset: vec(15, 5) },
    { name: 'leftHand', offset: vec(-15, 5) },
  ],
  animations: {
    attack: {
      '*': {
        name: 'attack',
        frameCount: 3,
        frameRate: 10,
        mode: 'play-once-and-reset',
        images: attackFrames,
        attachmentPointKeyframes: {
          rightHand: [
            { frame: 0, offset: vec(15, 5) },    // Wind up
            { frame: 1, offset: vec(25, -5) },   // Swing
            { frame: 2, offset: vec(15, 5) },    // Follow through
          ]
        }
      }
    }
  },
  defaultAnimation: 'idle',
});

const weapon = new Sprite({
  image: swordImage,
});

// Update weapon position every frame
function update(dt: number) {
  body.update(dt);

  const handPos = body.getAttachmentPoint('rightHand');
  if (handPos) {
    weapon.position = handPos;
    weapon.rotation = body.rotation;  // Match body rotation
  }

  weapon.update(dt);
}

function render(context: CanvasRenderingContext2D) {
  body.draw(context);
  weapon.draw(context);
}
```

## Composite Sprite

Building a character from multiple sprite layers:

```typescript
class CompositeCharacter {
  private body: Sprite;
  private head: Sprite;
  private weapon: Sprite;

  constructor() {
    this.body = new Sprite({
      attachmentPoints: [
        { name: 'neck', offset: vec(0, -20) },
        { name: 'rightHand', offset: vec(10, 5) },
      ],
      // ... body animations
    });

    this.head = new Sprite({
      // ... head animations
    });

    this.weapon = new Sprite({
      // ... weapon sprite
    });
  }

  update(dt: number) {
    this.body.update(dt);
    this.head.update(dt);
    this.weapon.update(dt);

    // Update attachment positions
    const neckPos = this.body.getAttachmentPoint('neck');
    if (neckPos) {
      this.head.position = neckPos;
      this.head.rotation = this.body.rotation;
    }

    const handPos = this.body.getAttachmentPoint('rightHand');
    if (handPos) {
      this.weapon.position = handPos;
      this.weapon.rotation = this.body.rotation;
    }
  }

  draw(context: CanvasRenderingContext2D) {
    this.body.draw(context);
    this.head.draw(context);
    this.weapon.draw(context);
  }

  // Delegate common properties
  get position() { return this.body.position; }
  set position(value: vec2) { this.body.position = value; }

  get animation() { return this.body.animation; }
  set animation(value: string) { this.body.animation = value; }

  get direction() { return this.body.direction; }
  set direction(value: string) { this.body.direction = value; }
}
```

## Custom Rendering

Using pre/post render hooks for effects:

```typescript
const sprite = new Sprite({
  // ... sprite config
  preRender: (context, sprite) => {
    // Apply custom effects before drawing sprite
    context.globalAlpha = 0.5;  // Semi-transparent
    context.shadowBlur = 10;
    context.shadowColor = 'blue';
  },
  postRender: (context, sprite) => {
    // Reset effects after drawing sprite
    context.globalAlpha = 1.0;
    context.shadowBlur = 0;

    // Draw additional decorations
    context.strokeStyle = 'red';
    context.strokeRect(
      -sprite.origin.x,
      -sprite.origin.y,
      sprite.size.x,
      sprite.size.y
    );
  },
});
```

## Rotation and Scaling

Transforming sprites:

```typescript
const sprite = new Sprite({
  position: vec(200, 200),
  scale: 2.0,  // Double size
  rotation: Math.PI / 4,  // 45 degrees
  // ... animations
});

// Animate rotation
function update(dt: number) {
  sprite.rotation += dt * 2;  // Rotate at 2 radians per second
  sprite.update(dt);
}

// Pulsing scale effect
function update(dt: number) {
  const time = Date.now() / 1000;
  sprite.scale = 1.0 + Math.sin(time * 4) * 0.2;  // 0.8 to 1.2
  sprite.update(dt);
}
```

## Custom Origin Point

Setting origin for rotation/scale:

```typescript
// Rotate around bottom-center (useful for upright characters)
const character = new Sprite({
  size: vec(32, 64),
  origin: vec(16, 64),  // Bottom-center
  // ... animations
});

// Rotate around top-left corner
const sprite = new Sprite({
  size: vec(32, 32),
  origin: vec(0, 0),  // Top-left
  // ... animations
});
```

## Debug Visualization

Enabling debug overlays:

```typescript
// Enable all debug options
const sprite = new Sprite({
  // ... sprite config
  debug: true,
});

// Enable specific debug options
const sprite = new Sprite({
  // ... sprite config
  debug: {
    showSpriteTransforms: true,    // Show X/Y axes
    showSpriteBoundingBox: true,   // Show bounding box
    showAttachmentPoints: false,   // Hide attachment points
  },
});
```

## Animation State Machine

Managing complex animation states:

```typescript
enum CharacterState {
  Idle,
  Walking,
  Running,
  Jumping,
  Attacking,
}

class Character {
  private sprite: Sprite;
  private state: CharacterState = CharacterState.Idle;

  constructor() {
    this.sprite = new Sprite({
      animations: {
        idle: { /* ... */ },
        walk: { /* ... */ },
        run: { /* ... */ },
        jump: { /* ... */ },
        attack: { /* ... */ },
      },
      defaultAnimation: 'idle',
    });
  }

  setState(newState: CharacterState) {
    if (this.state === newState) return;

    this.state = newState;

    switch (newState) {
      case CharacterState.Idle:
        this.sprite.animation = 'idle';
        break;
      case CharacterState.Walking:
        this.sprite.animation = 'walk';
        break;
      case CharacterState.Running:
        this.sprite.animation = 'run';
        break;
      case CharacterState.Jumping:
        this.sprite.animation = 'jump';
        break;
      case CharacterState.Attacking:
        this.sprite.animation = 'attack';
        this.sprite.resetAnimation();
        this.sprite.playAnimation();
        break;
    }
  }

  update(dt: number) {
    this.sprite.update(dt);

    // Return to idle after one-shot animations
    if (this.state === CharacterState.Attacking) {
      // Check if attack animation finished
      // (implementation depends on your animation setup)
    }
  }
}
```

## Performance: Shared Images

Reusing image assets across multiple sprites:

```typescript
// Load images once
const walkFrames = [
  loadImage('walk-1.png'),
  loadImage('walk-2.png'),
  loadImage('walk-3.png'),
  loadImage('walk-4.png'),
];

// Create multiple sprites sharing the same images
const enemy1 = new Sprite({
  position: vec(100, 100),
  animations: {
    walk: { '*': { name: 'walk', images: walkFrames } }
  },
});

const enemy2 = new Sprite({
  position: vec(200, 100),
  animations: {
    walk: { '*': { name: 'walk', images: walkFrames } }
  },
});

// Both sprites share the same image objects in memory
```
