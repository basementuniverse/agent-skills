# Types

The library uses `vec2` from `@basementuniverse/vec` for 2D coordinates.

## CameraOptions

```ts
type CameraOptions = {
  bounds?: CameraBounds;
  allowScale: boolean;
  minScale: number;
  maxScale: number;
  moveEaseAmount: number;
  scaleEaseAmount: number;
};
```

## CameraBounds

```ts
type CameraBounds = {
  top: number;
  bottom: number;
  left: number;
  right: number;
};
```

## vec2

`vec2` comes from `@basementuniverse/vec` and is used for positions and screen sizes.

Typical shape:

```ts
{ x: number; y: number }
```
