# **LANGUAGE:**  **English** | [日本語](ExplainingPorting_jp.md) | [中文](ExplainingPorting_zh.md) | [한국어](ExplainingPorting_kr.md)
## What does it mean to "port" content from CotC over to OT0, and what is it that this project does?
There are a lot of misconceptions going around this project, usually because of the assumption that because OT0 is seemingly based on an older version of CotC JP, a lot of CotC content must still be fully functional but dormant. That **could not be further from the truth**, and it is crucial that anyone wishing to contribute understands the distinction.

There is an important distinction between OT0 being derived from / sharing design and content lineage with CotC and OT0 shipping with CotC's implementation sitting around waiting to be re-enabled. Those are completely different claims. In most cases, you have only got a few breadcrumbs left over from the original CotC system, nowhere near enough to call it a functioning system or something you could meaningfully test at runtime; it is usually just a few labels, fields, or dead identifiers at best.

The binaries make that distinction unusually easy to demonstrate. OT0 still statically registers `FQueenPortModule`, still has a reflected `UQueenPortSettings`, and even retains `ECURVEID::TimeZone_PlayerLightIn = 0xA5`. Yet the actual CotC/QPS runtime classes `ATimeZoneManager`, `UTimeOperator`, `ATimeZoneDirector`, and `UTimeZoneLightComponent` are present throughout CotC's native code and reflected metadata and are absent from OT0. That is exactly what I mean by a **breadcrumb** rather than a surviving subsystem.

The biggest exceptions to this in the mod are: the ailment **Combust**, **potency buffs**, and the **Enemy HP** and **Level** UI. Besides that, every new system in this modpack is implemented **from scratch**. I could've just as well implemented e.g. Crafting, higher damage values (and the damage cap mechanics and indicator), custom ailments, etc. in Octopath Traveler 1; the only perk OT0 gives here is that with the release of v1.0.8.0, it shipped with a PDB, making it far easier to work out the runtime code, but the OT1 binary conversely has **far fewer** functions, so much so that I have already labeled about 60% of them and am certain most of the left-over unlabeled ones are middleware stuff.

In fact, the DataBase-side similarities and pak layout for the most part is about as far as the analogies go, and if that is your metric for "good portability," you might as well say porting OT1 content over to OT2 is a triviality as well.

In some respects I would actually argue that porting OT1 content into OT2 is an easier problem than porting CotC content into OT0: CotC is built around **UE4-era cooked content targeting Android**, with the relevant build using **GLSL ES 3.1** and **ARM64/ARMv8-A**. OT0, by contrast, is a **UE5.4.2 Win64** title targeting the **desktop D3D/SM5 rendering path on x86-64**.

You are crossing:

* UE4 → UE5 package/serialization and engine-version differences
* mobile → desktop rendering assumptions
* GLSL ES → Direct3D shader infrastructure
* ARM64 → x86-64 native code
* different cooked asset formats and platform data
* different shader permutations and platform resources
* different engine subsystems and content authoring practices
* and, for effects in particular, cooked Cascade-era content → a usable UE5 effect implementation, often Niagara

Compare that with OT1 Switch and OT2: the Switch release of OT1 is already UE4.27.2, putting it vastly closer to OT2's content ecosystem than CotC's Android build is to OT0's UE5.4.2 PC build; but even if we were to stay on PC, a UE 4.18.0 → UE 4.27.2 migration is not even nearly as problematic as UE 4.27.2 → UE 5.4.2.


Recovering CotC's `.ushaderbytecode` was a major breakthrough because it lets me recover and study the actual GLSL ES programs generated for the Android version. That means instead of merely guessing that a material performs some radial mask/dither operation, I can trace the actual arithmetic.

But that does **not** mean I have recovered some magic UE5.4 Material Editor asset that can be dropped into OT0

 - It does not give me the original editable `UMaterialExpression` graph.
 - It does not give me an OT0 Win64 `FMaterialShaderMap`.
 - It does not turn an Android GLSL permutation into something I can drag into OT0 and call the port finished.

A recovered CotC GLSL shader is the _end product_ of CotC's UE4 material compilation pipeline for one specific target platform. OT0 needs material assets and shader permutations that make sense to UE5.4.2's own material system and Win64/D3D target. The GLSL therefore gives me evidence from which I can reconstruct the behavior; it does **not** give me directly reusable OT0 content.


Effects are even worse. A complicated CotC effect can depend on a pile of materials, material functions, textures, parameter collections, Blueprints, meshes, animation data, particle modules, sequencing data, runtime code and other assets. Those dependencies were cooked for an entirely different engine/platform target, and some editor-only information simply does not survive cooking in the same form.

Then, after recovering enough information to understand what the effect did, I still have to reproduce it in OT0's environment. Even when you possess the uncooked source content, Unreal's Cascade-to-Niagara conversion is not some perfectly deterministic one-click process. With cooked assets recovered from another game, another engine version, and another platform, expecting automatic conversion to reproduce the original behavior is absurd. A lot of the work becomes manual reconstruction: determine what the old system actually did, identify its dependencies and parameter flow, then build a Niagara system that behaves equivalently.

"A lot of the work becomes manual reconstruction" fits quite well for most of the project. As far as porting maps is concerned specifically, I would argue that porting over OT1's maps into OT0 is an easier task generally in spite of the DB-side differences. This is due to the shared platform, how much easier it is to dump data at runtime from an offline game, and the fact that OT0 more or less has all the materials and the like OT1 uses anyway.
### Example: the Lighting System
The **lighting system** is probably the cleanest example of where OT0 descending from the same codebase does not imply "CotC's systems are still intact inside OT0 and I am merely re-enabling them"

