# 平台与系统技术适配

本文按需阅读。先确定目标平台、最低系统版本、SDK 和真实使用方式，再选组件与 API。Apple HIG 是设计指导；API 可用性须由对应 Developer Documentation、项目 SDK 与实际构建确认。阅读日期：2026-11-01。

## 平台分流

| 平台 | 先解决的设计问题 | 代码与验证入口 |
| --- | --- | --- |
| iPadOS | 多任务与窗口宽度变化；触摸、键鼠、Pencil 交替；减少不必要全屏跳转 | 优先系统导航、布局、拖放与输入支持，按目标 SDK 核实 API；验证方向、多任务、Dynamic Type、键盘和指针 |
| macOS | 长时工作、多个应用与窗口并存、精确选择、菜单命令和快捷键 | SwiftUI/AppKit 的窗口、菜单、命令与文件模型；验证窗口缩放、失活/激活、键盘操作和工具栏定制 |
| tvOS | 从远处识别内容，明确当前焦点，遥控器与多用户切换 | 系统焦点与遥控器语义；真机检验焦点路径、返回行为、文本距离与音视频 |
| visionOS | 选择合适的沉浸程度，保持用户舒适与周围环境感知 | Window/Volume/ImmersiveSpace 与系统输入；验证间接手势、视野、空间音频、辅助技术和真机舒适度 |
| watchOS | 可扫读、单屏短操作、少层级与 Digital Crown 导航 | 独立应用、系统通知、复杂功能与 Always On；验证手腕姿势、屏幕状态和短任务完成 |

macOS 的当前 HIG 包括部分触摸设备指导，但明确保留面向键盘和指针的大小与密度。所有平台共享的数据和任务身份应稳定，导航结构、触区、材料和输入反馈则按平台适配。

平台依据：[iPadOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-ipados)、[macOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-macos)、[tvOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-tvos)、[visionOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-visionos)、[watchOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-watchos)。

## 字体、颜色与材料

- 当前 HIG 的正文默认字号分别为 iOS/iPadOS 17pt、macOS 13pt、tvOS 29pt、visionOS 17pt、watchOS 16pt；这些是平台参考，不应把最小字号当作设计目标，也不能直接换成跨平台 CSS px。
- iOS/iPadOS、tvOS、visionOS、watchOS 的 Dynamic Type 要通过系统文字样式与随字号重排支持；当前 HIG 明确 macOS 不支持 Dynamic Type。Mac 应使用合适的系统字体变体、提供任务需要的缩放或文字设置，并验证窗口与用户设置。
- 用系统字体 API，不随应用重新打包 SF/NY 系统字体；自定义字体要实现相应的可读性、字重和可访问缩放行为。中文等语言检查真实字体回退、行高和文本长度。
- 语义颜色表示 label、separator、selection、background 等职责，不用文档中的 RGB 快照替代 API；自定义颜色至少验证目标浅深外观、提高对比度和背景变化，图像保留正确色彩配置。
- 在支持的系统上，Liquid Glass 属于功能与导航层。内容层使用标准材料或适合内容的背景；定制玻璃只用于有理由强调的控件。regular 注重可读性，clear 适合视觉丰富的媒体背景并可能需要暗化层；不要把所有表单分组做成玻璃。
- visionOS 的系统 glass 随物理和虚拟环境变化，没有独立 Dark Mode；需要真机检查透明度、亮度与空间舒适。动效应短、可取消，并响应无障碍设置，不能把等待动画当作异步状态管理。

设计依据：[Typography](https://developer.apple.com/design/human-interface-guidelines/typography)、[Color](https://developer.apple.com/design/human-interface-guidelines/color)、[Materials](https://developer.apple.com/design/human-interface-guidelines/materials)、[Motion](https://developer.apple.com/design/human-interface-guidelines/motion)。开发入口：[SwiftUI Font](https://developer.apple.com/documentation/swiftui/font)、[NSFont](https://developer.apple.com/documentation/appkit/nsfont)、[SwiftUI Material](https://developer.apple.com/documentation/swiftui/material)、[NSVisualEffectView](https://developer.apple.com/documentation/appkit/nsvisualeffectview)。

## 适用与验收边界

- Windows 或网页中的 Apple 风格是视觉与交互适配，不能宣称使用了 SwiftUI、AppKit 或系统 Liquid Glass；系统字体、图标与素材应按各自授权使用。
- 本次研读发生在 Windows。文档核读、代码静态检查和资料引用分别成立；未运行 `xcodebuild`、Apple 模拟器或 Apple 真机，不能据此声称完成平台验收。
- 按目标平台验证布局、输入、状态恢复、无障碍和持久化；需要特殊设备、传感器、授权或账户的技术，单独记录模拟与真实能力边界。
