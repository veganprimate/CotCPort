## **LANGUAGE:**  [English](Objects_Prog_en.md) | [日本語](Objects_Prog_jp.md) | [中文](Objects_Prog_zh.md) | [한국어](Objects_Prog_kr.md)
The `/Object` folder primarily contains world/gameplay props and their presentation assets, typically:
- **Static Meshes** (props, environment pieces, interactables)
- **Textures** used by those meshes (albedo/color, normal, masks, etc.)
- **Materials**/**Material Instances** to apply those textures
- (Sometimes) Paper2D **sprites/flipbooks** stored inside object resource folders (e.g. `Obj_Dice` in CotC)
As with [Effects and Animations](Animations_Prog_en.md), you cannot import the CotC's asset files into UE5 and we'll only be working with JSON, GLB, and PNG files to reconstruct these assets.
We also analogously construct a multi-stage process (although untested too, this one appears to be more promising than the one for `Effect`):

### Stage 0 (build a manifest)
A cooked dump is rather chaotic and a manifest gives one a deterministic map:
- Object → mesh JSON path
- Object → textures found
- Object → material configs
- Object → sprite frame JSON paths
We will also want to build a lenient JSON loader again as FModel export JSON often contain concatenated JSON objects or "extra data" parsing failures.

### Stage 1 (import raw resources: PNG → `Texture2D`, GLB → StaticMesh)
Import PNG files into UE5 as `Texture2D`, GLB as `StaticMesh`. Also apply different import settings depending on usage:
- Sprites / Paper2D
  - UI2D compression
  - no mips
  - nearest filter
  - clamp
  - TEXTUREGROUP_Pixels2D
- Objects / World / VFX
  - default compression
  - mips enabled
  - appropriate texture group (World, Effects, etc.)
This is essential, because object textures should look correct in 3D and sprite textures should not blur.

Note that FModel also sometimes exports GLB files with the `_LOD0`, `_LOD1`, etc. suffix, but JSONs often just expect `ObjMD_X` making no explicit references to e.g. `ObjMD_X_LOD0`, etc.

### Stage 2 (Materials)
As explained in [Effects and Animations](Animations_Prog_en.md), Unreal materials are node graphs. The cooked dump does not give you:
- full expression graph
- custom HLSL
- correct function graphs
- editor-only info
Even when you have some `CachedExpressionData`, it rarely reconstructs cleanly into a graph. So you'll have to build a small set of master materials on your own again, e.g.:
- `M_Obj_Master_Opaque`
- `M_Obj_Master_Masked`
- (a safe Paper2D sprite master)
Then for each object, you create a `MaterialInstanceConstant` and set parameters:
- BaseColor texture (suffix `_cl`)
- Normal texture (`_no`)
- Masks (`_mk`) etc.

### State 2b (parameter repair)
I have found that even with JSON metadata, parameters sometimes don't match the names you expect and textures end up as `DefaultTexture`.
So this stage would ideally apply heuristics:
 - if MIC parameter points to `DefaultTexture`, scan folder siblings for `*_cl`, `*_no`
 - assign those textures automatically
This reduces the manual cleanup from thousands of assets to a (hopefully) manageable set.

### Stage 3 (assign materials to meshes)
Static meshes store a list of material slots: `StaticMaterials`, ideally use the editor subsystem for these. If that is not possible (e.g. you're in commandlet trying to bulk-create assets):
- load mesh
- build new `static_materials` list
- `mesh.set_editor_property("static_materials", ...)`
- save mesh

This makes meshes actually render in levels.

### Stage 4 (Paper2D sprites and flipbooks)
Effectively just:
- reading baked source dimensions/UVs
- restoring baked render data (important for trimming/alignment)
- generating flipbooks and keyframes
These sprites sometimes reference textures under `/Game/Character/...` (`ChrTX_*`) and thus you should ideally create uncooked assets for those first, else, they show `DefaultTexture` (blank in-game).
