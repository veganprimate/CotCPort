## **LANGUAGE:**  [English](Maps_Prog_en.md) | [日本語](Maps_Prog_jp.md) | [中文](Maps_Prog_zh.md) | [한국어](Maps_Prog_kr.md)
Porting level from CotC into Octopath 0 is possible in a limited, reconstruction-based sense as with [Effects and Animations](Animations_Prog_en.md) and [Objects](Objects_Prog_en.md)., but it is not the same kind of problem as porting meshes/textures.
It's closer to rebuilding a scene graph from metadata, and you'll only get a faithful result if you
(a) have the referenced assets reconstructed, and
(b) accept that some systems won't transfer/need more involved solutions (Level Blueprint logic, baked lighting, nav, etc.).

A typical Unreal map (`.umap`) contains a hierarchy roughly like:

- UWorld: top-level world asset
  - PersistentLevel (ULevel): contains the actor list
    - AActor instances: `StaticMeshActor`, `DecalActor`, `PostProcessVolume`, Light actors, Blueprint actors, etc.
      - Components: `StaticMeshComponent`, `DecalComponent`, `BrushComponent`, etc.
  - StreamingLevels (optional): sublevels, or in UE5 World Partition/DataLayers
  
So a "map port" here means:
1. having all referenced assets available in the target project, and
2. recreating the actor placement + component properties.

From my very limited observations:
- CotC maps contain many placed actors like decals and volumes (→reconstructable)
- brush-based vlumes (`PostProcessVolume` brush) are reconstructable only approximately:
  -  In UE4, volumes often use BSP/Brush geometry (Model + `BrushComponent`)
  -  In UE5, brush support exists but automation is awkward; also Brush model data may be incomplete in FModel extracted JSONs
- `BodySetup.ConvexElems.VertexData` may be useful as it gives one a convex shape for a volume
- **Likely the biggest blockers:**
  - You cannot port Level Blueprint logic (`LevelScriptActor` BPGC) as the `LevelScriptActor` class is a cooked `BlueprintGeneratedClass` (FModel's feature to "decompile Blueprint to pseudo C++" can be very useful as a reference)
    - UE5 Editor will not load it from CotC dumps
    - even if you recreated the class name, the logic is still missing
  - CotC uses PhysX cooked collision data, OT0 uses Chaos physics (→we ignore cooked PhysX)
  - Asset dependencies must exist first: a map is mostly references: `StaticMesh` for props and ubildings, Material (often overriden per component), Decal materials, Texture/parameter collections, etc. (→Map porting can only come after Object/Character/VFX/etc. reconstruction)
  - CotC does not permit free movement on the map and may thus lack a lot of collision data anyway
  - Some map features are engine-version dependent (heightmap and layer info may not be exportable as simple GLB/PNG, Lightmass build data is not portable; UE5 uses different pipelines; HISM actors; PhysX vs. Chaos as mentioned above)

