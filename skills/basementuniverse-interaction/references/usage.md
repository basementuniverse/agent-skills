# Practical Usage Notes

## Common pattern

```ts
import { InteractionSystem, InteractionState, BehaviorType, createBehavior } from '@basementuniverse/interaction';
import { vec2 } from '@basementuniverse/vec';

const system = new InteractionSystem();

const button = {
  interactionState: new InteractionState('button-1', vec2(100, 80), vec2(160, 44)),
};

system.register(
  button,
  createBehavior(BehaviorType.Hoverable, {
    onEnter: () => {},
    onLeave: () => {},
  })!,
  createBehavior(BehaviorType.Clickable, {
    onPress: () => {},
    onRelease: () => {},
    onClick: () => {},
    onLongPress: () => {},
    onDoubleClick: () => {},
  })!
);

system.update(dt, inputProvider);
```

## Input and frame semantics

- `keyPressed`, `mousePressed`, and `mouseReleased` must be edge-triggered for the current frame.
- The library assumes `update(dt, input)` is called once per frame.
- `mousePosition` is used for hit testing and drag/resize/rotate/slide interactions.

## Targeting rules

- Interactables are ordered by descending `zIndex`, then registration order.
- Pointer events stop at the first interactable that consumes pointer events.
- `receivePointerEvents = false` removes an interactable from pointer targeting.
- Resizable handles can be hit even if the pointer is outside the base hit box.

## Behavior authoring tips

- Use `interactionState.addBehavior(...)` to replace or add a behavior by type.
- Use `interactionState.setState(...)` when external code needs to drive a behavior state directly.
- If you need a custom hit test, pass `hitTest` into `InteractionState` or assign `interactionState.hitTest`.
- For drag/drop workflows, register the draggable and drop zone interactables through the system so ownership and drop-zone membership stay consistent.

## Demo files in this repository

If you need concrete examples, review the demo files in `demos/`:

- `demos/demo-click.html`
- `demos/demo-select.html`
- `demos/demo-drag.html`
- `demos/demo-sliders.html`
