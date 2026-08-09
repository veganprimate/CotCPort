# On Switch Support for the Octopath Modding API
## **LANGUAGE:**  **English** | [日本語](SwitchSupport_jp.md) | [中文](SwitchSupport_zh.md) | [한국어](SwitchSupport_kr.md)

# OT0 Kingship Modding API — Nintendo Switch Support Architecture and Porting Plan

**Status:** Engineering design / feasibility plan ; the completion of the PC Modding API is prioritized, will likely not also include OT1 and OT2 support
**Current Plan:** ship the first Switch version as **one injected `subsdkN` NSO containing the Kingship runtime plus a compile-time set of built-in mods**, but make those built-in mods enter through the same **stable v3 host/mod ABI** that a later dynamically loaded NRO would use. 
**The reason for this document:** This is not only to explain the relevant architectural questions, but also to lay out my plan in detail in case I later on give up on the Switch support and someone else is willing to develop the support themselves. It is like a guide for adding Switch Support.

---

## Table of contents

1. [Executive conclusion](#1-executive-conclusion)
2. [What was inspected](#2-what-was-inspected)
3. [The architecture I believe is best](#3-the-architecture-i-recommend)
4. [Why a single injected subsdk NSO is the right Phase 1](#4-why-a-single-injected-subsdk-nso-is-the-right-phase-1)
5. [Why dynamic NRO plugins are nevertheless viable later](#5-why-dynamic-nro-plugins-are-nevertheless-viable-later)
6. [What in the current KingshipAPI runtime is portable vs Windows-specific](#6-what-in-the-current-kingship-runtime-is-portable-vs-windows-specific)
7. [Build-system restructuring](#7-build-system-restructuring)
8. [Switch bootstrap and injected-module lifecycle](#8-switch-bootstrap-and-injected-module-lifecycle)
9. [Module image, build identity, ASLR, and target resolution](#9-module-image-build-identity-aslr-and-target-resolution)
10. [AArch64 hooking and code patching](#10-aarch64-hooking-and-code-patching)
11. [Unreal Engine ABI and data-layout hazards](#11-unreal-engine-abi-and-data-layout-hazards)
12. [Stable ABI v3 and built-in mod registration](#12-stable-abi-v3-and-built-in-mod-registration)
13. [Inter-mod dependencies and service discovery](#13-inter-mod-dependencies-and-service-discovery)
14. [Platform services that need Switch implementations](#14-platform-services-that-need-switch-implementations)
15. [Mod-by-mod migration strategy](#15-mod-by-mod-migration-strategy)

---

# 1. Executive conclusion

I would choose for the first usable Switch port:

> **Inject one Kingship host as an unused `subsdkN` NSO, put the core runtime/modloader in that NSO, and statically link an explicitly selected set of mods into the same image.**

There are, however, two important refinements.

First, **I will not statically link the current DLL-oriented entrypoints unchanged** and then invent fake equivalents of `LoadLibrary`, `GetProcAddress`, and Windows module handles. Instead, create a proper **built-in module provider**. Each compiled-in mod should expose a unique internal factory/query function, and the host should feed that function into the existing v3 mod lifecycle. From the host's perspective, "built-in module" and "future NRO module" should differ only in how the host obtains an `KingshipAPI_ModQueryV3Fn` (but again, the support will likely be constrained to just OT0).

Second, I will not regard the monolithic NSO as an architectural dead end. **[Skyline](https://github.com/skyline-dev/skyline) proves that application-hosted NRO loading is practical on Switch.** Its plugin manager initializes `nn::ro`, scans a plugin directory, computes plugin hashes, constructs/registers an NRR, loads page-aligned NRO images/BSS with `nn::ro::LoadModule`, resolves an entrypoint with `nn::ro::LookupModuleSymbol`, and calls it. In other words, a PC-like "one core runtime + separately distributed native plugins" architecture is possible. It simply adds enough loader, permission, relocation, symbol-resolution, lifecycle, and failure-isolation work that it should not be the first problem solved.

The port therefore should be deliberately **split into two loader backends**:

```text
                KingshipModLoader Host / Runtime
                                |
                       stable host ABI v3
                                |
                  +-------------+-------------+
                  |                           |
          BuiltinModuleProvider       NroModuleProvider
             Phase 1                  Phase 4/optional
                  |                           |
          linker registration         nn::ro / ldr:ro +
             table/factories         symbol lookup/rtld
                  |                           |
              mod API v3                  mod API v3
```

The largest engineering costs are not actually the module loader. They are:

1. **Replacing Win32/x86-64 platform assumptions in the runtime**
2. **Building an AArch64 hook/patch backend**
3. **Producing a Switch-specific target/build database** The current evidence has roughly 963 distinct Win64 RVAs referenced across the mod set; those addresses and x86 instruction signatures do not translate to the Switch binary
4. **Re-validating Unreal Engine/native ABI assumptions** Reflection-level concepts may transfer; compiler ABI, native function addresses, generated thunks, string-width assumptions, prologs, and mid-function register contracts do not.
5. **Migrating legacy mods away from DLL/export-based inter-mod discovery** 

A realistic first target is therefore **not** “all current mods on Switch.” It is:

```text
subsdkN loads
    -> Kingship logger works
    -> Switch module/base/build fingerprint works
    -> target database recognizes exactly one supported Switch build
    -> AArch64 HookBackend can hook one known function safely
    -> host ABI v3 initializes
    -> CAbiV3ExampleMod runs as a built-in module
    -> one small real gameplay mod runs
    -> game-thread dispatch works
    -> then expand the supported target/mod set incrementally
```

---

# 2. What was inspected

The current Modding API root contains a substantial native runtime and a large body of recovered/derived game knowledge. The relevant portions I inspected include:

- `runtime/loader/ModLoaderMain.cpp`
- `api/src/ModLoader.cpp`
- `api/include/KingshipAPI/ModABIV3.h`
- `api/include/KingshipAPI/CallingConvention.h`
- `api/include/KingshipAPI/HookBackend.hpp`
- `api/include/KingshipAPI/HookBroker.hpp`
- `api/include/KingshipAPI/BuildManifest.hpp`
- `api/src/BuildGuard.cpp`
- `api/src/ModuleImage.cpp`
- `api/include/KingshipModdingAPI/PatternScanner.hpp`
- the runtime CMake graph
- the current runtime mods under `runtime/mods/`
- the checked-in target-usage/evidence material under `analysis/`
- the supplied UE5.4.2/OT0 generated SDK
- supplied Win64 C/ASM decompiler outputs
- supplied IDA local types


## 2.1 Current loader shape

The current runtime is fundamentally a Windows DLL host:

```text
Windows process
  -> DllMain
     -> bootstrap thread
        -> create ModLoader
           -> enumerate KingshipMods/*.dll
              -> LoadLibraryExW
                 -> GetProcAddress(query/create entry)
                    -> initialize mod
     -> loader scheduler thread
```

`runtime/loader/ModLoaderMain.cpp` uses Windows concepts including `DllMain`, `HANDLE`, `DWORD`, `SRWLOCK`, `CreateThread`, `GetModuleHandleW`, process-image-name validation, module pinning, and `__declspec(dllexport)` service exports.

`api/src/ModLoader.cpp` is a Win32 implementation: the loader is guarded by `_WIN32` and uses Windows module loading, export lookup, PE/module inspection, Unicode conversion, and DLL filesystem discovery.

This is tells us exactly where the platform boundary currently is too porous.

## 2.2 The stable C ABI is a major advantage

`ModABIV3.h` is much more portable than the Windows loader around it. It uses fixed-width integers, explicit `struct_size`/`abi_version` fields, opaque `void*` contexts, function pointers, string views, capabilities, service lookup, and lifecycle callbacks.

`CallingConvention.h` also already makes a good architectural distinction:

- on Windows, the stable ABI is explicitly `__cdecl`;
- elsewhere, `OT0K_ABI_CALL` is the platform C ABI;
- `OT0K_GAME_CALL` is similarly native on non-Windows platforms;
- specialized vector/preserve-none/MASM contracts are kept separate from the stable ABI.

That means the **C ABI should become the canonical cross-platform mod boundary**. It should not be replaced for Switch. The Switch binary is based on v1.0.4.0 however… this **may change with the upcoming OT0 update**.

## 2.3 Existing `IHookBackend` is another good seam

`HookBackend.hpp` already expresses hooking as an interface with capabilities and requests rather than forcing callers to know PolyHook2. The concepts include:

- inline detours;
- instruction relocation;
- near/far trampolines;
- re-hooking;
- mid-function support;
- verified patch points;
- hook integrity/auditing;
- ownership and owner IDs.

That is exactly the abstraction we need. The Switch work should primarily add a new implementation such as:

```cpp
class SwitchAArch64HookBackend final : public IHookBackend {
    ...
};
```

rather than changing every mod to call an AArch64 hook library directly.

## 2.4 Current build identity is explicitly Win64

The active `BuildManifest.hpp` records Win64-specific facts such as:

- `Octopath_Traveler0-Win64-Shipping.exe`;
- Win64 Shipping platform identity;
- a Win64 executable hash;
- an IDA image base near `0x140000000`;
- concrete Win64 RVAs for `GObjects`, `GNames`, `GWorld`, `FName::Append`, `ProcessEvent`, etc.

`BuildGuard.cpp` also contains x86-64-oriented validation logic/prolog assumptions.

This should become **a family of build manifests**, not a single active Windows manifest with `#ifdef`s inserted into every field.

## 2.5 Current mod set is heterogeneous

The loader migration is not the same difficulty for every mod. Some mods already expose v3; many **are still legacy C++ factory modules**. The following are C v3 modules:

- `CAbiV3ExampleMod`
- `CotCPort_BattleUI`
- `CotCPort_Conditions`
- `CotCPort_Events`
- `CotCPort_MapRuntime`
- `CotCPort_WorldObjects`
- `MonsterArenaFixMod`
- `OT0Kingship_LuaHost`

Most other gameplay/debug modules still use the legacy `kingship_mod_create` lane.

A number of mods also call Win32 directly for one or more of:

- `GetProcAddress`
- `GetModuleHandle*`
- `LoadLibrary*`
- `VirtualProtect`
- `VirtualAlloc` / `VirtualFree`
- `FlushInstructionCache`
- `GetAsyncKeyState`
- `CreateThread`
- `OutputDebugString`
- `MessageBox`
- `GetModuleFileName`
- `WideCharToMultiByte` / `MultiByteToWideChar`
- thread/process IDs and performance counters

So static linking alone does not make those mods portable. The platform services underneath them must be separated (this primarily affects `CorruptDataProbeMod`).

---

# 3. The architecture I believe is best

The target architecture should be a three-layer design.

## 3.1 Layer A — Switch injection/bootstrap

This is the smallest platform-specific executable layer. Its responsibilities are:

- be loaded as the selected `subsdkN` NSO;
- establish minimal runtime prerequisites;
- discover the main game module and its address range;
- initialize logging early enough to debug failed startup;
- fingerprint the supported game build;
- initialize the AArch64 hook/memory backend;
- invoke the platform-neutral Kingship host startup;
- avoid doing unsafe UE work before the engine is ready.

Conceptually:

```text
subsdkN entry
  -> SwitchPlatform::early_init()
  -> SwitchModuleEnumerator::find_main()
  -> SwitchBuildGuard::identify(main)
  -> SwitchMemoryBackend::init()
  -> SwitchAArch64HookBackend::init()
  -> KingshipHost::construct(...platform services...)
  -> BuiltinModuleProvider::enumerate()
  -> arm deferred game-thread bootstrap
```

This layer replaces `DllMain` and the Win32 bootstrap thread. It should **not** become a second giant loader implementation.

## 3.2 Layer B — platform-neutral Kingship host/runtime

This is the code worth preserving across PC and Switch:

- stable ABI v3 host table;
- service registry;
- mod lifecycle;
- hook broker and ownership;
- target/evidence registry interfaces;
- update dispatch;
- save-state coordination where portable;
- reflection/domain catalogs where platform-neutral;
- shutdown/quiescence state machine;
- logging API (not the sink implementation);
- dependency resolution.

This layer should never call `GetModuleHandle`, `VirtualProtect`, `GetAsyncKeyState`, etc. It asks platform services instead.

## 3.3 Layer C — module providers

A module provider answers a simple question:

> Which mods are available, and how do I obtain their `KingshipAPI_ModQueryV3Fn`?

There will be at least these providers conceptually:

```cpp
class IModProvider {
public:
    virtual ~IModProvider() = default;
    virtual void enumerate(std::vector<DiscoveredMod>& out) = 0;
};

class Win32DllModProvider final : public IModProvider { ... };
class BuiltinModProvider final : public IModProvider { ... };
class SwitchNroModProvider final : public IModProvider { ... }; // later
```

The host then does the same ABI negotiation, capability checks, service access, lifecycle registration, owner assignment, and update scheduling regardless of source.

That separation is the single most important architectural decision in this port.

---

# 4. Why a single injected subsdk NSO is the right Phase 1

A single NSO solves the right early problems and avoids solving the wrong ones.

## 4.1 It gets code into the title through a proven model

The current exlaunch framework is explicitly a C/C++ injection framework for Switch applications/applets/sysmodules. In its `Module` configuration, its build scripts currently emit `subsdk9` and deploy it under:

```text
atmosphere/contents/<PROGRAM_ID>/exefs/
```

Its sample application entry calls `exl::hook::Initialize()` and can install hooks at a function pointer, absolute address, or offset into the main module.

That is almost exactly the bootstrap shape Kingship needs.

We will **not** have to permanently depend on exlaunch. It is a useful reference implementation and a good bring-up vehicle. Once KingshipAPI's platform interfaces are stable, I will decide whether to:

1. keep exlaunch as the Switch injection/hook substrate;
2. vendor/fork only the pieces you need;
3. replace it with a Kingship-specific NSO build/link/bootstrap implementation.

For Phase 1, minimizing custom loader work is valuable.

## 4.2 It avoids relocatable-plugin permissions and registration during bring-up

If every gameplay mod is already inside the injected NSO:

- no NRO directory scan is required;
- no NRR construction/registration is required;
- no `ldr:ro`/`nn::ro` plugin-loading path is required;
- no plugin symbol lookup is required;
- no unload/unmap machinery is required;
- no plugin-specific relocation failures occur;
- no plugin C++ runtime duplication has to be debugged yet;
- fewer NPDM/service-permission assumptions are introduced.

That significantly reduces the number of simultaneous unknowns.

## 4.3 It makes debugging AArch64/UE problems much easier

At the beginning, most failures will be things like:

- wrong main-module base;
- unsupported game build;
- wrong AArch64 target;
- calling a UE function before initialization;
- incorrect AAPCS64 signature;
- malformed trampoline;
- instruction relocation bug;
- wrong cache maintenance;
- incorrect UObject/FName/FString layout assumption;
- thread-affinity violation.

If the code is one symbolized ELF/NSO image, those failures are much easier to reason about than if each mod is its own relocated NRO.

## 4.4 It lets you put a hard allowlist on mods

I won't link every current mod in the first build. I'll add compile-time options, e.g.:

```cmake
option(OT0K_SWITCH_MOD_CABI_EXAMPLE "Build C ABI example" ON)
option(OT0K_SWITCH_MOD_BATTLE_UI "Build Battle UI" OFF)
option(OT0K_SWITCH_MOD_CONDITIONS "Build Conditions" OFF)
option(OT0K_SWITCH_MOD_CRAFTING "Build Crafting" OFF)
...
```

Then bring mods over by dependency tier. A single broken native hook should not hold up the entire Switch runtime.

## 4.5 The important caveat: choose `subsdkN` after inspecting OT0's ExeFS

One shuld not hardcode "`subsdk9` is always correct" into the KingshipAPI. exlaunch uses `subsdk9` today, but the packaging logic should inspect the title's ExeFS and choose/require a known-free module slot for the supported game build.

For OT0, the deployment manifest should record something like:

```yaml
switch:
  title_id: "<actual-title-id>"
  version: "<actual-version>"
  injected_module_name: "subsdk9"   # only after ExeFS validation
  expected_original_modules:
    - rtld
    - main
    - sdk
    - "<existing subsdk entries, if any>"
```

Fail packaging if the selected name collides with an original module.

---

# 5. Why dynamic NRO plugins are nevertheless viable later

[Skyline](https://github.com/skyline-dev/skyline)'s implementation demonstrates the complete shape:

1. initialize `nn::ro`;
2. walk `romfs:/skyline/plugins`;
3. read each plugin image;
4. ask `nn::ro::GetBufferSize` for required BSS;
5. calculate SHA-256 hashes;
6. build an NRR containing the allowed plugin hashes;
7. register that NRR with `nn::ro::RegisterModuleInfo`;
8. allocate page-aligned BSS;
9. call `nn::ro::LoadModule(..., BindFlag_Now)`;
10. resolve a symbol using `nn::ro::LookupModuleSymbol`;
11. call the resolved plugin entrypoint.

libnx also exposes the lower-level relocatable-object service wrappers (`ldrRoInitialize`, `ldrRoLoadNro`, `ldrRoLoadNrr`, unload functions, and `ro:1` equivalents).

So a future KingshipAPI architecture can absolutely be:

```text
Atmosphère LayeredFS
└─ exefs/subsdkN              <- Kingship host
└─ romfs/ot0k/plugins/*.nro   <- native mods

KingshipAPI host
  -> scan plugins
  -> authorize/register hashes
  -> load NRO
  -> resolve kingship_mod_query_v3
  -> negotiate ABI v3
  -> register resources under owner id
  -> on_load
```

The complication is not whether it can be done but rather **doing it without weakening the current lifecycle guarantees**

A dynamic loader must additionally answer:

- How is the v3 query symbol found?
- Which symbols may the NRO import from the host/game?
- Does the game NPDM permit the necessary `ro` service access?
- How are NRRs generated/registered for arbitrary user plugins?
- What memory alignment and lifetime rules apply?
- What happens if relocation fails halfway through?
- How do you prevent a plugin from unloading while one of its detours/callbacks is executing?
- How do you unwind owner-scoped hooks/services before `UnloadNro`?
- How do you prevent C++ objects allocated under one runtime/allocator from being freed through another?
- What is the policy for plugin dependencies and load order?

We already have useful machinery for the hardest of those: v3 `prepare_stop`, `finalize`, owner-scoped resources, and host service tables. That makes an NRO phase more attractive for the KingshipAPI than it would be for a loader designed around raw `main()` callbacks.

---

# 6. What in the current KingshipAPI runtime is portable vs Windows-specific

The cleanest porting strategy is to classify code rather than scatter `#ifdef __SWITCH__` everywhere.

## 6.1 Mostly portable / preserve

These areas should remain conceptually intact:

| Area | Keep? | Notes |
|---|---:|---|
| Stable C ABI v3 structures | Yes | Excellent cross-module boundary. |
| `query_service` model | Yes | Should replace DLL export lookup in mods. |
| `HookBroker` ownership model | Yes | Back it with AArch64 backend. |
| Hook target semantic/evidence records | Yes | Addresses/evidence become per-platform. |
| Mod lifecycle state machine | Yes | Useful even for static mods. |
| Owner IDs/resource revocation | Yes | Essential for eventual NRO unload. |
| High-level domain/reflection catalogs | Mostly | Validate platform-sensitive layout fields. |
| Save/streaming coordinators | Mostly | Replace OS primitives where embedded. |
| Logging API | Yes | Replace sinks only. |
| C ABI strings as byte/string views | Yes | Prefer UTF-8 at host boundary. |

## 6.2 Replace with platform interfaces

| Current concept | Windows implementation | Switch implementation |
|---|---|---|
| module discovery | PE/process APIs | NSO/module enumeration or bootstrap-provided main range |
| loader | DLL enumeration + `LoadLibraryExW` | built-in provider first; NRO provider later |
| symbol lookup | `GetProcAddress` | registration table first; rtld/`nn::ro` symbol path later |
| loader entry | `DllMain` | NSO entry / `exl_main`-style bootstrap |
| executable identity | EXE name/hash | title/version + main-module fingerprint |
| page permission | `VirtualProtect` | Switch code-memory/permission backend or hook framework |
| executable allocation | `VirtualAlloc` | JIT/code memory/trampoline allocator |
| icache sync | `FlushInstructionCache` | AArch64/Switch cache maintenance |
| keyboard state | `GetAsyncKeyState` | HID/game input service |
| debug output | `OutputDebugString` | file/socket/USB/TCP/logger backend |
| path discovery | `GetModuleFileName` | explicit title/runtime path service |
| threads | Win32 thread handles | Switch/libnx or portable C++ wrapper |
| timing | QPC/GetTickCount | monotonic Switch timer wrapper |
| UTF conversion | Win32 W/A conversion | UTF-8 centric conversion layer |

## 6.3 Architecture-scope x64-only concepts

The repository contains generated x64 production gateways and x64-special ABI lanes. These cannot be "ported" by recompiling.

These should be treated as architecture-specific implementations:

```text
api/runtime gateway concept
  ├─ arch/x86_64/windows/... existing generated gateway
  └─ arch/aarch64/switch/... new gateway, only where actually required
```

I won't create AArch64 gateway equivalents preemptively and instead first classify every use:

- ordinary function-entry hook -> normal AArch64 detour;
- reflected exec thunk -> normal native call/hook if signature is known;
- mid-function instrumentation -> requires explicit AArch64 machine-context contract;
- patchpoint -> architecture-specific expected instructions and patch bytes;
- x64 register-preservation trick -> redesign for AAPCS64.

A surprising amount of complexity can often be deleted if a Windows mid-function hook can be replaced on Switch by a nearby whole-function entry hook identified during re-analysis.

---

# 7. Build-system restructuring

The current runtime CMake logic is Windows-first and applies definitions such as `WIN32`, `_WIN32`, `NOMINMAX`, and `WIN32_LEAN_AND_MEAN` broadly. It also builds PolyHook2/x64 gateway material as part of the normal runtime graph.

I won't try to make that graph universal through dozens of conditional source removals and instead aplit platform targets explicitly.

## 7.1 Proposed top-level shape

```text
CMakeLists.txt
cmake/
  PlatformWindows.cmake
  PlatformSwitch.cmake
  toolchains/
    devkitA64.cmake

api/
  include/KingshipAPI/...           # portable interfaces
  src/common/...
  src/platform/windows/...
  src/platform/switch/...

runtime/
  host/common/...
  host/windows/...
  host/switch/...
  loader/windows/...
  loader/switch/...
  mods/...
  arch/x86_64/...
  arch/aarch64/...
```

## 7.2 Possible targets

```text
ot0kingship_core                  STATIC
  platform-neutral host, service registry, lifecycle, hook broker

ot0kingship_platform_windows      STATIC
  module image, Win32 paths, memory, logging, timing, etc.

ot0kingship_platform_switch       STATIC
  main module, Switch paths, memory, logging, timing, etc.

ot0kingship_hook_x64_polyhook     STATIC

ot0kingship_hook_aarch64_switch   STATIC

ot0kingship_mod_<name>            OBJECT or STATIC

ot0kingship_loader_win64          SHARED (.dll)

ot0kingship_switch_payload        ELF -> NSO (`subsdkN`)
```

## 7.3 Use OBJECT libraries for built-in mods where possible

For Switch:

```cmake
add_library(ot0k_mod_conditions OBJECT
    mods/CotCPort_Conditions/...
)

target_compile_definitions(ot0k_mod_conditions PRIVATE
    OT0K_BUILTIN_MOD=1
    OT0K_PLATFORM_SWITCH=1
)

target_sources(ot0kingship_switch_payload PRIVATE
    $<TARGET_OBJECTS:ot0k_mod_conditions>
)
```

This has several benefits:

- no DLL export machinery;
- no accidental separate copies of the common runtime support library;
- straightforward dead stripping;
- explicit feature selection;
- easier symbolization in a single ELF;
- unique internal factory symbols can be enforced at compile time.

## 7.4 Link shared runtime support once

Today a static support target can be linked into each Windows DLL because those DLLs are separate images. In a monolithic Switch image, prefer one host/runtime-support instance. In particular, avoid creating duplicated “singleton” state because the same support archive happened to be linked privately into every built-in mod target.

A good rule:

> **Mods may own mod-local static state; services, target registries, hook ownership, logging, and module-image state live once in the host.**

## 7.5 Toolchain strategy

There are two practical approaches.

### Approach A — start from exlaunch build rules

Use exlaunch's existing devkitA64/module linker pipeline for the first injected payload and compile/link Kingship libraries into it. This gets to "code runs in OT0" fastest.

### Approach B — own the NSO pipeline immediately

Create a devkitA64 CMake toolchain and a post-link ELF-to-NSO stage. This gives a cleaner long-term build but makes bootstrap/linker work part of the first milestone.

I will likely do the following: **A for bring-up**, then migrate to **B** once the runtime is stable.

---

# 8. Switch bootstrap and injected-module lifecycle

The Windows bootstrap currently depends on `DllMain` plus an extra thread. The Switch version needs a lifecycle designed around an injected NSO.

## 8.1 Keep the injected entrypoint tiny

A robust entry should do roughly:

```cpp
extern "C" void exl_main(void* x0, void* x1) {
    SwitchEarlyLog::initialize();
    SwitchPlatform::initialize_minimal();

    const auto modules = SwitchModuleImage::enumerate();
    const auto main = modules.main;
    if (!main.valid()) {
        log_fatal("main module not found");
        return;
    }

    const auto build = SwitchBuildGuard::identify(main);
    if (!build.supported()) {
        log_fatal("unsupported OT0 build");
        return;
    }

    g_runtime.emplace(main, build, ...);
    g_runtime->initialize_non_ue_services();
    g_runtime->arm_engine_ready_hook();
}
```

The important property is that `exl_main` does **not** immediately traverse `GObjects`, touch `GWorld`, construct engine objects, or run arbitrary mod `on_load` callbacks that expect a live world.

## 8.2 Separate "host loaded" from "engine ready"

Define explicit startup stages:

```cpp
enum class RuntimeStage {
    Injected,
    PlatformReady,
    BuildValidated,
    HookBackendReady,
    EngineCoreReady,
    GameThreadReady,
    WorldReady,
    ModsLoaded,
    Running,
};
```

A mod descriptor can eventually declare a minimum stage or the host can defer specific services until available.

This solves a problem the Windows DLL model can hide: being loaded into the process is not the same thing as UE being ready for arbitrary native interaction.

## 8.3 Establish a reliable game-thread dispatch point

I already distinguish the loader scheduler from the UE game thread, this has to be preserved.

For Switch, we'll have to find one stable game-thread lifecycle target and make it a host-owned hook. Candidates might include an engine/game-instance/tick path, but the exact target should be chosen from the Switch binary rather than copied from Win64.

The host-owned hook should:

1. record the game-thread identity;
2. advance engine/world readiness;
3. drain a host game-thread task queue;
4. invoke mod updates that require game-thread execution;
5. dispatch streaming/world transitions as appropriate.

## 8.4 Avoid static C++ constructors for mod registration

It is tempting to make each built-in mod register itself through a global constructor. This is a no-go for an injected console module.

Instead we'll use an explicit generated/static table:

```cpp
extern OT0K_StatusCode OT0K_ABI_CALL
ot0k_builtin_query_conditions(...);
extern OT0K_StatusCode OT0K_ABI_CALL
ot0k_builtin_query_battle_ui(...);

static constexpr BuiltinModRegistration kBuiltinMods[] = {
    {"cotc.conditions", &ot0k_builtin_query_conditions, 20},
    {"cotc.battle_ui",  &ot0k_builtin_query_battle_ui, 40},
};
```

This produces deterministic initialization order and avoids loader-time constructor surprises.

---

# 9. Module image, build identity, ASLR, and target resolution

This is one of the largest tasks in the port.

## 9.1 The Win64 RVAs are semantic clues, not Switch addresses

The checked-in target-usage evidence references approximately **963 distinct Win64 RVAs** across the current mods. The largest debug modules alone reference hundreds.

That means a Switch port needs its own target set. This will take a lot of manual RE work.

A formula such as:

```text
switch_rva = windows_rva + delta
```

will **NOT** work. Cross-compiler, cross-architecture builds rearrange functions, merge/split code, inline differently, emit different thunks, and use completely different instruction encodings.

## 9.2 Preserve semantic target keys

The thing worth keeping is the name/identity of a target.

For example, conceptually:

```text
TargetKey::ProcessEvent
TargetKey::GWorld
TargetKey::FNameAppend
TargetKey::KSWhatever_DoThing
```

should resolve through a build database:

```cpp
TargetAddress resolve(TargetKey key, BuildId build);
```

where the data can be:

```text
builds/
  win64/<build-id>/targets.json
  switch/<build-id>/targets.json
```

A mod then asks for a target by semantic key and **never embeds a platform RVA directly**.

## 9.3 Introduce a Switch build manifest

Do not overload the current active Win64 constants. Use something like:

```cpp
struct GameBuildManifest {
    Platform platform;
    Architecture architecture;
    std::string_view game;
    std::string_view app_version;
    BuildFingerprint fingerprint;
    std::span<const TargetRecord> targets;
};
```

For Switch, the fingerprint should be based on data you can derive reliably from the dumped/runtime `main` module, for example:

- application/title version;
- title/program ID;
- main NSO module/build identifier if exposed/reliable in your tooling;
- hash of a normalized or known read-only text region;
- main text size and/or selected immutable anchors.

The exact fingerprint can be finalized once the OT0 Switch ExeFS is available.

## 9.4 Build guard must understand AArch64

The current build guard's x86 instruction/prolog checks should become architecture-specific validators.

For AArch64, validation could include:

- address belongs to main executable text;
- address is 4-byte aligned;
- expected 32-bit instruction words/masks match;
- nearby literal/string/reference anchors match;
- target's known callers/callees can be validated where useful;
- reflected/native metadata agrees with the expected function identity.

Represent expected instructions as masked words rather than x86 byte strings when appropriate:

```cpp
struct A64InstructionPattern {
    uint32_t value;
    uint32_t mask;
};
```

Example concept:

```cpp
(value & mask) == (expected & mask)
```

This handles immediate/register fields intentionally rather than wildcarding random bytes.

## 9.5 Pattern scanner needs an architecture-neutral backend

The current pattern scanner advertises scalar, SSE2, and AVX2 backends. On Switch:

- always retain the scalar implementation;
- optionally add NEON acceleration later;
- never let the lack of SSE2/AVX2 block correctness.

The initial Switch build should simply select scalar scanning. Target discovery is a startup operation; correctness matters more than a micro-optimized scanner during bring-up.

---

# 10. AArch64 hooking and code patching

This is the other major core-runtime task.

## 10.1 Keep `HookBroker`; replace its physical backend

The mod-facing API should continue to ask for hooks through the broker. The Switch backend handles the machine code.

Proposed implementation:

```cpp
class SwitchAArch64HookBackend final : public IHookBackend {
public:
    HookBackendCapabilities capabilities() const noexcept override;
    HookBackendProbe probe_inline_hook(const HookRequest&) const noexcept override;
    HookHandle install_inline_hook(const HookRequest&) override;
    bool remove_hook(const HookHandle&) override;
    HookIntegrityReport audit_hook(const HookHandle&) const noexcept override;
};
```

Early capabilities should be conservative:

```text
InlineDetour            yes
InstructionRelocation   only after proven
FarTrampoline           if backend supports it
NearTrampoline          if allocator supports it
MidFunction             no initially
VerifiedPatchpoint      yes for explicit 4-byte instruction patches
ThreadQuiescence        no initially, unless implemented
```

## 10.2 Function-entry hooks first

AArch64 has fixed-width 4-byte instructions, but inline hooking still needs care:

- enough original instructions must be displaced to install a branch sequence;
- displaced PC-relative instructions may need relocation;
- direct branches have finite range;
- `ADR`/`ADRP`, literal loads, conditional branches, and other PC-relative operations require correct relocation;
- trampoline memory must be executable;
- data/instruction caches must be synchronized after writes.

I'll use a proven AArch64 hook implementation as the initial backend (exlaunch's hook environment is an obvious reference).

## 10.3 Mid-function hooks are a separate feature

The existing runtime has x64-specific generated gateways and mods with x64/gateway assumptions. Mid-function hooks are harder because the callback contract depends on exact machine state.

For every such hook, I will have to document:

```text
Target: semantic name
Location: function + Switch RVA
Purpose: what state is being observed/modified
Live input registers: x0..xN / vN / flags as applicable
Original instruction(s) replaced
Stack state/alignment
Continuation address
Registers clobbered by injected code
Whether original instruction semantics are replayed
```

## 10.4 Code memory / trampolines

On Switch you cannot assume `VirtualAlloc(PAGE_EXECUTE_READWRITE)` semantics. libnx's JIT interface explicitly models executable code memory with writable/executable transitions and RW/RX aliases.

I'll have to create a runtime abstraction:

```cpp
class IExecutableAllocator {
public:
    virtual ExecutableBlock allocate(std::size_t size,
                                     AddressHint hint = {}) = 0;
    virtual bool make_writable(ExecutableBlock&) = 0;
    virtual bool make_executable(ExecutableBlock&) = 0;
    virtual void flush_code(ExecutableBlock&, Range changed) = 0;
};
```

The hook backend should use this rather than calling platform primitives directly.

## 10.5 Direct patching

For mods that currently use `VirtualProtect + memcpy + FlushInstructionCache`, create a host patch service:

```cpp
PatchHandle patch_code(
    OwnerId owner,
    uintptr_t address,
    span<const byte> expected,
    span<const byte> replacement);
```

On Switch, replacements should generally be expressed as AArch64 instruction words/bytes specific to that build.

The service should:

- validate build/target;
- verify expected original instructions;
- acquire the code mutation coordinator;
- make/update writable alias as required;
- write atomically where possible;
- synchronize caches;
- retain original bytes/instructions;
- restore on owner revocation when safe.

That keeps `VirtualProtect`-style logic out of mods.

---

# 11. Unreal Engine ABI and data-layout hazards

## 11.1 Reflection identity may transfer; native ABI does not automatically transfer

Potentially reusable semantic information includes:

- UObject/class names;
- reflected function names;
- property names;
- enum values;
- high-level inheritance;
- gameplay meaning of fields/functions;
- data-table/resource names;
- many serialized-content identifiers.

Must be independently verified on Switch:

- absolute/RVA addresses;
- vtable layout/index assumptions;
- native thunks;
- structure size/alignment when non-reflected/native;
- compiler-added padding;
- inline helper behavior;
- return-by-value ABI;
- aggregate/HFA/HVA passing;
- exception/RTTI details if used;
- native mangled symbols;
- function prologs/epilogs;
- mid-function register contracts.

An ARM port of Dumper-7 could be useful here, but in itself a large-scale effort.

## 11.2 Windows x64 ABI vs AAPCS64

A native function typedef that happened to be correct on Windows may still be wrong after a simple recompile because aggregate passing/return rules differ.

For every important native hook/call, record an architecture-specific signature validation status:

```cpp
enum class NativeAbiValidation {
    Unknown,
    ReflectionOnly,
    Decompiled,
    CallsiteValidated,
    RuntimeValidated,
};
```

Require a higher level before invoking high-risk helper functions on Switch.

## 11.3 `wchar_t` / `TCHAR` is a major portability trap

Any code that implicitly treats C++ `wchar_t` as Unreal's character type deserves an audit.

Windows code often gets away with assumptions about `wchar_t` width that are not portable to an AArch64 GNU/Clang environment. Meanwhile Unreal's `TCHAR` representation is a platform configuration choice. Therefore:

- do not expose `wchar_t*` across the Kingship stable ABI;
- keep stable ABI text UTF-8;
- represent UE text with an explicit Kingship `UEChar`/`char16_t`-like type **only after validating the Switch UE build**;
- add `static_assert(sizeof(...))` for every native string struct you use;
- never reinterpret a `std::wstring` buffer as an `FString` payload.

Audit helpers around `FString`, FName rendering, path conversion, and Win32 wide/narrow conversions early.

## 11.4 FName/GNames

Do not assume the Win64 `GNames`/FName helper location or decoding implementation is identical.

The correct Switch process is:

1. find the Switch FName pool/global mechanism;
2. validate the raw FName representation;
3. locate or reconstruct an appropriate name-to-string path;
4. test on known class/object names;
5. only then enable generic object-name diagnostics.

A robust object-name resolver is one of the most useful bring-up tools because it lets you verify that object registry traversal is genuinely correct.

## 11.5 GUObjectArray / GObjects

Similarly, locate the Switch object array independently. Validate:

- chunk layout;
- object item stride;
- serial/index semantics used by your runtime;
- pointer alignment;
- known objects/classes found at runtime;
- garbage collection behavior around cached pointers.

Do not enable broad runtime object scanning until the build guard proves the registry layout.

## 11.6 ProcessEvent

`ProcessEvent` is attractive because it can unlock high-level generic interaction. But both its address and the safest hook strategy need Switch validation.

If the vtable index differs, a hardcoded Windows index is unsafe. Prefer resolving the function semantically and validating the vtable/function relationship for the supported build.

---

# 12. Stable ABI v3 and built-in mod registration

The v3 ABI is the key to making the static Phase 1 and dynamic Phase 4 converge.

## 12.1 The host should always speak v3 internally

Even when a mod is built into the same NSO, initialize it through:

```cpp
KingshipAPI_ModQueryV3Fn
```

and the same `OT0K_HostApiV3` structure.

Benefits:

- capability negotiation remains real;
- service lookup remains real;
- per-mod owner/resource tracking remains real;
- the same mod can later compile as an NRO;
- the same source can compile as a Windows DLL;
- platform-specific host services remain behind the ABI;
- test coverage applies to all providers.

## 12.2 Avoid duplicate `kingship_mod_query_v3` symbols when statically linking

Every dynamically built v3 mod can export the same conventional symbol name because each is a separate module. A monolithic NSO cannot.

Refactor each v3 mod so its implementation has a unique internal query function, and the export is just an adapter.

Example:

```cpp
OT0K_StatusCode OT0K_ABI_CALL
query_mod_conditions_v3(
    uint32_t requested,
    const OT0K_HostApiV3* host,
    OT0K_ModApiV3* out)
{
    ...
}

#if defined(OT0K_DYNAMIC_MOD)
extern "C" OT0K_ABI_EXPORT OT0K_StatusCode OT0K_ABI_CALL
kingship_mod_query_v3(
    uint32_t requested,
    const OT0K_HostApiV3* host,
    OT0K_ModApiV3* out)
{
    return query_mod_conditions_v3(requested, host, out);
}
#endif
```

Then the built-in table references `query_mod_conditions_v3` directly.

## 12.3 Add a built-in registration record

Something like:

```cpp
struct BuiltinModRegistration {
    const char* identifier;
    KingshipAPI_ModQueryV3Fn query;
    uint32_t load_order_hint;
    bool enabled_by_default;
};
```

The runtime can then reuse normal discovery code:

```cpp
for (const auto& reg : builtin_provider.mods()) {
    load_v3_mod({
        .origin = ModOrigin::Builtin,
        .display_path = "builtin:" + std::string(reg.identifier),
        .query = reg.query,
    });
}
```

## 12.4 Static does not mean unloadable code, but lifecycle still matters

For a built-in mod, `prepare_stop/finalize` cannot remove its machine code from the NSO. They can still:

- unregister callbacks;
- remove hooks;
- restore patches;
- close file/log resources;
- stop worker threads;
- unregister services;
- clear owned engine references;
- mark the mod inactive.

That means the lifecycle remains worth enforcing even before NRO unload exists.

---

# 13. Inter-mod dependencies and service discovery

A recurring portability problem in the current mods is `GetProcAddress`/module lookup used to find another runtime component or provider.

That should be eliminated from gameplay code.

## 13.1 Use v3 `query_service`

I already have the right mechanism:

```cpp
host->query_service(
    host->host_context,
    service_id,
    requested_abi_version,
    &service,
    sizeof(service));
```

The goal is to make every cross-mod capability a named/versioned service.

Examples:

```text
ConditionServiceV1
WorldObjectServiceV1
MapRuntimeServiceV1
DatabaseExpansionServiceV1
LuaHostServiceV1
GameThreadDispatcherV1
InputServiceV1
FilesystemServiceV1
TargetResolverServiceV1
```

The service registry then works identically whether provider and consumer are:

- two Windows DLLs;
- two built-in Switch mods;
- a built-in host service plus NRO mod;
- two future NRO mods.

## 13.2 Do not expose raw C++ provider objects across NRO boundaries

Use POD C service tables with:

- ABI version;
- struct size;
- opaque context pointer;
- function pointers;
- explicit ownership rules.

Never expose `std::string`, `std::vector`, `std::filesystem::path`, exceptions, or C++ virtual interfaces through the public NRO boundary.

## 13.3 Define dependency load order declaratively

Eventually extend the v3 descriptor or a sidecar manifest with dependencies:

```yaml
id: cotc.crafting
requires:
  - service: ot0k.conditions.v1
  - service: ot0k.db_expansion.v1
optional:
  - service: ot0k.lua.v1
```

For built-in Phase 1, a topological load order can be generated at build time. For NRO Phase 4, it can be resolved at runtime.

---

# 14. Platform services that need Switch implementations

This section is the concrete "replace Win32" checklist.

## 14.1 Module image service

Current issue: `ModuleImage.cpp` only discovers a real image on Windows and otherwise returns invalid state.

Needed Switch API:

```cpp
struct ModuleSegment {
    uintptr_t start;
    size_t size;
    MemoryProtection protection;
};

struct ModuleImage {
    uintptr_t base;
    ModuleSegment text;
    ModuleSegment rodata;
    ModuleSegment data;
    std::string_view name;
    BuildFingerprint fingerprint;
};
```

At minimum, we have to resolve `main` and the injected Kingship NSO.

## 14.2 Memory query/read/write service

Replace Windows VM assumptions with a platform backend.

Required operations:

- query mapped region/protection;
- validate readable pointer/range;
- validate executable target;
- safe data write where permitted;
- code patch transaction;
- executable trampoline allocation;
- cache synchronization.

Keep raw `svc`/libnx/hook-framework details in one file set.

## 14.3 Filesystem/path service

Stop deriving runtime paths from `GetModuleFileName`.

Define logical paths:

```cpp
enum class RuntimePathKind {
    ReadOnlyAssets,
    UserConfig,
    WritableData,
    Logs,
    Plugins,
    CrashData,
};
```

Then map them per platform.

A possible Switch policy:

```text
read-only packaged assets:
  romfs:/KingshipAPI/...

user/mod writable data, when SD access is available:
  sdmc:/KingshipAPI/<title-id>/...
```

I won't make writable SD access a prerequisite for the first runtime boot. The host should still initialize with a fallback logger if SD mounting fails.

## 14.4 Logging

Replace `OutputDebugString` and ad-hoc file writes with sinks behind one logger.

Suggested sinks:

```text
Early ring buffer       always available
TCP/socket logger       development option
SD log file             development/release option if FS available
In-game debug overlay   later, once UI is stable
```

Every early fatal error should produce a compact numeric/error-stage code even if the normal logger is unavailable.

## 14.5 Threading

Create a small platform wrapper instead of sprinkling `#ifdef` around `HANDLE`/`CreateThread`.

Prefer portable synchronization (`std::mutex`, `std::condition_variable`, atomics) if the chosen Switch C++ runtime/toolchain behaves reliably for the build. Use libnx/native thread primitives behind the wrapper where explicit stack sizing/priority is required.

Important: a host worker thread is **not** a UE game-thread replacement.

## 14.6 Timing

Expose monotonic nanoseconds:

```cpp
uint64_t monotonic_time_ns();
```

Then all mods stop depending on `GetTickCount`, QPC, or platform-specific clock units.

## 14.7 Input

Replace `GetAsyncKeyState` usage with `InputServiceV1`.

Do not let each mod poll HID independently. The host can normalize input into actions/chords:

```cpp
struct InputSnapshotV1 {
    uint64_t held;
    uint64_t pressed;
    uint64_t released;
};
```

For debug tools, define configurable button chords. For gameplay UI, prefer UE/game input pathways where possible so input respects the game's own state.

## 14.8 Text encoding

Make host paths/config/log strings UTF-8. Add explicit conversion helpers only at UE/native boundaries.

This eliminates most `WideCharToMultiByte` / `MultiByteToWideChar` calls and avoids the `wchar_t` trap.

## 14.9 Process/thread IDs

Where the current code uses process/thread IDs only for diagnostics, hide them behind:

```cpp
uint64_t current_thread_token();
uint64_t current_process_token();
```

Do not make Win32 numeric-ID semantics part of an API contract.

## 14.10 Message boxes

A Switch mod runtime cannot rely on Win32 modal dialogs. Convert fatal/user-visible reports to:

- log + crash marker;
- optional in-game overlay/toast later;
- optional fatal screen only for unrecoverable developer builds.

---

# 15. Mod-by-mod migration strategy

The right order is not simply smallest source file first. Rank mods by:

1. ABI status (v3 vs legacy);
2. number of native targets required;
3. presence of x64/mid-function gateways;
4. direct Win32 calls;
5. inter-mod dependency complexity;
6. data-layout sensitivity;
7. amount of game-thread behavior.

The source scan gives a useful first triage.

| Mod | Current ABI | Approx. size | Major Switch blockers | Suggested tier |
|---|---|---:|---|---|
| `CAbiV3ExampleMod` | v3 | tiny | none/game-independent | **0 — first** |
| `CotCPort_BattleUI` | v3 | small | module/path Win32 calls, target remap | 2 |
| `CotCPort_Conditions` | v3 | medium | export/module lookup, targets | 2–3 |
| `CotCPort_Events` | v3 | small | x64-sensitive code paths, targets | 3 |
| `CotCPort_MapRuntime` | v3 | small | module lookup, targets | 2 |
| `CotCPort_WorldObjects` | v3 | small | x64-sensitive paths, targets | 3 |
| `MonsterArenaFixMod` | v3 | medium | x64/native targets | 3 |
| `OT0Kingship_LuaHost` | v3 | medium | dynamic-library assumptions, Lua/toolchain integration, x64-sensitive code | 4 |
| `RemoveMainCharacterPartyLockMod` | legacy | tiny | legacy ABI, x64/native assumptions, module path | 2 after conversion |
| `CotCPort_EnemyDmgUncap` | legacy | tiny | x64/native hook assumptions | 2 after conversion |
| `CotCPort_OverworldRenderHooks` | legacy | tiny | target remap | 2–3 |
| `CotCPort_SkillRules` | legacy | small | legacy ABI, provider/module lookup | 3 |
| `CotCPort_Stats` | legacy | small | direct code patching/VirtualProtect, x64 | 3–4 |
| `CotCPort_FieldSkills` | legacy | medium | module/provider lookup, targets | 3–4 |
| `CotCPort_DarkMap` | legacy | medium | module/provider lookup, paths | 3–4 |
| `CorruptDataProbeMod` | legacy | large | debug/process APIs, x64, module loading | 5 |
| `CotCPort_Ailments` | legacy | large | x64-sensitive native paths, many targets | 4 |
| `CotCPort_AutoDungeon` | legacy | large | module/file APIs, many native targets | 4 |
| `CotCPort_Menus` | legacy | medium | x64-sensitive code, targets | 4 |
| `CotCPort_Pets` | legacy | large | x64, input polling, module lookup | 4–5 |
| `CotCPort_SkillExtensions` | legacy | medium | executable allocation/patching, x64, module lookup | 4–5 |
| `CotCPort_Timeshifting` | legacy | large | x64, input, threading/timing assumptions | 4–5 |
| `CotCPort_DBExpansion` | legacy | very large | module/provider lookup, layout-heavy/database behavior | 5 |
| `CotCPort_Crafting` | legacy | very large | x64, direct code mutation, input, filesystem, many systems | **5 — late** |
| `MapDebugToolsMod` | legacy | very large | x64, threads/input/UI/debug APIs, huge target set | **6 — very late** |
