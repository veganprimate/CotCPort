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
 
## Update (03/04/2026)
**MarvinCx886** was able to **reconstruct** `Map_DP_Forest_N0_1009_A1` in OT0 (with many caveats however) using the [latest version of UAssetGUI](https://github.com/atenfyr/UAssetGUI/releases/tag/experimental-latest), you will need the Octopath Traveler 0 Mappings and obviously access to CotC and OT0's umap assets.

Below a **comparison** of the two games' `Level/` folders which may help you do some elementary porting of maps of that kind yourself:
<img width="3699" height="4806" alt="image" src="https://github.com/user-attachments/assets/744c1c28-3121-427a-b58c-42ffb8c9ccac" />


### 1. Directory structure comparison
CotC Top-Level Structure:
```
  Level/
  ├── Area/         (1,151 files) - Map visual/gameplay data
  │   ├── Gacha/    (266 files) - Gacha system visuals
  │   ├── Title/    (54 files) - Title screen assets
  │   └── V1_0_0/   (831 files) - Main game maps (47 sub-regions)
  ├── PathActor/    (847 files) - Navigation/pathing DataTables
  │   ├── PathActorBP.json
  │   └── V1_0_0/   (845 files) - Per-map path data
  ├── Sequencer/    (297 files) - Level Sequencer animations *** ABSENT in OT0 ***
  │   ├── Battle/   (51) - Battle time-zone changes
  │   ├── Battle_Sub/ (61) - Battle sub-sequences
  │   ├── Common_Time/ (88) - Common time-of-day sequences
  │   ├── Dungeon/  (27) - Dungeon sequences
  │   └── [8 more categories]
  ├── Footstep/     (5 files) - Physics materials for footsteps
  ├── BP/           (2 files) - Tutorial blueprints
  ├── Persistent - World persistent level
  └── ResidentGame - Always-loaded game level
```
OT0 Top-Level Structure:
```
  Level/
  ├── Area/         (1,166 files) - Map visual/gameplay data (split into sub-levels)
  │   ├── Gacha/    (5 files) - Minimal gacha assets (textures/materials only)
  │   ├── Status/   (9 files) - Status screen assets *** NEW in OT0 ***
  │   ├── Title/    (10 files) - Title screen assets (reduced)
  │   └── V1_0_0/   (1,142 files) - Main game maps (14 sub-regions, with _EFF/_GD)
  ├── PathActor/    (502 files) - Navigation/pathing DataTables
  │   ├── PathActorBP.json
  │   └── V1_0_0/   (501 files) - Per-map path data
  ├── Camera/       (11 files) - Camera system *** NEW in OT0 ***
  │   ├── Component/ - Camera component blueprints
  │   ├── Curve/    - Camera blend/follow curves
  │   └── Trigger/  - Camera trigger blueprints
  ├── Footstep/     (7 files) - Physics materials (expanded)
  ├── BP/           (1 file) - Generic collision blueprint
  ├── Persistent - World persistent level
  └── ResidentGame - Always-loaded game level
```
### 2. Asset path/Package naming convention
CotC uses: `Game/` prefix for all asset references
  Example: `Game/Level/Area/V1_0_0/Snow/Map_DP_Snow_N0_1001_A1`

OT0 uses: `Octopath_Traveler0/Content/` prefix
  Example: `Octopath_Traveler0/Content/Level/Area/V1_0_0/Snow/Map_DP_Snow_N0_1001_A1`

This reflects the project renaming from a generic `Game` to the specific
`Octopath_Traveler0` project name. The `Content/` intermediate folder is 
standard for UE5 packaged projects.

Script references:
 - Both use: `/Script/Kingship` (shared game module name)
 - Both use: `/Script/Engine`, `/Script/CoreUObject`

### 3. Map file conventions
Maps follow: `Map_XX_Region_N0_NNNN_VV[_SUFFIX]`, in very rare cases, `R0` is used instead of `N0` (e.g. `Map_DB_Other_R0_0001_A1` in OT0 and CotC).

**Type codes** (XX):
 - `BB` = **Battle Background** - Arena visual backdrops (small, ~200-600 objects)
 - `DB` = **Dungeon Background** - Dungeon environmental layers
 - `DP` = **Dungeon Playfield** - Interactive dungeon areas (largest, ~1000-5000+ objects)
 - `EV` = **Event** - Scripted event/cutscene areas
 - `FB` = **Field Background** - World field backdrop layers
 - `FP` = **Field Playfield** - Traversable overworld areas
 - `HB` = **Hub Background** - Hub area visual layers (CotC only)
 - `HP` = **Hub Playfield** - Hub interactive areas (CotC: 78, OT0: 12)
 - `OP` = **Other(?) Playfield** - World map areas (CotC only: 34)
 - `SB` = **Special/Summon Background** (CotC only: 14)
 - `SQ` = **Sequence/Special area** (CotC only: 2)
 - `TB` = **Town Background** - Town visual layers
 - `TP` = **Town Playfield** - Interactive towns with NPCs

The OT0 SDK defines the following type codes:
```hpp
// Enum Kingship.EMAP_CATEGORY
// NumValues: 0x0012
enum class EMAP_CATEGORY : uint8
{
	NN                                       = 0,
	TP                                       = 1,
	TB                                       = 2,
	TE                                       = 3,
	FP                                       = 4,
	FB                                       = 5,
	FE                                       = 6,
	DP                                       = 7,
	DB                                       = 8,
	DE                                       = 9,
	HP                                       = 10,
	HB                                       = 11,
	WP                                       = 12,
	WB                                       = 13,
	EV                                       = 14,
	BB                                       = 15,
	EN                                       = 16,
	EMAP_MAX                                 = 17,
};
```


**OT0 Suffixes** (**NOT** present in CotC; Sub-Level splitting):
 - `(none)` = **Base map** (visual meshes, landscape, static content)
 - `_GD`    = **Game Design** sub-level (NPCs, triggers, cameras, pathing, gameplay logic)
 - `_EFF`   = **Effects** sub-level (Niagara particles, VFX)

  322 maps have _EFF sub-levels
  
  368 maps have _GD sub-levels
  
  321 maps have BOTH _EFF and _GD sub-levels

  ### 4. Map regions comparison
**Shared Regions** (17):
- Cliff
- Desert
- Forest
- Heaven
- Hell
- HellCliff
- HellForest
- HellMount
- Mount
- OblCliff
- OblForest
- OblSea
- Other
- Plain
- River
- Sea
- Snow

**CotC-Only Regions** (30):
  - **Typhoon-named regions** _(These are regions related to **collaborations**)_ (8):
    - Atsani
    - Bolaven
    - Danas
    - Hagupit
    - Jangmi
    - Krosa
    - Nakri
    - Ragasa  
  - **QPS** _(="QueenPort Solistia", i.e. **Side Solistia**)_ regions (10):
    - QPSCity
    - QPSCity1
    - QPSDesert
    - QPSForest
    - QPSGacha
    - QPSIsland
    - QPSMount
    - QPSOther
    - QPSSea
    - QPSSnow
    - QPSWilderness
  - **CotC-specific** (12):
    - AutoDun
    - Dice
    - EventSq
    - Gacha
    - Other1
    - Other4
    - Sea1
    - Summon
    - THE
    - Tower
    - Tower1

**OT0-Only Regions**: NONE (all OT0 regions exist in CotC)

### 5. Critical UE 4.26 → UE 5.4.2 Migration Changes
#### 5a. Particle System Migration (Cascade → Niagara)
**CotC**: `ParticleSystemComponent` (10,880) + `Emitter` (6,639)

**OT0**:  `NiagaraComponent` (12,027) + `NiagaraActor` (6,657)
  - Near 1:1 replacement, with Niagara effects now in separate `_EFF` sub-levels

#### 5b. Sub-Level Architecture
**CotC**: Monolithic level files (all content in one file per map)

**OT0**: Split sub-level architecture:
    - Base level: Visual art (meshes, landscape, materials)
    - `_GD` sub-level: Game design (NPCs, triggers, cameras, audio)
    - `_EFF` sub-level: Visual effects (particles, Niagara)
Connected via `LevelStreamingAlwaysLoaded` references in base level.

#### 5c. Sequencer Content (CotC only!)
 **CotC**: 297 Sequencer files (52.3 MB in FModel-extracted JSON package property files) for time-zone changes, battles, etc.
    - Contains `MovieScene`, `LevelSequence`, `MovieScene3DTransformTrack`, etc.
    - Used for day/night cycles, cutscenes, lighting changes

 **OT0**:  ZERO Sequencer files in Level folder
    - Sequencer content likely moved to separate asset folders or replaced with runtime systems

#### 5d. Npc System Changes
 **CotC**: `NpcAppear_C` (11,044) + `NpcComponent` (250)
 
**OT0**:  `BP_NpcAppear_C` (7,735) + `NpcTalkWidgetComponent` (7,735)
  - NPCs renamed with `BP_` (=BluePrint) prefix
  - Each NPC now has a dedicated `NpcTalkWidgetComponent` (UI)
  - NPCs moved to `_GD` sub-levels

#### 5e. Lighting System Overhaul
**CotC**: 192 `PointLight`, 4 `SpotLight`, 4,436 `LightAreatrigger2BP_C`

**OT0**:  3,423 `PointLight` (+1,683%), 3,039 `SpotLight` (+75,875%), 0 `LightAreatrigger2BP_C`
  - Massive increase in dynamic lights
  - Light area triggers replaced with proper light actors
  - New: `BP_RoomLight`, `BP_LightStudio_Map_C` systems

#### 5f. Camera System
**CotC**: Camera logic embedded in main level files

**OT0**:  Dedicated `Camera/` sub-directory with:
    - `QPCameraComponent` (blueprint)
    - `QPDefaultFollowCamera`, `QPSeaFollowCamera`
    - Camera curves (blend, offset, rotation)
    - Camera triggers (blend, lock)
    - `VillageEditCamera`

#### 5g. Landscape Changes
 **CotC**: 4,521 `LandscapeComponent` + 4,521 `LandscapeLODStreamingProxy`
 
**OT0**:  13,345 `LandscapeComponent` + 0 `LandscapeLODStreamingProxy`
  - ~3x more landscape components (higher resolution terrain)
  - `LandscapeLODStreamingProxy` removed (UE5 handles LOD differently via Nanite/VSM)

#### 5h. Collision/Trigger System
**CotC**: `AreaTriggerPrimitiveComponent` (3,152)

**OT0**:  `BoxComponent` (46,167) + `AreaTriggerPrimitiveComponent` (1,011) + `BlockingVolume` (318) + `SpringArmComponent` (190)
  - `BoxComponent` is the dominant collision/trigger primitive in UE5

#### 5i. Instanced Mesh System
  **CotC**: `HierarchicalInstancedStaticMeshComponent` (52,735) + `FoliageInstancedStaticMeshComponent` (4,092)

  **OT0**:  `HierarchicalInstancedStaticMeshComponent` (80,229) + `FoliageInstancedStaticMeshComponent` (2,987) + `MapObjectConvertedFoliage` (3,339) **[NEW]**
  - New `MapObjectConvertedFoliage` type for optimized foliage rendering

### 6. Serialization Differences
#### 6a. Object-Level Keys:
**CotC-only keys**: `ChildProperties`, `ScriptBytecode`, `FunctionFlags`, `FuncMap`, `Children`
    → Blueprint class definitions **embedded in level files**

  **OT0-only keys**: `ActorLabel`, `Template`, `PerInstanceSMData`, `ClusterTree`, `EditorTags`, `SizeX/SizeY/PackedData/PixelFormat/Mips`
    → UE5 adds **actor labels**, **template references**, **inline texture metadata**

#### 6b. Template Pattern
**CotC**: 1 object uses Template references

**OT0**: 51,880 objects use Template references
  - UE5 **extensively** uses Template-based instantiation instead of embedding full class definitions

#### 6c. PathActor DataTable Schema
**Shared fields** (16): `PathName`, `X`, `Y`, `Z`, `MiniMapView`, `HiddenPath`, `PathRoutePassed`, `PathClosed`, `LinkExcluded`, `UniqueLabel`, `FastTravel`, `MinimapFastTravel`, `MapIconType`, `SaveBitKey`, `Link`, `DirLink`

**CotC-only fields** (6): `SweptOffLink`, `DistanceGoal`, `PosX`, `PosY`, `PosZ`, `PortalType`
  - Mobile-specific features (swept-off mechanics, portal navigation)

#### 6d. ObjectPath Format
  **CotC**: `Game/Level/Area/V1_0_0/...`
  
  **OT0**:  `Octopath_Traveler0/Content/Level/Area/V1_0_0/...`

### 7. Content removed in UE5 Upgrade
- 297 Sequencer files (time-zone/battle sequences) 
- 261 Gacha files (gacha system art, widgets, materials) - mobile-only content
- 44 Title screen assets (reduced from 54 to 10)
- 30 entire map regions (QPS, typhoon names, special areas)
- 387 canonical maps that exist only in CotC
- `MovieScene` types (7,092 easing functions, 624 sub-sections, etc.)
- `TimeZone` components (emitter, sound, light param)
- Treasure, Symbol Enemy, Map Object components
- `BokehBoll_C` reduced from 8,137 to 7
- `ChildActorComponent` reduced from 8,162 to 615
- `QPPostProcessVolume` (165 → 0)

### 8. Content added in UE5 Upgrade
- `Camera/` directory (11 files) with dedicated camera system
- `Status/` directory (9 files) for UI status effects
- `_GD` and `_EFF` sub-level architecture (690+ files)
- `NiagaraComponent`/`NiagaraActor` (replacing Cascade)
- `BoxComponent` (46,167 instances)
- `ArrowComponent` (8,592 instances)
- `NpcTalkWidgetComponent` (7,735 instances)
- `TextRenderComponent` (1,188 instances)
- `LevelStreamingAlwaysLoaded` (734 references)
- `SpringArmComponent`, `CineCameraComponent`
- Various new light types and room light blueprints
- `MapObjectConvertedFoliage` (3,339 instances)
- `BlockingVolume` (318), `BP_RoomBox_C` (584)
- New lamp/torch 9pcs variants (batch-optimized props)
- Footstep materials: Canoe, Snow, Wood added; Soil removed

### 9. Various resources
 - [Complete inventory of all 3,993 analyzed files with metadata](https://github.com/veganprimate/CotCPort/blob/main/MiscResources/Maps_Prog/file_inventory.csv)
 - [2,281 object types compared across both versions](https://github.com/veganprimate/CotCPort/blob/main/MiscResources/Maps_Prog/object_type_comparison.csv)
 - [892 canonical map names with cross-version matching](https://github.com/veganprimate/CotCPort/blob/main/MiscResources/Maps_Prog/map_comparison.csv)
 - [47 regions with file counts and presence flags](https://github.com/veganprimate/CotCPort/blob/main/MiscResources/Maps_Prog/region_comparison.csv)
 - [All 299 CotC sequencer files analyzed](https://github.com/veganprimate/CotCPort/blob/main/MiscResources/Maps_Prog/sequencer_analysis.csv)
 - [Key UE4.26→UE5.4.2 type migration mappings](https://github.com/veganprimate/CotCPort/blob/main/MiscResources/Maps_Prog/type_migration_patterns.csv)
