## **LANGUAGE:**  [English](Custom_Prog_en.md) | [日本語](Custom_Prog_jp.md) | [中文](Custom_Prog_zh.md) | [한국어](Custom_Prog_kr.md)

原则上（以非常有限的方式）是可行的，但与其说是把 CotC 的代码“原封不动移植”到《Octopath Traveler 0》，不如说更像是通过 hook/扩展 OT0 的 UE5 运行时，在 OT0 里重新实现一套类似 CotC 的系统。
可行性最终取决于：《Octopath Traveler 0》里是否存在一个足够接近、可以借用/挂靠的子系统（例如小游戏框架、UI 路由等），还是说必须从零搭建完整的功能栈。

在 UE5 的已发行（shipping）游戏里，用 C++ 模组通常能做到的事情包括：
- 拦截原生函数（detours/vtable hooks）并改变其行为
- 在运行时调用反射得到的 `UFunction` / 生成 `UObject`（不过 OT0 的很多逻辑是原生实现的）
- 注入 UI（UMG widgets）与输入处理
- 如果能挂载/重定向路径，则可加载新资产
- 如果游戏是数据驱动的（DataTables、DataAssets），则可添加新的“内容”数据；如果是硬编码的，则尝试模拟（emulate）它

像“财富棋盘（Fortune Board Game）”这样的内容要移植到 OT0，可能还需要很长时间。
另外，如果不把某个现有的未使用值（如果存在的话）挪作他用，状态异常（ailments）的移植也可能不可行——因为它们看起来是以硬编码的 enum/switch 逻辑实现的（DataBase 文件 `SkillAilmentType` 似乎只处理状态异常的外观表现；往该列表里新增的任何状态异常都不会生效，即便是对 OT0 状态异常做的、标签完全相同的拷贝也一样）。
