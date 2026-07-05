---
name: basementuniverse-scene-manager
description: >
  Use this skill when implementing or working with stack-based scene management in TypeScript/JavaScript games. Covers scene transitions, scene lifecycle, rendering order, and scene stack operations using @basementuniverse/scene-manager.
---

# Basement Universe Scene Manager

Use this skill when working with `@basementuniverse/scene-manager` for game development.

## When to Use This Skill

- Implementing scene management in a TypeScript/JavaScript game
- Creating game scenes with transitions (fade in/out effects)
- Managing a stack of scenes (menus, gameplay, overlays)
- Handling scene lifecycle (initialize, update, draw, dispose)
- Working with transparent/opaque scene rendering
- Debugging scene transition issues
- Setting up canvas-based game scene architecture

## Quick Start

```typescript
import SceneManager, { Scene } from '@basementuniverse/scene-manager';

// Must be called once before use
SceneManager.initialise();

// Create a scene class
class MyScene extends Scene {
  constructor() {
    super({
      transitionTime: 2.5,  // 2.5 seconds to fade in/out
      transparent: false     // Scenes below are hidden
    });
  }

  initialise(...args: any[]) {
    // Setup code - called when scene is pushed
  }

  update(dt: number, ...args: any[]) {
    // Game logic - only runs for top-most scene
    // this.transitionAmount is 0-1 (0=out, 1=in)

    // Push new scene
    SceneManager.push(new OtherScene());

    // Pop current scene
    SceneManager.pop();
  }

  draw(context: CanvasRenderingContext2D, ...args: any[]) {
    // Rendering - runs for all visible scenes
  }
}

// In your game loop
SceneManager.push(new MyScene());
SceneManager.update(deltaTime);
SceneManager.draw(canvasContext);
```

## Key Concepts

### Scene Stack

Scenes are managed in a stack (last-in, first-out):
- **Push**: Add a new scene on top (e.g., opening a menu)
- **Pop**: Remove the top scene (e.g., closing a menu)
- **Clear**: Remove all scenes (e.g., returning to main menu)

### Update Behavior

Only the **top-most non-transitioning-out scene** receives `update()` calls. This ensures that only the active scene processes game logic.

### Draw Behavior

Scenes are drawn **bottom-to-top**:
- All visible scenes are rendered
- If a scene is **opaque** and **not transitioning**, scenes below it are culled (not drawn)
- **Transparent** scenes allow scenes below to be visible
- **Transitioning** scenes are treated as transparent (for fade effects)

### Transition States

Scenes can be in three transition states:
- `In`: Fading in (`transitionAmount` increases from 0 to 1)
- `Out`: Fading out (`transitionAmount` decreases from 1 to 0)
- `None`: Fully visible/active (not transitioning)

The `transitionAmount` property (0-1) can be used for fade effects or animation.

### Scene Lifecycle

1. **Created**: Scene instance created with `new MyScene()`
2. **Pushed**: `SceneManager.push(scene)` adds to stack
3. **Initialized**: `scene.initialise()` called automatically
4. **Transitioning In**: `transitionAmount` increases 0→1
5. **Active**: `update()` and `draw()` called each frame
6. **Popped**: `SceneManager.pop()` starts transition out
7. **Transitioning Out**: `transitionAmount` decreases 1→0
8. **Disposed**: Automatically removed from stack when transition completes

## Common Patterns

### Menu System

```typescript
class MainMenuScene extends Scene {
  constructor() {
    super({ transparent: false }); // Hide scenes below
  }

  update(dt: number) {
    if (startButtonPressed) {
      SceneManager.push(new GameScene());
    }
    if (settingsButtonPressed) {
      SceneManager.push(new SettingsScene()); // Stack on top
    }
  }
}
```

### Pause Menu

```typescript
class PauseScene extends Scene {
  constructor() {
    super({ transparent: true }); // Show game below
  }

  update(dt: number) {
    if (resumeButtonPressed) {
      SceneManager.pop(); // Return to game
    }
  }
}

// In game scene
if (pauseKeyPressed) {
  SceneManager.push(new PauseScene()); // Game still visible
}
```

### Fade Transitions

```typescript
class FadeScene extends Scene {
  draw(context: CanvasRenderingContext2D) {
    // Use transitionAmount for fade effect
    context.globalAlpha = this.transitionAmount;
    // ... render scene content
    context.globalAlpha = 1.0;
  }
}
```

### Scene with Callbacks

```typescript
class LoadingScene extends Scene {
  constructor() {
    super({
      transitionTime: 1.0,
      onTransitionedIn: () => {
        console.log('Loading scene fully visible');
      },
      onTransitionedOut: () => {
        console.log('Loading scene removed');
      }
    });
  }
}
```

## Common Issues

### Scene Not Updating

- **Cause**: A scene higher in the stack is not transitioning out
- **Solution**: Ensure scenes properly call `SceneManager.pop()` when done

### Scene Not Visible

- **Cause**: An opaque scene above is hiding it
- **Solution**: Make the scene above transparent or ensure it's transitioning

### Multiple Updates

- **Cause**: Scenes not being properly removed
- **Solution**: Always use `SceneManager.pop()` rather than manually managing the stack

### Transition Not Working

- **Cause**: `transitionTime` set to 0 or very small value
- **Solution**: Set appropriate `transitionTime` in scene options (default is 2 seconds)

## Integration Notes

- Works with any HTML5 Canvas-based game
- Compatible with other Basement Universe components
- No dependencies on specific game frameworks
- Supports optional `resize()` method for responsive games

## References

- Public API surface: [references/api.md](references/api.md)
- Type reference: [references/types.md](references/types.md)
