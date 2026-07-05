# Type Reference

TypeScript type definitions for `@basementuniverse/scene-manager`.

## SceneOptions

Configuration options for scene creation.

```typescript
type SceneOptions = {
  transitionTime: number;
  transparent: boolean;
  onTransitionedIn?: () => void;
  onTransitionedOut?: () => void;
};
```

### Properties

#### transitionTime

```typescript
transitionTime: number
```

The duration of transition in/out animations, in seconds.

**Default**: `2`

**Example**:
```typescript
new MyScene({ transitionTime: 1.5 }) // 1.5 second transitions
new MyScene({ transitionTime: 0 })   // Instant transitions
```

---

#### transparent

```typescript
transparent: boolean
```

Whether this scene allows scenes below it in the stack to be visible.

**Default**: `true`

**Usage**:
- `true`: Scenes below will be drawn (useful for overlays, pause menus)
- `false`: Scenes below will be culled/hidden (useful for full-screen scenes)

**Example**:
```typescript
// Pause menu - show game below
new PauseScene({ transparent: true })

// Loading screen - hide everything below
new LoadingScene({ transparent: false })
```

**Note**: Scenes that are transitioning are always treated as transparent, regardless of this setting.

---

#### onTransitionedIn

```typescript
onTransitionedIn?: () => void
```

Optional callback function invoked when the scene finishes transitioning in (when `transitionAmount` reaches 1).

**Example**:
```typescript
new MyScene({
  onTransitionedIn: () => {
    console.log('Scene is now fully visible and active');
    this.startGameplay();
  }
})
```

---

#### onTransitionedOut

```typescript
onTransitionedOut?: () => void
```

Optional callback function invoked when the scene finishes transitioning out (when `transitionAmount` reaches 0) and just before the scene is disposed.

**Example**:
```typescript
new MyScene({
  onTransitionedOut: () => {
    console.log('Scene has been removed');
    this.cleanupResources();
  }
})
```

---

## SceneTransitionState

Enum representing the current transition state of a scene.

```typescript
enum SceneTransitionState {
  In = 'in',
  Out = 'out',
  None = 'none'
}
```

### Values

#### SceneTransitionState.In

```typescript
In = 'in'
```

The scene is transitioning in. `transitionAmount` is increasing from 0 to 1.

**When this occurs**:
- Immediately after `SceneManager.push()` is called
- Continues until `transitionAmount` reaches 1
- Then transitions to `SceneTransitionState.None`

---

#### SceneTransitionState.Out

```typescript
Out = 'out'
```

The scene is transitioning out. `transitionAmount` is decreasing from 1 to 0.

**When this occurs**:
- When `SceneManager.pop()` is called
- When `SceneManager.clear()` is called
- Continues until `transitionAmount` reaches 0
- Scene is then automatically disposed and removed

**Note**: Scenes in this state do not receive `update()` calls.

---

#### SceneTransitionState.None

```typescript
None = 'none'
```

The scene is not transitioning. It is fully visible and active.

**When this occurs**:
- After transitioning in completes
- Before transitioning out begins
- `transitionAmount` remains at 1

---

## Usage Examples

### Complete Scene with All Options

```typescript
import { Scene, SceneOptions, SceneTransitionState } from '@basementuniverse/scene-manager';

class CompleteScene extends Scene {
  constructor() {
    const options: Partial<SceneOptions> = {
      transitionTime: 1.5,
      transparent: false,
      onTransitionedIn: () => {
        console.log('Scene is ready');
      },
      onTransitionedOut: () => {
        console.log('Scene removed');
      }
    };

    super(options);
  }

  initialise(level: number) {
    console.log(`Initializing level ${level}`);
  }

  update(dt: number) {
    // Check transition state
    if (this.transitionState === SceneTransitionState.In) {
      console.log('Still fading in...');
    }

    // Use transition amount for effects
    console.log(`Visibility: ${this.transitionAmount * 100}%`);
  }

  draw(context: CanvasRenderingContext2D) {
    // Apply fade based on transition
    context.globalAlpha = this.transitionAmount;
    // ... render scene
    context.globalAlpha = 1.0;
  }
}
```

---

### Checking Transition State

```typescript
update(dt: number) {
  switch (this.transitionState) {
    case SceneTransitionState.In:
      // Scene is fading in
      this.disableControls();
      break;

    case SceneTransitionState.None:
      // Scene is fully active
      this.enableControls();
      break;

    case SceneTransitionState.Out:
      // Scene is fading out
      // (Note: update() won't be called in this state)
      break;
  }
}
```

---

### Using Transition Amount

```typescript
draw(context: CanvasRenderingContext2D) {
  // Fade effect
  context.globalAlpha = this.transitionAmount;
  this.renderBackground(context);
  context.globalAlpha = 1.0;

  // Scale effect
  const scale = this.transitionAmount;
  context.save();
  context.scale(scale, scale);
  this.renderUI(context);
  context.restore();

  // Position animation
  const offset = (1 - this.transitionAmount) * 100;
  this.renderMenu(context, offset);
}
```

---

### Instant Transitions

```typescript
// No transition animation
class InstantScene extends Scene {
  constructor() {
    super({ transitionTime: 0 });
  }
}
```

---

### Slow Transitions

```typescript
// Long fade for dramatic effect
class DramaticScene extends Scene {
  constructor() {
    super({ transitionTime: 5.0 });
  }
}
```

---

## Type Imports

Import types and classes from the package:

```typescript
// Import everything
import SceneManager, {
  Scene,
  SceneOptions,
  SceneTransitionState
} from '@basementuniverse/scene-manager';

// Use in type annotations
function createScene(options: Partial<SceneOptions>): Scene {
  // ...
}

function isTransitioning(state: SceneTransitionState): boolean {
  return state !== SceneTransitionState.None;
}
```
