---
name: direct2d-rust-rendering
description: Implement and evaluate Direct2D and DirectWrite rendering in Rust Windows apps, including GDI interoperability, DPI, antialiasing, render-target recovery and CPU/GPU memory budgets. Use for a concrete Windows rendering need or renderer comparison; retain input and accessibility semantics and choose Fluent or another visual style separately.
---

# Rust Direct2D / DirectWrite 绘制

用于 Rust Windows 应用的矢量、文字或合成绘制。先明确要解决的画质/文字/帧耗时问题，再选CPU/GPU与宿主。Fluent/WinUI 3风格是设计选择；绘制API本身不提供完整控件、布局和交互。

## 选择渲染路径

| 场景 | 起点 | 必查成本 |
| --- | --- | --- |
| 现有Win32/GDI逐步平滑控件 | `ID2D1DCRenderTarget`接HDC | 绑定区域、opaque背景、DPI、内部位图复制与缓存 |
| 独立矢量画布 | HWND render target或合适的canvas host | resize、dirty region、device loss和首次创建设备 |
| 大量文字/精确缩放 | DirectWrite shaping/layout，Direct2D绘制 | 实际字体/回退、cluster、基线、caret/selection共用度量 |
| 高速合成与多表面 | DXGI swap chain / DirectComposition | GPU纹理预算、共享句柄、合成/输入生命周期 |

## 实施约束

1. 按锁定的 `windows` crate版本读生成签名和feature；Direct2D、Common、DXGI、DirectWrite及部分数学类型可能需要独立feature。
2. factory、目标、画刷和布局各有所有者；线程约束在类型/架构上保持。缓存device-dependent资源，并在目标失效时一起释放重建。
3. HDC互操作使用BGRA格式和适合表面的alpha；每次更换DC或区域重新BindDC。坐标单位只转换一次，避免把DPI同时用在布局与目标缩放。
4. BeginDraw/EndDraw配对并检查错误，失败保留可用回退。不要跨回调持有应用锁/借用，也不在绘制里启动IO或保存配置。
5. 自绘仅改变呈现；保留原生输入或提供完整语义控件。字体替换、中文/RTL/emoji、焦点、选择、IME和读屏分别验收。
6. 对照同一release页面的画质、private bytes、working set、GPU、GDI/USER与帧耗时；采用Direct2D的决定由证据支持。

## 参考

- [生命周期与代码](references/implementation.md)：版本化实现、DPI和目标恢复。
- [验证和内存](references/validation.md)：画质、输入、buffer与设备错误矩阵。
- [官方来源](references/sources.md)。

不要通过每次WM_PAINT重建factory/设备或强制清空工作集“优化”。小窗口的纯GDI可能更轻，复杂页面可能从Direct2D/DirectWrite受益；两者都需实测。
