# Usage Patterns

## Basic Setup

```ts
import InputManager, { MouseButton } from '@basementuniverse/input-manager';

InputManager.initialise({
  element: canvas,
  preventContextMenu: true,
});

function gameLoop() {
  InputManager.update();

  if (InputManager.keyDown('KeyW')) {
    // move forward
  }

  if (InputManager.keyPressed('Space')) {
    // jump
  }

  if (InputManager.mousePressed(MouseButton.Left)) {
    // fire
  }
}
```

## Good Practices

- Call `initialise()` once only.
- Call `update()` before reading `keyPressed`, `keyReleased`, `mousePressed`, `mouseReleased`, `mouseWheelUp`, or `mouseWheelDown`.
- Use `KeyboardEvent.code` strings such as `KeyA`, `Space`, and `ArrowLeft`.
- Use `MouseButton.Left`, `MouseButton.Middle`, and `MouseButton.Right` instead of raw numbers when possible.

## Common Options

- `element`: choose the DOM element that should receive mouse events.
- `mouse`: disable when only keyboard input is needed.
- `mouseWheel`: disable if wheel input is irrelevant.
- `keyboard`: disable if you only need pointer input.
- `preventContextMenu`: enable when right-click should not open the browser menu.
