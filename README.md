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

## Backports from upstream trunk

Upstream fixes from after 30.0.1 that change only `wgpu-core`'s source and none of its public API,
so `wgpu` 30.0.1 and Bevy 0.20.1 build against them unchanged. Each commit keeps its upstream
author and names the trunk commit it came from; their CHANGELOG, test and Cargo.toml changes are
left out.

- gfx-rs/wgpu#9791: zero-initialise the padding at the end of a buffer allocation.
- gfx-rs/wgpu#9960: drop `PendingSubmission`'s guards in reverse acquisition order (a deadlock).
- gfx-rs/wgpu#10002: corner cases in initialised range tracking.
- gfx-rs/wgpu#9958: a spurious assertion in `Device::maintain` when threads race to poll.
- gfx-rs/wgpu#9959: `Buffer::unmap` failing with `NotMapped` when it races a `Buffer::map`.
- gfx-rs/wgpu#10301: pending buffer maps no longer succeed after the device is lost.

`emm-30.0.1-3` also carried gfx-rs/wgpu#10188 (small bind group descriptors stored inline) and
gfx-rs/wgpu#10319 (one barrier for a batch of queue writes, recorded at submit). They are reverted
here: in ExtractMeMaybe's A/B they gained no frame time, and on its heaviest map submits got slower.
