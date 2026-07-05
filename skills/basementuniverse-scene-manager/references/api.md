# Scene Manager API Reference

Complete API documentation for `@basementuniverse/scene-manager`.

## SceneManager (Static Class)

The `SceneManager` is a singleton static class that manages a stack of scenes.

### SceneManager.initialise()

```typescript
static initialise(): void
```

Initializes the scene manager. **Must be called once** before using any other SceneManager methods.

**Throws**: Error if called multiple times.

**Example**:
```typescript
SceneManager.initialise();
```

---

### SceneManager.push()

```typescript
static push(scene: Scene, ...args: any[]): Scene
```

Pushes a scene onto the scene stack, calls `scene.initialise(...args)`, and starts the transition in.

**Parameters**:
- `scene`: Scene instance to push onto the stack
- `...args`: Arguments passed to the scene's `initialise()` method

**Returns**: The pushed scene instance.

**Example**:
```typescript
const gameScene = SceneManager.push(new GameScene());
const menuScene = SceneManager.push(new MenuScene(), { level: 1 });
```

---

### SceneManager.pop()

```typescript
static pop(): Scene | undefined
```

Starts transitioning out the top-most non-transitioning scene. The scene is automatically removed from the stack when the transition completes.

**Returns**: The scene being transitioned out, or `undefined` if the stack is empty.

**Example**:
```typescript
const scene = SceneManager.pop();
if (scene) {
  console.log('Removing scene:', scene.constructor.name);
}
```

**Note**: If multiple scenes are already transitioning out, this will find the next scene down the stack that is not transitioning and start transitioning it out.

---

### SceneManager.clear()

```typescript
static clear(): void
```

Starts transitioning out all scenes that are not already transitioning out. Effectively clears the entire scene stack.

**Example**:
```typescript
// Return to main menu, clearing all scenes
SceneManager.clear();
SceneManager.push(new MainMenuScene());
```

---

### SceneManager.update()

```typescript
static update(dt: number, ...args: any[]): void
```

Updates the scene manager and the current scene.

**Behavior**:
1. Calls `update(dt, ...args)` on the top-most non-transitioning-out scene
2. Updates all scene transitions (`updateTransition(dt)` on all scenes)
3. Removes disposed scenes from the stack

**Parameters**:
- `dt`: Delta time in seconds since the last update
- `...args`: Additional arguments passed to the scene's `update()` method

**Example**:
```typescript
class Game {
  private lastTime: number = 0;

  public loop(currentTime: number) {
    const dt = (currentTime - this.lastTime) / 1000;
    this.lastTime = currentTime;

    SceneManager.update(dt);

    requestAnimationFrame(this.loop.bind(this));
  }
}
```

---

### SceneManager.draw()

```typescript
static draw(context: CanvasRenderingContext2D, ...args: any[]): void
```

Renders scenes on the canvas.

**Behavior**:
1. Determines which scenes need to be drawn (culls based on transparency)
2. Draws visible scenes in **bottom-to-top** order
3. Treats transitioning scenes as transparent

**Drawing Rules**:
- All scenes below an **opaque** and **non-transitioning** scene are culled
- **Transparent** scenes allow scenes below to be drawn
- **Transitioning** scenes are always treated as transparent

**Parameters**:
- `context`: Canvas rendering context
- `...args`: Additional arguments passed to each scene's `draw()` method

**Example**:
```typescript
class Game {
  private canvas: HTMLCanvasElement;
  private context: CanvasRenderingContext2D;

  public render() {
    this.context.clearRect(0, 0, this.canvas.width, this.canvas.height);
    SceneManager.draw(this.context);
  }
}
```

---

### SceneManager.resize()

```typescript
static resize(width: number, height: number): void
```

Notifies all scenes that a resize event has occurred. Calls the optional `resize()` method on each scene that implements it.

**Parameters**:
- `width`: New canvas/viewport width
- `height`: New canvas/viewport height

**Example**:
```typescript
window.addEventListener('resize', () => {
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
  SceneManager.resize(canvas.width, canvas.height);
});
```

---

## Scene (Abstract Class)

Base class for all scenes. Extend this class and implement the abstract methods.

### Constructor

```typescript
constructor(options?: Partial<SceneOptions>)
```

Creates a new scene with optional configuration.

