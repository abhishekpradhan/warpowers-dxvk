# Contributing to the War Powers DXVK fork

This private checkout is the native graphics dependency of the War Powers engine. Its source and attribution come from DXVK and the fbraz3 native/macOS integration fork. Follow the [engine's contribution guidance](https://github.com/abhishekpradhan/warpowers-engine/blob/main/CONTRIBUTING.md) alongside the dependency-specific notes below.

Work on the source here, then build it through the engine with `SAGE_DXVK_USE_LOCAL_FORK=ON` as described in [README.md](README.md). Do not edit generated `_deps` source trees or generated libraries. Commit the dependency change in this repository before updating the engine's submodule pointer.

Keep changes focused on demonstrated integration or rendering behavior. Preserve DXVK's license, upstream authorship and platform separation. Explain the original failure, the native hardware/backend used, the behavior after the change and the tested engine revision. A browser pass does not validate this dependency: the browser uses a different renderer. For shared DXVK code, assess impact beyond the macOS/SDL3 path and state which platforms were not tested.

Before any authorized push, verify that its destination is the intended War Powers fork; preserve upstream remotes as fetch-only. Do not open upstream issues or pull requests, change visibility, deploy, or publish releases without the owner's explicit authorization. Follow authorization already given for normal fork work in the current session. Prepare an upstream-compatible patch separately when requested, keeping private game data out of reports.

Do not commit tokens, machine-specific build settings, native build outputs, proprietary game assets or test captures containing unpublished material. Document relevant dependency changes in the parent engine's monthly worklog. AI-assisted contributions must be reviewed, tested and described accurately by their contributor.
