# API Surface

`@basementuniverse/interaction` exports a small set of runtime primitives and a broader type surface for behavior-driven interaction.

## Main runtime exports

- `InteractionSystem`
- `InteractionState`
- `BehaviorType`
- `createBehavior(type, params)`
- `Interactable`

## InteractionSystem

Create an instance with optional key bindings and thresholds:

```ts
const system = new InteractionSystem({
  keyBindings?: {
    focusNext?: string[];
    focusPrevious?: string[];
    focusLeft?: string[];
    focusRight?: string[];
    focusUp?: string[];
    focusDown?: string[];
    activate?: string[];
    cancel?: string[];
  },
  thresholds?: {
    doubleClick?: number;
    longPress?: number;
    dragSelect?: number;
  },
});
```

Public members and methods:

- `interactables: Interactable[]`
- `dragSelectEnabled: boolean`
- `keyBindings: KeyBindings`
- `options: InteractionOptions`
- `doubleClickThreshold`
- `longPressThreshold`
- `dragSelectThreshold`
- `selectionBox`
- `register(interactable, ...behaviors)`
- `unregister(id | interactable)`
- `update(dt, input)`
- `setKeyBindings(bindings)`
- `setThresholds(thresholds)`
- `startDrag(...)`
- `bringToFront(interactable)`
- `endDrag()`
- `detachInteractableFromDropZones(interactable)`
- `reattachInteractableToDropZones(interactable, entries)`
- `addInteractableToDropZone(interactable, dropZone, behavior)`

## InteractionState

Each interactable owns one `InteractionState`.

Constructor:

```ts
new InteractionState(id, position, size, hitTest?)
```

Important fields:

- `id: string`
- `position: vec2`
- `size: vec2`
- `behaviors: Behavior[]`
- `zIndex: number`
- `tabIndex: number`
- `anchor: Anchor`
- `consumePointerEvents: boolean`
- `receivePointerEvents: boolean`
- `hitTest?: HitTestFunction`
- `owner?: Interactable`

Methods:

- `setState(type, newState)` for every behavior type
- `addBehavior(...behaviors)`
- `removeBehavior(...behaviorTypes)`
- `update(dt, input, context?)`
- `isDisabled()`

## Behavior creation

Use `createBehavior(type, params)` to build a behavior with default state and no-op callbacks.

Runtime defaults include:

- `Disableable`: `state: 'enabled'`
- `Hoverable`, `Focusable`, `Selectable`, `Clickable`, `Draggable`, `Resizable`, `Rotatable`, `Slideable`, `Dialable`: `state: 'idle'`
- `DropZone`: `state: 'empty'`, `hoveredState: 'idle'`, `interactables: []`, `accepts: () => true`
- `Resizable`: corner handles, `handleSize: 10`, `minSize: (0, 0)`, `maxSize: (Infinity, Infinity)`
- `Rotatable`: `angle: 0`, `minAngle: -Infinity`, `maxAngle: Infinity`
- `Slideable`: `value: 0`, `minValue: 0`, `maxValue: 1`, `axis: 'x'`
- `Dialable`: `angle: 0`, `value: 0`, `minAngle: -Infinity`, `maxAngle: Infinity`, `minValue: 0`, `maxValue: 1`

Note: the TypeScript return type includes `| undefined`, so callers often use a non-null assertion when they know the `BehaviorType` is valid.

## Input contract

`InteractionSystem.update(dt, input)` expects an input provider with:

```ts
{
  keyDown(code?: string): boolean;
  keyPressed(code?: string): boolean;
  keyReleased(code?: string): boolean;
  mouseDown(button?: number): boolean;
  mousePressed(button?: number): boolean;
  mouseReleased(button?: number): boolean;
  mouseWheelUp(): boolean;
  mouseWheelDown(): boolean;
  mousePosition: vec2;
}
```

## Behavior types

- `BehaviorType.Disableable`
- `BehaviorType.Hoverable`
- `BehaviorType.Focusable`
- `BehaviorType.Selectable`
- `BehaviorType.Clickable`
- `BehaviorType.Draggable`
- `BehaviorType.DropZone`
- `BehaviorType.Resizable`
- `BehaviorType.Rotatable`
- `BehaviorType.Slideable`
- `BehaviorType.Dialable`
