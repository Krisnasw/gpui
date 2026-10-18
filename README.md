# gpui

[Sonora](https://github.com/sonorahq/sonora)'s fork of [GPUI](https://github.com/zed-industries/zed),
the GPU-accelerated UI framework behind Zed. It follows upstream and adds the rendering,
animation and debugging pieces Sonora needs. Unless a line says otherwise, each feature works on
all three renderers: wgpu on Linux, Metal on macOS and DirectX on Windows.

## What the fork adds

### Shapes and shadows

- `Styled::corner_smoothing` takes rounded corners from circular arcs towards a squircle, from
  0 for round to 1 for corners close to the continuous ones of iOS. `Styled::squircle` is the
  full setting. The radius from `rounded` stays the size of the corner, and borders, box shadows
  and backdrop blur follow the smoothed outline. `cargo run --example squircle` puts both kinds
  side by side.
- `Styled::text_shadow` takes a list of `TextShadow`s, like CSS `text-shadow`, each with a
  colour, offset and blur. Shadows follow underlines and strikethroughs and leave colour emoji
  alone.
- A box shadow is clipped to outside its element, so a translucent element no longer shows its
  own shadow through it.

### Blur and layers

- `Styled::blur` blurs an element and its subtree as one composited layer.
- `Styled::backdrop_blur` blurs whatever sits behind an element, like CSS
  `backdrop-filter: blur()`.
- `Styled::fade_edges` and `Styled::fade_sides` fade a layer out towards its top and bottom or
  its left and right.
- Layer and backdrop blur run a dual Kawase pass, with a full-resolution Gaussian for small
  radii where Kawase would turn blocky.
- Neighbouring layers that ask for the same filter merge into one pass, and the frame stays on
  the swapchain unless a backdrop needs to read it.
- `GPUI_FILTERS=0` turns layer filters off.

### Paint-only transforms

- `Styled::layer_scale` scales an element and its subtree without touching layout, and
  `Styled::layer_scale_origin` picks the point it grows from.
- `Styled::layer_translate` moves a layer in the compositor without moving its layout or
  hitboxes.
- Both take fractional values, so spring animations move smoothly instead of stepping a pixel at
  a time.

### Pixel snapping

- `Window::with_pixel_snapping(false, …)` lets a subtree sit at fractional positions instead of
  having its layout rounded to device pixels.

### Text

- The cosmic-text backend honours the requested font weight, reads the `wght` axis of variable
  fonts and picks fallback fonts by weight.
- Colour emoji fonts are detected and drawn correctly.
- ClearType text blends correctly inside layers on Windows and under wgpu.

### Memory

- The renderers allocate offscreen targets on first use and free them once they sit idle or the
  window stops presenting.
- wgpu and Metal shrink their instance buffers after a quiet period.
- wgpu writes each atlas tile straight to the queue instead of keeping a copy of it until the
  next frame.

### App and windows

- `App::hide_cursor` hides the cursor until the mouse next moves.
- Window controls keep their hitboxes when a cached view is reused.
- An image given an explicit size keeps that size.
- Wayland windows keep their edges off half device pixels and handle fractional scaling better.

### Profiling and debugging

- `GPUI_DEBUG_OVERLAY=minimal` or `full` draws a frame overlay with layout, prepaint and paint
  timings and view render, cache and dirty counts.
- `GPUI_SHOW_REPAINTS=1` highlights what each frame redraws.
- `GPUI_LOG_NOTIFIES=1` logs which views notify and re-render.

## Shaders

The Metal and HLSL shaders compile only on their own systems, so the `shaders` workflow builds
the squircle example on macOS and Windows for every pull request that touches them and uploads
the binaries for a visual check. The `gpui_wgpu` tests validate the WGSL shaders.

## Licence

Apache-2.0, inherited from Zed. See `LICENSE-APACHE`.
