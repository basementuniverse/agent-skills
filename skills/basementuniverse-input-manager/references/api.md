# Public API

## Overview

`InputManager` is a singleton class. Initialise it once, call `update()` every frame, and then poll state through static methods and getters.

## Static Methods

### `InputManager.initialise(options?: Partial<InputOptions>): void`
Initialises the singleton and registers event listeners.

### `InputManager.update(): void`
Copies the current input state to the previous state and clears transient wheel input. Call once per frame before polling edge-triggered methods.

### `InputManager.keyDown(code?: string): boolean`
Returns whether any key is down, or whether a specific `KeyboardEvent.code` is down.

### `InputManager.keyPressed(code?: string): boolean`
Returns whether any key was pressed this frame, or whether a specific key was pressed this frame.

### `InputManager.keyReleased(code?: string): boolean`
Returns whether any key was released this frame, or whether a specific key was released this frame.

### `InputManager.mouseDown(button?: MouseButton): boolean`
Returns whether any mouse button is down, or whether a specific `MouseButton` is down.

### `InputManager.mousePressed(button?: MouseButton): boolean`
Returns whether any mouse button was pressed this frame, or whether a specific `MouseButton` was pressed this frame.

### `InputManager.mouseReleased(button?: MouseButton): boolean`
Returns whether any mouse button was released this frame, or whether a specific `MouseButton` was released this frame.

### `InputManager.mouseWheelUp(): boolean`
Returns whether the wheel scrolled up during the current frame.

### `InputManager.mouseWheelDown(): boolean`
Returns whether the wheel scrolled down during the current frame.

## Getters

### `InputManager.mousePosition: vec2`
Returns the current mouse position in screen-space.

### `InputManager.hoveredElement: HTMLElement | null`
Returns the currently hovered HTML element, if any.

## Notes

- The package is published as `@basementuniverse/input-manager`.
- The default export is the `InputManager` class.
- Keyboard state uses `KeyboardEvent.code` values, not display labels.
- Mouse wheel state is transient and resets on every `update()` call.
