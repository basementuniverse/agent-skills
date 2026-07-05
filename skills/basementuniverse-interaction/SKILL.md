---
name: basementuniverse-interaction
description: >
  Use when working with @basementuniverse/interaction to build or modify canvas-based UI interactions such as focus, selection, clicking, dragging, dropping, resizing, rotating, sliding, and dial controls.
---

# Basement Universe Interaction

Use this skill when working with `@basementuniverse/interaction`.

This library provides a stateful, rendering-agnostic interaction engine for canvas-style UIs and browser games. It centers on `InteractionSystem`, `InteractionState`, and composable behaviors created with `createBehavior(...)`.

## When to use

- Adding interactive UI to canvas-based games or custom renderers.
- Wiring pointer and keyboard input into `InteractionSystem.update(dt, input)`.
- Defining interactive entities with `InteractionState` and behavior objects.
- Implementing hover, focus, selection, click, drag and drop, resize, rotate, slider, or dial interactions.
- Adjusting key bindings, thresholds, z-index ordering, or pointer targeting rules.

## Core workflow

1. Create an `InteractionSystem`.
2. Give each interactive object an `interactionState`.
3. Add behaviors either directly with `interactionState.addBehavior(...)` or at registration time with `system.register(interactable, ...behaviors)`.
4. Call `system.update(dt, input)` once per frame.
5. Render separately using the state and callback effects produced by the behaviors.

## Best practices

- Register interactables through `InteractionSystem.register(...)` so the state owner is set correctly.
- Prefer `createBehavior(...)` when constructing behavior objects; it fills in defaults for the runtime.
- Use `interactionState.setState(...)` when you need to force a behavior state externally.
- Treat input as edge-sensitive: `keyPressed`, `mousePressed`, and `mouseReleased` must behave per frame.
- Keep rendering order separate from interaction order if needed; interaction uses descending `zIndex`.

## References

- Public API surface: [references/api.md](references/api.md)
- Type reference: [references/types.md](references/types.md)
- Practical usage notes: [references/usage.md](references/usage.md)
