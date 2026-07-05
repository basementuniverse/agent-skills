---
name: basementuniverse-input-manager
description: Use when working with @basementuniverse/input-manager to initialise input handling, update frame state, query keyboard or mouse events, configure input options, or interpret MouseButton and KeyboardEvent.code values.
---

# Basement Universe Input Manager

Use this skill when working with `@basementuniverse/input-manager`.

This library exposes a singleton `InputManager` for browser-based games and applications. It tracks keyboard, mouse, wheel, and touch input, and provides polling methods for down, pressed, and released state.

## When To Use

Use this skill when you need to:

- initialise input handling once at startup
- call `update()` once per frame or game loop tick
- check whether keys or mouse buttons are down, pressed, or released
- read the current mouse position or hovered element
- configure which input devices are tracked
- work with `MouseButton` or `KeyboardEvent.code` values

## Workflow

1. Call `InputManager.initialise()` once before any queries.
2. Call `InputManager.update()` every frame before reading transient input state.
3. Use the polling methods to detect input.
4. Treat `keyPressed`, `keyReleased`, `mousePressed`, `mouseReleased`, `mouseWheelUp`, and `mouseWheelDown` as frame-scoped checks.

## Important Behavior

- `InputManager` throws if initialised more than once.
- Query methods throw if used before initialisation.
- `update()` copies current state to previous state and resets the mouse wheel delta.
- `mouseDown()`, `mousePressed()`, and `mouseReleased()` accept an optional `MouseButton` value.
- Touch input is mapped to the left mouse button.
- Keyboard queries use `KeyboardEvent.code` strings such as `KeyW`, `Space`, and `ArrowUp`.

## References

- Public API surface: [references/api.md](references/api.md)
- Type reference: [references/types.md](references/types.md)
- Usage patterns: [references/usage.md](references/usage.md)
