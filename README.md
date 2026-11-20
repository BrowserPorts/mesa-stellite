# mesa-stellite

mesa-stellite is the Mesa that
[stellite-gex](https://github.com/BrowserPorts/stellite-gex) builds. It is a
fork of [mesa-wink](https://github.com/MercuryWorkshop/mesa-wink), which is
[Mesa](https://mesa3d.org) with `gex_wgpu`, a Gallium driver that draws
through WebGPU. Compiled to WebAssembly, it gives the programs gex runs in a
browser page desktop OpenGL and OpenGL ES on the browser's WebGPU.

- Parent: [MercuryWorkshop/mesa-wink](https://github.com/MercuryWorkshop/mesa-wink),
  branch `wink`
- Used by: [stellite-gex](https://github.com/BrowserPorts/stellite-gex) and
  its fork [android-gex](https://github.com/BrowserPorts/android-gex), as
  their `third_party/mesa` submodule
- Related: [wine-stellite](https://github.com/BrowserPorts/wine-stellite),
  the Wine the same build uses

The branch `stellite` is the one stellite-gex pins.

## What it adds

`stellite` is the parent's `wink` branch with two changes to the `gex_wgpu`
driver (`src/gallium/drivers/gex_wgpu`):

- A copy whose box runs past the edge of either texture is cut to fit. WebGPU
  rejected the whole command buffer before, so the copy did nothing. The
  mismatch is reported once, with both the GL and the WebGPU sizes.
- `override_vram_size`, Mesa's driconf name, is read in MiB from the
  environment the page gives Mesa. It sets the video memory that
  `GL_NVX_gpu_memory_info` and `GL_ATI_meminfo` report. Both extensions are
  offered only when a size is named.

It also merges the parent's change that gives buffer texture ranges a 16-byte
alignment.

Everything else is the parent's: the `gex_wgpu` driver, the `wink` build of
Mesa as static libraries (`wink/`), and Mesa itself.

## Building

stellite-gex builds this tree as its submodule. From a stellite-gex checkout:

```console
$ make mesa      # Mesa for the page alone
$ make page      # the whole page, Mesa included
```

Both check the submodule out themselves. stellite-gex's `page/README.md` and
`ports/mesa/build.sh` describe the build and its settings. The first Mesa
build also builds Tint, so it takes much longer than later ones.

The parent's own build, `wink/build.sh`, is in this tree too and is
unchanged. `wink/README.md` covers it.

## Using it

Inside gex, a guest's OpenGL calls go through gex's own libGL and libEGL to
this Mesa, which draws with the browser's WebGPU. A `MesaGl` made before boot
gives the runtime's programs GL in their windows, as stellite-gex's
`page/runtime/README.md` describes.

## Licence

Mesa's own. The core library and most of the code are under the MIT licence,
and individual files may carry their own. `docs/license.rst` explains it, and
the `licenses/` directory holds every licence used. The `gex_wgpu` driver's
files are marked MIT.

## Contributing

This repository takes no pull requests and no issues. Both are turned off
for the BrowserPorts organisation.
