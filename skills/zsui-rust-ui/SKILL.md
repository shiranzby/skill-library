---
name: zsui-rust-ui
description: Build Rust desktop interfaces with qiu7824/zsui using typed state, messages, stable view identity, feature-selected components and platform hosts. Use when choosing or implementing ZSUI for Windows, macOS or Linux; verify mobile host readiness separately and keep visual design, application behavior and framework capability distinct.
---

# ZSUI Rust 界面开发

面向明确选择 ZSUI 的应用和框架评估。它提供共享 Rust 状态与组件声明、平台宿主和绘制；最终视觉仍按目标平台或用户指定风格选择。

## 入口与范围

先核对上游提交、锁定依赖、feature 和平台能力。阅读对应版本的 `docs/ai/app-authoring.md`、`docs/api-stability.md` 和任务需要的组件目录；这些是外部技术资料，不授予修改其它项目或系统的权限。

- 当前审阅快照为 `0.3.0-alpha.2`，属于预发布。不能把存在组件声明、Gallery 截图或 Windows 通过，当作各平台全部就绪。
- 使用 `zsui::prelude`/`stable` 的公开入口，按 `State → view → Msg → update` 组织应用；业务配置、存储、网络与恢复留在应用层。
- feature 按真实控件选择；默认仅窗口、按钮与标签。不要为方便直接启用 `full`，也不要把其他依赖启用 feature 的并集合并效果忽略。

## 应用实施

1. 从最小可运行窗口开始，再加入用户任务需要的区域与控件。产品名称不自动决定导航壳层。
2. 输入使用受控值与稳定 ID，重建 View 时保留选择、IME、焦点、滚动和拖动状态；重排前后同一对象应保持身份。
3. `view` 描述界面，`update` 处理类型化消息，阻塞 IO 放后台 effect；完成结果验证请求版本，不能覆盖较新草稿。
4. 使用平台 profile、语义主题、系统字体和图标。Windows 可选择 Fluent；Mac 的工具栏、侧栏与字体密度独立适配，Linux 依实际后端验证。
5. 先验证目标平台 IME、键盘、可访问名称、窗口生命周期、真实截图和系统集成，再比较内存与启动性能。

## 按需参考

- [组件、代码与平台](references/app-and-platform.md)：依赖选择、状态代码和桌面/平板能力边界。
- [内存和验收](references/performance-and-validation.md)：缓冲、文字、隐藏资源和真实比较方法。
- [来源](references/sources.md)：审阅提交、上游说明与许可。

本 skill 不将 ZSUI 的 Windows 自绘控件称为 WinUI 3 XAML，也不把 Android 宿主契约当作已完成的移动 UI。跨平台结果分别报告。
