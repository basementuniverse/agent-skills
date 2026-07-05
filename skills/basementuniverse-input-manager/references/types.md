# Types

## `InputOptions`

Controls which input sources are tracked.

```ts
type InputOptions = {
  element: Window | HTMLElement;
  mouse: boolean;
  mouseWheel: boolean;
  keyboard: boolean;
  preventContextMenu: boolean;
};
```

## `MouseButton`

Enum values used by the mouse polling methods.

```ts
enum MouseButton {
  Left = 0,
  Middle = 1,
  Right = 2,
}
```

## `MouseState`

Runtime mouse state tracked by the manager.

```ts
type MouseState = {
  buttons: { [key in MouseButton]: boolean };
  position: vec2;
  wheel: number;
  hoveredElement?: HTMLElement | null;
};
```

## `KeyboardState`

Runtime keyboard state keyed by `KeyboardEvent.code`.

```ts
type KeyboardState = {
  [key: string]: boolean;
};
```

## Related Dependencies

- `vec2` comes from `@basementuniverse/vec`
