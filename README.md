# gpui

[Sonora](https://github.com/sonorahq/sonora)'s fork of [GPUI](https://github.com/zed-industries/zed),
the GPU-accelerated UI framework behind Zed. Everything below works on wgpu, Metal and DirectX.

## What the fork adds

- **Shapes and shadows.** Squircle corners with `corner_smoothing` and `squircle`, text shadows
  with `text_shadow`, and box shadows clipped to outside their element.
- **Blur and layers.** `blur`, `backdrop_blur`, `fade_edges` and `fade_sides`, rendered with a
  dual Kawase blur. `GPUI_FILTERS=0` turns them off.
- **Paint-only transforms.** `layer_scale`, `layer_scale_origin` and `layer_translate` with
  fractional values, and `Window::with_pixel_snapping` to keep a subtree off the pixel grid.
- **Text.** Font weights, the variable `wght` axis and weight-aware fallback in cosmic-text,
  colour emoji, and ClearType inside layers.
- **Memory.** Offscreen targets and instance buffers are freed or shrunk when idle.
- **App and windows.** `App::hide_cursor`, and fixes for cached window controls, sized images and
  Wayland fractional scaling.
- **Debugging.** `GPUI_DEBUG_OVERLAY`, `GPUI_SHOW_REPAINTS` and `GPUI_LOG_NOTIFIES` show frame
  timings, repaints and re-renders.

## Licence

Apache-2.0, inherited from Zed. See `LICENSE-APACHE`.