**Parameters**:
- `options`: Optional scene configuration (see [SceneOptions](types.md#sceneoptions))

**Example**:
```typescript
class MyScene extends Scene {
  constructor() {
    super({
      transitionTime: 2.5,
      transparent: false,
      onTransitionedIn: () => console.log('Scene ready'),
      onTransitionedOut: () => console.log('Scene removed')
    });
  }
}
```

---

### Properties

#### transitionState

```typescript
transitionState: SceneTransitionState
```

Current transition state of the scene. See [SceneTransitionState](types.md#scenetransitionstate).

- `SceneTransitionState.In`: Transitioning in
- `SceneTransitionState.Out`: Transitioning out
- `SceneTransitionState.None`: Not transitioning

---

#### transitionAmount

```typescript
transitionAmount: number
```

Transition progress value between `0` (fully out) and `1` (fully in).

Use this for fade effects or other transition animations:

```typescript
draw(context: CanvasRenderingContext2D) {
  context.globalAlpha = this.transitionAmount;
  // ... render scene
  context.globalAlpha = 1.0;
}
```

---

#### transitionTime

```typescript
transitionTime: number
```

Duration of transitions in seconds. Set via constructor options (default: 2).

---

#### transparent

```typescript
transparent: boolean
```

Whether scenes below this scene should be visible. Set via constructor options (default: true).

---

#### disposed

```typescript
disposed: boolean
```

`true` if the scene has been disposed and will be removed from the stack. Automatically set when transition out completes.

---

### Methods

#### dispose()

```typescript
dispose(): void
```

Marks the scene as disposed. Called automatically when a scene finishes transitioning out. Can be called manually to force immediate disposal.

**Example**:
```typescript
if (errorOccurred) {
  this.dispose(); // Force immediate disposal
}
```

---

#### transitionIn()

```typescript
transitionIn(): void
```

Starts the transition in animation. Called automatically by `SceneManager.push()`.

---

#### transitionOut()

```typescript
transitionOut(): void
```

Starts the transition out animation. Called automatically by `SceneManager.pop()` or `SceneManager.clear()`.

---

#### updateTransition()

```typescript
updateTransition(dt: number): void
```

**Internal method** - Updates the transition state and `transitionAmount`. Called automatically by `SceneManager.update()`.

**Parameters**:
- `dt`: Delta time in seconds

**Behavior**:
- When transitioning in: increases `transitionAmount` from 0 to 1
- When transitioning out: decreases `transitionAmount` from 1 to 0
- Calls `onTransitionedIn` callback when transition in completes
- Calls `onTransitionedOut` callback and disposes when transition out completes

---

### Abstract Methods (Must Implement)

#### initialise()

```typescript
abstract initialise(...args: any[]): void
```

Called by `SceneManager.push()` when the scene is added to the stack. Use this for setup code.

**Parameters**:
- `...args`: Arguments passed from `SceneManager.push()`

**Example**:
```typescript
initialise(level: number, difficulty: string) {
  this.level = level;
  this.difficulty = difficulty;
  this.loadAssets();
}
```

---

#### update()

```typescript
abstract update(dt: number, ...args: any[]): void
```

Called every frame by `SceneManager.update()` when this scene is the top-most non-transitioning-out scene.

**Parameters**:
- `dt`: Delta time in seconds
- `...args`: Additional arguments from `SceneManager.update()`

**Example**:
```typescript
update(dt: number) {
  this.player.update(dt);

  if (InputManager.keyPressed('Escape')) {
    SceneManager.push(new PauseScene());
  }

  if (this.gameOver) {
    SceneManager.pop();
  }
}
```

---

#### draw()

```typescript
abstract draw(context: CanvasRenderingContext2D, ...args: any[]): void
```

Called every frame by `SceneManager.draw()` for all visible scenes.

**Parameters**:
- `context`: Canvas rendering context
- `...args`: Additional arguments from `SceneManager.draw()`

**Example**:
```typescript
draw(context: CanvasRenderingContext2D) {
  context.globalAlpha = this.transitionAmount;

  this.background.draw(context);
  this.player.draw(context);
  this.ui.draw(context);

  context.globalAlpha = 1.0;
}
```

---

### Optional Methods

#### resize()

```typescript
resize?(width: number, height: number): void
```

Optional method called by `SceneManager.resize()` when a resize event occurs.

**Parameters**:
- `width`: New canvas/viewport width
- `height`: New canvas/viewport height

**Example**:
```typescript
resize(width: number, height: number) {
  this.camera.setViewport(width, height);
  this.ui.reposition(width, height);
}
```