In CotC JP 4.8, ALightStage is constructed at `0x6F12AA8` and the constructor explicitly registers the environment MPC parameter names:
 - `SunBrightness`
 - `SunTint`
 - `LightCastShadow`
 - `FogTint`
 - `FogMaxOpacity`
 - `FogHeightFalloff`
 - `FogDensity`
 - `FogStartDistance`
 - `FogAltitude`
 - `SkyLightIntensity`
 - `DirLightRotation`

Its reflected layout contains, among other things:

 - `m_bUseFog`
 - `m_bUseSkyDome`
 - `m_bUseSkyLight`
 - `m_pLightFunctionMaterial`
 - `m_fShadowDistance`
 - `m_fShadowBias`
 - `m_bUseSun`
 - `m_fIndoorSunBrightness`
 - `m_fIndoorSkyLightIntensity`
 - `m_fIndoorFogDensity`
 - `m_pMainDirectionalLight`
 - `m_pFog`
 - `m_pSkyLight`

Then the actual native update routine at `0x6F12F84` reads those MPC values and pushes them into the scene. It calls the native light/fog APIs to set:

* directional-light intensity from `SunBrightness`
* directional-light color from `SunTint`
* cast shadows from `LightCastShadow`
* directional rotation from `DirLightRotation`
* fog density
* fog inscattering color
* fog max opacity
* fog height falloff
* fog start distance
* fog altitude
* skylight intensity

This is not an inference from a Blueprint. It is what the ARM64 native code actually does.

Now compare that with OT0: OT0 still has `ALightStage`, with its constructor is at `0x144695610` and its relevant native update is `ALightStage::UpdateParam` (`0x1446A9760`). Its class layout has evolved into a different object. It contains things such as:

 - `m_pMPC`
 - `m_bUseSkyDome`
 - `m_pSkyMaterial`
 - `m_bUseSkyLight`
 - `m_bUpdate`
 - `m_pcCubemap`
 - `m_fOutdoorSkyLightIntensity`
 - `m_fIndoorSkyLightIntensity`
 - `m_isMaterialOverride`
 - `m_zenithColor`
 - `m_zenithColor2`
 - `m_maskOffsetUV`
 - `m_cloudColor`
 - `m_offsetVA`
 - `m_offsetVB`
 - `m_battleWindSpeedBias`
 - `m_cloudSpeed`
 - `m_sunTint`
 - `m_pSkyLight`
 - `m_pSkyMesh`

And `UpdateParam` does something materially different. It resolves indoor state through the event state or `UConditionsComponentBase::CheckIndoor`, chooses indoor/outdoor skylight intensity, reads `Zenith color`, `Cloud color`, `CloudSpeed`, and `SunTint`, creates/updates the skydome dynamic material, writes `ZenithColor`, `ZenithColor 2`, `Mask OffsetU/V`, `CloudColor`, `OffsetV (A/B)`, `BattleWindSpeedBias`, and updates `BoostEnvDarken` depending on battle boost/deathblow state. The CotC routine's direct directional-light/fog application path is simply not what this UE5 function does.

Here is the particularly interesting part: OT0 **still contains and initializes the names** `SunBrightness`, `DirLightRotation`, and `LightCastShadow` as the globals `MPC_SUN_BRIGHTNESS`, `MPC_DIR_LIGHT_ROTATION`, and `MPC_LIGHT_CAST_SHADOW`.

So those really *are* leftovers. But in OT0's full C export each one occurs only in its declaration/initializer machinery; there is no runtime consumer. In contrast, `MPC_SUN_TINT`, `MPC_ZENITH_COLOR`, `MPC_CLOUD_COLOR`, `MPC_CLOUD_SPEED`, and `MPC_BOOST_ENV_DARKEN` all have concrete consumers in `ALightStage::UpdateParam`.

The QPS TimeZone machinery makes the same point even more explicitly. CotC's `UTimeOperator` constructor at `0x72EFFD0` initializes four four-float tables whose decoded values are `9.33/12.67/2.67/6.0`, `10.0/13.3333/3.3334/6.67`, `16.0/19.33/19.33/16.0`, and `16.67/20.0/20.0/16.67`. Runtime paths at `0x72F0738`, `0x72F0F88`, and `0x72F10FC` actually feed the resulting positions into `UMovieSceneSequencePlayer::SetPlaybackPosition`. OT0 has the breadcrumb `TimeZone_PlayerLightIn`; it does **not** have this `UTimeOperator`/`ATimeZoneManager` runtime.

Even a shared class shows the integration disappearing: CotC's reflected `ALightAreaTrigger2` has `InHouse` at `+0x2C4`, `LightTimeZoneFlg` at `+0x396`, and `LightTimeZoneIndex` at `+0x398`. Its `UTimeZoneLightComponent` apply routine at `0x72EF534` resolves the owning `ALightAreaTrigger2` and directly pushes the matched TimeZone parameter actor's intensity/color into its light component. OT0's `ALightAreaTrigger2` still has the huge fog/light/DoF/color-grading curve system, but those CotC TimeZone fields and the `UTimeZoneLightComponent` integration are gone.

To put it simply: **a leftover name is not a leftover feature**. And in OT0, you very often find the combination **leftover name** + **not a leftover feature**. 
