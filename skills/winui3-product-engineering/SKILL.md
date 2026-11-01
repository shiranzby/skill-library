---
name: winui3-product-engineering
description: Design and implement Windows desktop settings and product interfaces with WinUI 3 and Fluent conventions, or deliberately reproduce that visual language in another stack while keeping the technology boundary explicit. Use for Windows app layout, controls, title bars, accessibility, DPI, async state, packaging, and real interaction validation; do not use it to force WinUI 3 or C#/C++ onto a Rust, Win32, Slint, or web project.
---

# WinUI 3 产品设计与开发

这个 skill 是 Windows Fluent 的可选设计与工程方案。它不取代用户已经选择的产品风格，也不把 Swiss 设计约束变成默认规则。

## 先确认技术边界

在开始前写清楚当前实现属于哪一类：

1. **真实 WinUI 3**：`Microsoft.UI.Xaml` 控件、Windows App SDK、XAML、C# 或 C++/WinRT。可使用 `TitleBar`、`AppWindowTitleBar`、`SettingsCard`（Windows Community Toolkit）和 UI Automation。
2. **Windows 原生实现**：Win32/`windows-rs`/Rust 等。可以借鉴 Fluent 的颜色、字号、几何、设置卡片和标题栏行为，但报告中写“仿 Fluent”或“保留 Windows 风格”，不能称作 WinUI 3。
3. **其它实现**：Slint、WPF、HTML 等。只在用户选择后借鉴 Fluent；不要因视觉相似声称使用了 WinUI 3。

用户指定 Rust 时保持 Rust 和现有系统边界；本 skill 提供 C#/XAML 范式只是用于对照和设计决策。控件、Windows App SDK、Community Toolkit 的版本必须记录，不把 UWP、WinUI 2、WPF 或 Fluent 2 Web 组件混写成 WinUI 3。

## 需要时再读的参考

- [references/design.md](references/design.md)：Windows 设计原则、颜色、字体、几何、材料、动效和响应式布局。
- [references/controls-and-settings.md](references/controls-and-settings.md)：设置页、`SettingsCard`、`SettingsExpander`、开关、单选、数字和时间输入。
- [references/engineering.md](references/engineering.md)：C#/XAML 状态边界、异步写入、标题栏、单实例、DispatcherQueue、版本适配和仿 Fluent 实现注意事项。
- [references/validation.md](references/validation.md)：真实交互、键盘/UIA、DPI、视觉、窗口生命周期和硬件边界验收矩阵。
- [references/sources.md](references/sources.md)：本 skill 使用过的官方文档、Gallery 提交和许可证；来源事实与本 skill 的工程建议分开。

## 设计决策

- 先保证任务清楚、状态可见、输入可达和失败可恢复，再决定卡片、材质和动画。
- Windows 11 Fluent 的参考方向是 effortless、calm、personal、familiar、complete/coherent。颜色只表达层级、交互和状态；支持浅色、深色和高对比度。
- 使用系统字体与主题资源优先。Segoe UI Variable 在真实 WinUI XAML 中默认参与字体回退；中文遵循系统语言字体映射。不要在自绘实现中假设每台 Windows 都有 Variable 字体。
- Windows 几何以有效像素和 4 的倍数为起点；常见控件圆角 4px，窗口/浮层 8px，互相接触的边缘不强行加圆角。设置页可使用卡片，但不把每一小段内容都包成装饰卡墙。
- 设计窗口可用宽度而非物理屏幕分辨率。小于 640、641–1007、1008 以上是可参考的宽度段；实际项目按内容预算验证，不能只按媒体查询声称响应式。
- Mica 适合主要不透明窗口表面，Acrylic 适合轻量临时弹层，Smoke 适合模态阻塞层。缺少系统材质时提供不透明回退，不把透明度当信息层级唯一依据。
- 动效应连接状态、响应输入、保持一致，短而可中断；支持减少动态效果。不要为每个设置行添加持续动画。

## 按设备和窗口形态分流

先根据**可用窗口大小、输入方式和设备姿态**选方案，不能只看物理屏幕型号：

- **桌面/系统工具**：以键鼠和键盘为主，保留系统标题栏、托盘入口、紧凑的单列设置和明确的快捷键。宽窗口可以增加辅助说明或双栏，但不让系统工具变成宽屏装饰面板。
- **平板触控**：优先 40×40 epx 触区，触控密集场景用 44×44 epx 且保留间隔；使用大尺寸 Toggle、Slider、TimePicker 和可滚动设置组，测试触控命中、横屏/竖屏和软件键盘遮挡。
- **窗口化/分屏**：按窗口有效宽度重新排列，同一状态和命令保持不变。小于 640 epx 收窄低频说明并改为单列；641–1007 epx 维持单列或有限双轨；1008 epx 以上才考虑并排辅助区。
- **可折叠或多屏姿态**：把折叠缝、保留区、窗口标题栏和系统 caption buttons 当作不可用空间；优先使用可收缩的 split/overlay 结构，姿态变化时保留对象、选择、焦点和草稿。若项目不是可折叠设备，不要凭截图声称已验证该路径。

输入适配要同时覆盖鼠标、触控、键盘、触控笔和 UI Automation。不要用 `hover` 作为触控唯一反馈；不要用自绘点击区域替换真实键盘焦点或可访问名称。

## 实施顺序

1. 先列出对象、设置、默认值、保存边界、失败行为和窗口生命周期。
2. 优先使用系统控件和主题资源；自定义控件必须补可访问名称、键盘路径、焦点视觉和 UIA 行为。
3. 把 UI 线程限定为布局、输入和反馈。文件、网络、WMI、DDC、HDR 或进程操作后台执行，完成后回 UI 线程并校验请求身份，迟到结果不得覆盖新值。
4. 自动保存要显示保存中、成功和失败；失败保留用户输入并提供重试，取消不写入。需要多个系统写入时要记录部分成功并提供恢复路径。
5. 先定向复现，再做阶段回归。静态编译、真实点击/键盘、UIA、截图几何和硬件行为分别报告。

## 不要越界

- 不因 Windows 风格选择而把 Rust 项目改成 C# 或引入 Windows App SDK。
- 不把 SettingsCard、Mica 或 `TitleBar` 视觉复制到不适合的页面；先判断页面是设置、编辑、监控还是内容阅读。
- 不用截图、源码匹配或历史测试数量代替真实交互；当前构建和运行进程必须可追溯。
- 不把 HDR、DDC、WMI、远程桌面、托盘、全局热键等系统行为当成 UI 控件已经验证。记录设备、权限、会话和未覆盖的边界。
