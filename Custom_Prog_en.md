## **LANGUAGE:**  [English](Custom_Prog_en.md) | [日本語](Custom_Prog_jp.md) | [中文](Custom_Prog_zh.md) | [한국어](Custom_Prog_kr.md)

This is possible in principle (in a very limited fashion) but would rather boil down to being more like re-implementing CotC-like systems inside Octopath 0 by hooking/extending its UE5 runtime as opposed to literally porting CotC code into Octopath 0.
The feasability ultimately depends on whether Octopath 0 has a nearby subsystem you can piggyback on (e.g. minigame framework, UI routing, etc.) or whether one would have to build an entire feature stack.

What one can do with C++ mods in UE5 shipping games is typically:
- intercept native functions (detours/vtable hooks) and alter behavior
- call reflected `UFunction`s / spawn `UObject`s at runtime (a lot in OT0 is handled natively, however)
- inject UI (UMG widgets) and input handling
- load new assets if you can mount/redirect paths
- add new "content" data if the game is data-driven (DataTables, DataAssets) or emulate it if it's hardcoded

It will likely take a long time before something like the Fortune Board Game is ported to OT0.
Porting ailments may not be possible either without repurposing an existing unused value (if any) as they seem to be implemented as hardcoded enum/switch logic (the DataBase file `SkillAilmentType` only seems to handle aesthetics of the ailment; any ailment that is added to this list is not functional, even copies of OT0 ailments with exact same labels).
