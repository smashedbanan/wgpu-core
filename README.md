# wgpu-core 30.0.1, patched for ExtractMeMaybe

The `wgpu-core` 30.0.1 package as published on crates.io (upstream
[gfx-rs/wgpu](https://github.com/gfx-rs/wgpu) commit `40f4a34ebaf56f9a046231f54125ad046239d3f3`,
path `wgpu-core`; see `.cargo_vcs_info.json`), with this README added, as the first commit, so that
a `[patch.crates-io]` entry takes its dependencies (`wgpu-hal`, `wgpu-types`, `naga`) from crates.io
as the published crate does. The commits after it are the changes. Licensed as upstream: MIT or
Apache-2.0 (`LICENSE.MIT`, `LICENSE.APACHE`).

## Changes

- A bind group no longer records an init action for a texture binding whose range is already
  initialised when the group is made, if the texture's usage cannot lose that (no
  `RENDER_ATTACHMENT`, which `TRANSIENT_ATTACHMENT` needs too): only a render pass's discard of an
  attachment makes initialised texture memory uninitialised again. Every flush of a
  bind group walks its init actions (`flush_bindings_helper`), so a bindless material slab of
  hundreds of textures re-checked all of them at every rebind, each check finding nothing to do.
