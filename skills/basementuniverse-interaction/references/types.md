# Type Reference

This file summarizes the exported type surface from `index.ts` / `build/index.d.ts`.

## Core types

- `Anchor`
- `PointerState`
- `HitTestFunction`
- `DisableableState`
- `HoverableState`
- `FocusableState`
- `SelectableState`
- `ClickableState`
- `DraggableState`
- `DropZoneState`
- `DropZoneHoveredState`
- `ResizableState`
- `RotatableState`
- `SlideableState`
- `DialableState`
- `KeyBindings`
- `InteractionOptions`
- `DropZoneOffsetFunction`
- `InteractionContext`
- `Behavior`
- `Interactable`

## Behavior shapes

- `DisableableBehavior`
- `HoverableBehavior`
- `FocusableBehavior`
- `SelectableBehavior`
- `ClickableBehavior`
- `DraggableBehavior`
- `DropZoneBehavior`
- `ResizableBehavior`
- `RotatableBehavior`
- `SlideableBehavior`
- `DialableBehavior`

## Behavior-specific notes

### Disableable

- State: `enabled | disabled`
- Callbacks: `onEnable`, `onDisable`

### Hoverable

- State: `idle | hovered`
- Callbacks: `onEnter`, `onLeave`

### Focusable

- State: `idle | focused`
- Callbacks: `onFocus`, `onBlur`

### Selectable

- State: `idle | selected`
- Optional: `multiSelect`, `multiSelectKey`, `bringToFrontOnSelect`
- Callbacks: `onSelect`, `onDeselect`

### Clickable

- State: `idle | pressed`
- Callbacks: `onPress`, `onRelease`, `onClick`, `onLongPress`, `onDoubleClick`

### Draggable

- State: `idle | dragging`
- Optional: `axisConstraint`, `bounds`, `bringToFrontOnDrag`, `dropInDropZoneOnly`, `snapPosition`
- Callbacks: `onDragStart`, `onDragEnd`, `onDrag`, `onCancel`

### DropZone

- State: `empty | occupied`
- Hovered state: `idle | hovered_acceptable | hovered_not_acceptable`
- Optional: `maxInteractables`, `offset`
- Callbacks: `accepts`, `onDragEnter`, `onDragLeave`, `onDrop`

### Resizable

- State: `idle | resizing`
- Optional: `handles`, `handleSize`, `aspectRatioConstraint`, `resizeFromCenter`, `snapSize`
- Callbacks: `onResizeStart`, `onResizeEnd`, `onResize`, `onCancel`, `onHandleEnter`, `onHandleLeave`

### Rotatable

- State: `idle | rotating`
- Optional: `rotationCenter`, `directionConstraint`, `snapAngle`
- Callbacks: `onRotateStart`, `onRotateEnd`, `onRotate`, `onCancel`

### Slideable

- State: `idle | sliding`
- Optional: `axis`, `stepSize`
- Callbacks: `onSlideStart`, `onSlideEnd`, `onSlide`, `onCancel`

### Dialable

- State: `idle | turning`
- Optional: `rotationCenter`, `directionConstraint`, `snapAngle`, `stepSize`
- Callbacks: `onTurnStart`, `onTurnEnd`, `onTurn`, `onCancel`
