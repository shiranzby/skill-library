# Fluent 设计参考

阅读日期：2026-11-01。下列“微软事实”来自 [Windows design](https://learn.microsoft.com/en-us/windows/apps/design/)、[Windows 11 design principles](https://learn.microsoft.com/en-us/windows/apps/design/design-principles)、[design guidelines](https://learn.microsoft.com/en-us/windows/apps/design/guidelines-overview) 及其子页；“工程建议”是本 skill 的适配方法。

## 微软事实

- Windows 设计原则强调 effortless、calm、personal、familiar、complete/coherent。指南覆盖 color、commanding、elevation、geometry、iconography、layout、materials、motion、navigation、typography、usability 和 writing。
- Fluent 颜色以中性色为基础，使用主题和 accent 表示层级及交互状态；同一颜色不能成为唯一的可访问状态线索。系统支持 light、dark 和 high contrast。
- Segoe UI Variable 是 Windows 11 的推荐系统字体。Windows type ramp 包含 12/16 caption、14/20 body、18/24 body large、20/28 subtitle、28/36 title 等；正文优先左对齐，标题使用 Semibold，避免把 Bold/Italic 当作默认层级。
- Windows 几何指南将普通控件圆角设为约 4px，顶层窗口和浮层约 8px；控件相接处保持直边。使用 4 epx 倍数有助于 100%、125%、150% 等缩放平台保持清晰。
- 设置页指南推荐可滚动的单列、约 1000–1100px 的最大内容宽度和分组标题；常见设置使用 SettingsCard / SettingsExpander。用户修改设置应立即反映，避免用确认按钮掩盖即时设置语义。
- 小、中、大窗口宽度约为 ≤640、641–1007、≥1008 有效像素。响应式可以 reposition、resize、reflow、show/hide 或 re-architect；应按窗口可用宽度而非物理屏幕分辨率作判断。
- Mica 是主要不透明窗口表面，Acrylic 适合 light-dismiss 的 flyout/context menu，Smoke 用于模态遮罩。材质缺失时要有可靠的纯色回退。
- Motion 应 connected、consistent、responsive、resourceful；官方给出 83ms、167ms、250ms、333ms 等典型时长，但具体时长仍需按动作和可打断性验证。

## 工程适配建议

### 设置窗口

保留用户现有 Windows 设置风格时，可以采用：页面标题与副标题 → 分组标题 → 每行一个 SettingsCard → 右侧动作控件。卡片之间保持一致的 4px/8px 节奏；长页面只保留一个主要滚动面。把“关于、版本、依赖、诊断”放在底部的 About 区域或 expander，不让实现细节占据首屏。

### 自绘或 Rust 实现

使用 `windows-rs`、Win32 或 Slint 时，复制的是设计语言：主题色、Segoe/中文回退、4/8 epx、标题栏命中区、Toggle/Radio 的状态和卡片布局。要自行实现：键盘焦点、UIA 暴露、文本缩放、高对比度、窗口拖拽、DPI、系统标题栏行为。报告中同时给出“仿 Fluent 的实现边界”和“未获得 WinUI 运行时支持”的事实。

### 文字与控件预算

正文不要小于系统可读下限；中文长标题、错误详情和热键文案要能换行或扩大窗口。控件可视形状可以小于触区，但触区至少 40×40 epx；触控场景建议 44×44 epx，并保持 4 epx 间隔。高 DPI 截图要同时报告有效像素和实际物理像素。

## 设备/姿态适配表

| 形态 | 主要任务 | 组件与布局 | 必测输入 |
| --- | --- | --- | --- |
| 桌面系统工具 | 快速看状态、改设置、关闭/恢复窗口 | 系统标题栏或可靠自定义标题栏；单列 SettingsCard；托盘与快捷键 | 鼠标、键盘 Tab/Enter/Esc、UIA |
| 平板横屏 | 触控修改设置、查看较多说明 | 触区 44×44 epx；两栏只在内容宽度足够时启用；滚动不抢焦点 | 触控点击/滑动、软键盘、旋转 |
| 平板竖屏/窄窗口 | 完成核心设置和错误恢复 | 单列、低频内容收进 expander；长文自然换行；不出现横向滚动 | 触控、键盘、焦点回退 |
| 可折叠/多窗 | 在姿态和窗口大小变化中保持上下文 | split/overlay 按可用区重排，避开折叠保留区，保留选择和草稿 | 姿态切换、窗口调整、键盘/UIA |

Windows 的有效像素与 4 epx 规则适用于 WinUI XAML；Rust/Win32 仿 Fluent 需要自己完成 DPI 到物理像素换算，必须把 100/125/150/200% 的实测几何单独列为证据。
