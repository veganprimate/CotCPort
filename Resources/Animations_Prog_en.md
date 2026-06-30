## **LANGUAGE:**  [English](Animations_Prog_en.md) | [日本語](Animations_Prog_jp.md) | [中文](Animations_Prog_zh.md) | [한국어](Animations_Prog_kr.md)

Porting VFX from **cooked UE4.27** assets from CotC into a **UE5.4** editor project is hard for two structural reasons:

### (1) You don't have uncooked `.uasset` graphs
For meshes/textures you can reconstruct from **GLB/PNG + JSON** (and FModel luckily gives you the option to export meshes as GLB). For effects you can’t, because the asset _is the graph_:

- **Cascade** `ParticleSystem` contains a stack of modules (Spawn, Lifetime, Velocity, Orbit, Drag, Color over Life, SubUV, Mesh TypeData…).
- In UE5 the canonical system is **Niagara**, which is not a direct 1:1 serialized representation of Cascade modules.
- The cooked dump from e.g. FModel gives you **module property values**, but not a ready-to-reimport graph that UE5 can load.

So VFX reconstruction is inherently semantic conversion, not asset import.

### (2) Materials are part of the VFX behavior
CotC's effects rely heavily on:
- Dynamic Parameters / curves driving material parameters
- Masked vs translucent behavior, dithering, noise scrolling, refraction/distortion, etc.

You can't load CotC cooked material `.uasset`s (unversioned cooked packages). So even if you perfectly rebuild Niagara spawn behavior, the look will still be wrong unless you also supply UE5 materials that accept the same parameters.

---

Even so, I have still made some moderate progress on this front and this is what I believe to be the core pattern that will also drive drive VFX:

- **Lenient JSON loader** (critical because FModel dumps often contain concatenated JSON/"Extra data")
- **Import raw sources** (PNG → Texture2D, GLB → StaticMesh) into a mirrored `/Game/...` structure
- **Generate placeholder master materials** + MICs, then fix parameters via heuristics
- **Assign materials to meshes** without `StaticMeshEditorSubsystem`
- Build Paper2D sprites/flipbooks (already has been done)

In other words: we have to reconstruct assets from source formats and metadata (never attempt binary conversion).

## Plausible but so far untested strategy

We treat it in multiple stages:

### Stage 0 (Spec extraction)
Input: Cascade JSON  
Output: `vfx_specs/*.json` with normalized fields:
- emitters list
- per-emitter: renderer type (sprite/mesh/ribbon), spawn rate/burst, lifetime, initial location/velocity, drag, etc.
- dynamic parameter curves (sample arrays, names, target channels)
- dependencies: textures/material names/mesh names
Goal: make the specs stable and independent of UE's internal property naming quirks.

### Stage 1 (Resource import)
- import textures with VFX settings
- import meshes
- (optional) import Niagara materials if we have them as sources, or create repro masters

### Stage 1.5 (Build a VFX repro material set)
I have previously done this to a much simpler effect to create assets for enemy sprites under EnmAdd:
- Create a small set of UE5 master materials, e.g.:
  - `M_VFX_Sprite_Additive`
  - `M_VFX_Sprite_Translucent`
  - `M_VFX_Mesh_Additive/Translucent`
  - Optional: distortion/refraction master if CotC uses it (haven't checked)
- Ensure they accept parameters that CotC effects expect:
  - `NoiseTex1/2`, scroll speeds, intensity/power
  - `DynamicParam0-3` equivalents (or named scalar params)
  - `MaskColor`, clip, dither alpha, etc.
- Then generate MICs from your VFX material JSON (if you have them) or generate MICs from heuristics (texture naming + defaults).

This prevents Niagara work from being wasted on effects that spawn but don't look right.

### Stage 2 (Template emitters library)
Keep a fixed set under e.g. `/Game/_VfxTemplates` that contain:
- Sprite emitter with standard spawn/update + sprite renderer bindings
- Mesh emitter with mesh renderer binding to a user param or a default mesh entry
- (Optional) ribbon/beam templates if CotC uses them
Do not rely on creating empty emitter and build stacks

### Stage 3 (Niagara asset generation)
For each spec:
1) Duplicate emitter templates into e.g. `/Game/Effect/Generated/...`
2) Create `CurveFloat` assets for dynamic curves
3) Patch:
   - mesh renderer static mesh (e.g., `FxMD_Btl_Cylinder_A`)
   - renderer material overrides/MIC assignment
   - spawn rate, lifetime, size, color (but only if those are exposed as user parameters in your templates)
4) Assemble emitters into a Niagara System

### Stage 4 (Cooking and packaging)
Although it may be more time-efficient to play these effects in Unreal before cooking and comparing them to CotC assets if playable. Given that Effect-related DataBase assets have already been merged, the effects should show up in-game provided you have not changed their asset names.
