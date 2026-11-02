# Torque DTS format for Blender 4.3

Import and export **Torque Game Engine DTS shapes and DSQ animation sequences (v24)** in Blender 4.3, made for Blockland and other TGE 1.5-era games.

This repository is the **Blender 4.3** version. The Blender 5.1 version is a separate repository: [io_scene_dts_5.1.2][other-version]. The two share the same exporter; only the Blender API layer differs.

## Install

Requires **Blender 4.3** (made for 4.3.2). This is a legacy add-on, not a Blender extension.

1. Download the release zip (not GitHub's "Download ZIP": that puts everything in a `-main` folder, and Blender then installs it under a different name).
2. `Edit > Preferences > Add-ons > Install from Disk...`, pick the zip.
3. Enable **Torque DTS format**.

The add-on's folder must be named `io_scene_dts`. Remove any older copy (`io_scene_dts-master` and the like) first, or two versions fight over the same operators.

### Working on the add-on

`dev_link.ps1` links `%APPDATA%\Blender Foundation\Blender\4.3\scripts\addons\io_scene_dts` to this folder (a directory junction), so Blender loads the code straight from the repository. Run it once, enable the add-on, and after editing use `F3 > Reload Scripts`. Don't also install it from disk. The `_*_selftest.py` scripts run outside Blender with plain Python.


## Usage

- **Export:** `File > Export > Torque (.dts)` or `Torque Sequences (.dsq)`, or search for **Export DTS** / **Export DSQ**.
- **Import:** `File > Import > Torque (.dts)` or `Torque Sequences (.dsq)`.
- Apply location, rotation and scale (`Ctrl+A`) on meshes before exporting: DTS meshes have no object transform of their own.
- Materials are looked up by name: a material named `boxTrapOrange` needs `boxTrapOrange.png` (or `.jpg`) next to the DTS. `foo#bar` uses the texture `foo`, as does a custom property `texture`.
- Collections set detail levels and collision: a collection named `detail32` is a detail level, `Col-1` meshes go in `Collision-1`, `LOSCol-9` in `LOS-9`. A mesh named `bounds` sets the bounding box. A collection named `__ignore__` is never exported.
- Name a sequence's timeline markers `name:start` / `name:end` when exporting from markers.

More answers are in [FAQ.md](FAQ.md).

## Features

**Import**
- DTS shapes (v24): meshes, UVs, materials and textures, node hierarchy, detail levels and collision meshes.
- Embedded sequences and DSQ files, as Blender Actions or as timeline markers, on armatures or empties.
- Skinned shapes import as armatures automatically; **Force armature** imports dummy-only shapes as bones too.

**Export**
- DTS and DSQ (v24), from the whole scene or from one collection per DTS.
- Sequences from individual Actions, or from timeline markers plus the `Sequences` text block.
- DSQ export can write **one DSQ per sequence**, with a file prefix / suffix (`m_`, `ae_`, ...).
- Node animation: rotation, translation and scale, visibility (objects and bones), IFL materials, morphs (shape keys, with bone-driven morph proxies), triggers, blend sequences with a reference frame, cyclic and priority flags, ground frames.
- **Optimize meshes** welds matching vertices and builds triangle strips the way the TGE exporters do, for smaller files.
- Auto-billboard (impostor) detail levels from an empty.
- Optional solid colour texture generation, blank materials for unassigned meshes, applying modifiers, and an export scale.
- Checks the TGE limits (16-bit primitive indices, node and object counts, skins with animated morphs) and refuses to write a file that would break in Show Tool / Blockland, unless **Force Export** is on.
- Optional log file next to the output (summary or full, with every vertex and animation track).

**Panels and tools**
- **Torque Material** (material properties): blend mode, wrap, two-sided, transparency, shadeless, IFL, mip mapping, detail / bump / reflectance maps.
- **Torque Sequence** (Dope Sheet): per-Action export options, timing (duration or frame rate), blend, priority, triggers, and what each sequence animates (transform, scale, visibility, IFL, morph).
- **Torque Visibility** (object and bone properties), **Torque Morph Proxy** (bone properties), **Torque Auto-Billboard** (object properties).
- **Torque Mount Simulation** (bone and object properties): parents one of Blockland's dummy items (gun, sword, wrench, ...) at a mount point to preview how an item sits in the hand.
- Mesh tools: **Split mesh by indices** (keeps a mesh under TGE's draw limit), **Collapse Unused Mesh Parents**, **Hide Blockhead nodes on selection**.
- Texture fallbacks: Blockland's stock colour textures ship with the add-on and are used on import when a material's texture can't be found next to the file.

## Node scale (read this if you animate scale)

TGE 1.5.1 only plays animated scale correctly when **every scaled sequence on a shape uses the same scale type** (the DTS and every DSQ loaded onto it together). A uniform sequence on a shape whose other sequences use per-axis scale draws with leftover scale values from whatever the engine animated last: stretched or squashed parts.

Since 1.1.0 the exporter writes **every sequence with scale keys as aligned (per-axis) scale**, which is exactly Blender's per-axis local scale, and gives sequences without scale keys no scale type at all, so they never change a shape's type. Files exported with older versions of this add-on, or with the original 3ds Max exporter, can still mix types; re-export them. The details, with references to the TGE 1.5.1 source, are in the comment block above `write_sequence_scales` in `shared_export.py`.

## Format references

- [DTS format](https://web.archive.org/web/20200906020103/http://docs.garagegames.com/torque-3d/official/content/documentation/Artist%20Guide/Formats/dts_format.html)
- [DSQ format](https://web.archive.org/web/20200906020104/http://docs.garagegames.com/torque-3d/official/content/documentation/Artist%20Guide/Formats/dsq_format.html)
- The TGE 1.5.1 engine source (`engine/ts`) is the ground truth for what the game actually reads.

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

## Credits and license

Based on [io_scene_dts](https://github.com/qoh/io_scene_dts) by port (Nick Smith), the Blender 2.8+ fork by bansheerubber (with visibility animations by irrelevant.irreverent), and work by Eagle517. Ported to Blender 4.3 and extended by aeclaudecode.

MIT License, see [LICENSE](LICENSE).

[other-version]: https://github.com/YOUR-GITHUB-NAME/io_scene_dts_5.1.2
