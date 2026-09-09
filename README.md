# War Powers DXVK fork

[![License: zlib](https://img.shields.io/badge/license-zlib-2b6cb0?style=flat)](LICENSE)

A fork of [DXVK](https://github.com/doitsujin/dxvk) maintained for the native macOS development path of the [War Powers engine](https://github.com/abhishekpradhan/warpowers-engine): the engine's Direct3D 8 calls go through DXVK's D3D8 and D3D9 layers to Vulkan, and MoltenVK carries Vulkan to Metal. The engine checks it out as the nested submodule `engine/references/fbraz3-dxvk`. It is a development fork, not an upstream DXVK release.

It is not part of the browser game. [War Powers](https://github.com/abhishekpradhan/warpowers) renders in the browser through d8web (Direct3D 8 to WebGL2); the browser build fetches an upstream DXVK Native release tarball for its D3D8 compatibility headers only and never compiles, links or runs this code.

## Lineage

- [doitsujin/dxvk](https://github.com/doitsujin/dxvk) — upstream DXVK (remote `upstream`); this fork is based on the 2.7.1 line.
- [fbraz3/dxvk](https://github.com/fbraz3/dxvk) — the GeneralsX fork that added the native SDL3/macOS integration (remote `fbraz3`).
- [abhishekpradhan/warpowers-dxvk](https://github.com/abhishekpradhan/warpowers-dxvk) — this repository (`origin`, branch `main`): the fbraz3 macOS history plus the fork's own fixes and the `WP_DXVK_SPY` draw and state instrumentation used while bringing up the native renderer.

Upstream remotes are fetch-only in a maintained checkout; nothing is pushed to them from here.

## How the engine consumes it

`engine/cmake/dx8.cmake` selects the DXVK source for the `macos-vulkan` preset:

- Default (`SAGE_DXVK_USE_LOCAL_FORK=OFF`): a pinned fbraz3 commit (`DXVK_REMOTE_REF`) is cloned into `build/macos-vulkan/_deps/dxvk-src-fbraz3`, and this submodule is ignored.
- `-DSAGE_DXVK_USE_LOCAL_FORK=ON`: this checkout is the source.

Either way DXVK is built with Meson in native mode with only the SDL3 windowing backend enabled (the engine sets `DXVK_WSI_DRIVER=SDL3` itself at launch). The build directory is `engine/build/macos-vulkan/_deps/dxvk-build-macos`; `libdxvk_d3d8.0.dylib` and `libdxvk_d3d9.0.dylib` are copied into `engine/build/macos-vulkan/` for the engine to load. The Linux presets use upstream's prebuilt DXVK Native binaries instead and do not build this fork.

## Build the version pinned by War Powers

The engine's submodule pointer pins the commit. In a workspace with the submodules initialized (`git submodule update --init --recursive`), after the engine's [macOS toolchain guide](https://github.com/abhishekpradhan/warpowers-engine/blob/main/docs/BUILD/MACOS.md) (CMake, Ninja, Meson, Clang and a LunarG Vulkan SDK with MoltenVK), run these commands **from the engine directory**:

```sh
cmake --preset macos-vulkan -DSAGE_DXVK_USE_LOCAL_FORK=ON
cmake --build build/macos-vulkan --target dxvk_d3d8_install
```

Then build the engine target (`cmake --build build/macos-vulkan --target z_generals`). Edit sources in this checkout, never under `_deps/`.

## Contributing

[CONTRIBUTING.md](CONTRIBUTING.md) covers scope, validation and routing. Commit a DXVK change in this repository and push it before updating the engine's submodule pointer, so that a recursive clone always resolves. A browser walkthrough does not validate this dependency: test on the native macOS build and say which platforms were not tested.

## License

DXVK's [zlib/libpng license](LICENSE), its copyright notices and its third-party licenses are unchanged. This is an altered fork; credit for DXVK belongs to its upstream authors and contributors.

## Upstream note

Upstream DXVK is a Vulkan-based translation layer for Direct3D 8/9/10/11, used with Wine on Linux and, as DXVK Native, without Wine in ports like this one. Its [README](https://github.com/doitsujin/dxvk#readme), [wiki](https://github.com/doitsujin/dxvk/wiki) and [releases](https://github.com/doitsujin/dxvk/releases) cover the Wine setup, driver support and Windows DLL builds, none of which apply here. The upstream runtime switches that do apply to the native macOS engine build:

| Variable | Effect |
|---|---|
| `DXVK_HUD=1`, `DXVK_HUD=full` or a list such as `devinfo,fps,frametimes,drawcalls,memory,compiler` | On-screen HUD: GPU and driver, frame rate, frame times, draw calls, pipelines, memory, shader-compiler activity |
| `DXVK_LOG_LEVEL=none`, `error`, `warn`, `info` or `debug` | Log verbosity; `DXVK_LOG_PATH=<dir>` chooses where log files go (`none` disables them) |
| `DXVK_FRAME_RATE=<fps>` | Frame-rate limit; `0` uncaps |
| `VK_INSTANCE_LAYERS=VK_LAYER_KHRONOS_validation` | Vulkan validation layers (needs the Vulkan SDK) |
| `DXVK_CONFIG_FILE=<path>` or `DXVK_CONFIG="key = value; ..."` | Configuration file or inline settings |
