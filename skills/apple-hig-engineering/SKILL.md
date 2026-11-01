---
name: apple-hig-engineering
description: Use Apple Human Interface Guidelines as an optional design and implementation reference for iOS, iPadOS, macOS, tvOS, visionOS, watchOS, or a deliberately Apple-informed product. Route decisions by platform, system controls, accessibility, state and recovery; keep the chosen visual style and non-Apple technology explicit. Do not use it to force SwiftUI, Apple branding, or mobile interaction patterns onto Windows, Rust, web, or another existing product.
---

# Apple HIG 设计与开发

这是一个可选的 Apple 设计与工程参考。它帮助把任务、平台习惯、输入方式、系统能力和可验证状态连起来；它不是视觉换肤指令，也不替用户选择 SwiftUI、AppKit 或 Apple 的材料。项目已有风格优先保留，只抽取适合目标平台的原则。

## 开始前划清边界

1. 写明目标平台、最低系统版本、SDK、输入设备和窗口形态。
2. 区分三类结论：
   - **HIG 建议**：可读性、层级、反馈、隐私和可恢复性等设计判断。
   - **API 能力**：由 Apple Developer Documentation、SDK availability、entitlement 和真实设备决定。
   - **本次证据**：源码检查、模拟器/真机交互、截图几何和硬件行为分别记录。
3. 如果目标是 Windows、网页、Rust 或其他栈，称为“借鉴 Apple HIG 原则”或“保留现有风格并采用 HIG 交互细节”，不要声称使用 SwiftUI、AppKit、ActivityKit 或 Liquid Glass。
4. 不复制 Apple 手册或 Apple Logo 资源；对品牌按钮、支付、身份、传感器、权限和商店能力先核对官方资格。

## 按需读取参考

- [references/design-principles.md](references/design-principles.md)：核心原则、内容/状态/隐私/文案和视觉取舍。
- [references/components-and-inputs.md](references/components-and-inputs.md)：控件、导航、菜单、表单、输入、反馈和无障碍。
- [references/platform-routing.md](references/platform-routing.md)：各平台的尺寸、输入、窗口、材料和能力边界。
- [references/platforms-and-technologies.md](references/platforms-and-technologies.md)：平台技术、系统材料、动态字体和跨平台借鉴边界的补充记录。
- [references/swiftui-engineering.md](references/swiftui-engineering.md)：SwiftUI 状态、异步、取消、错误恢复和撤销代码范式；代码仍需按目标 SDK 编译。
- [references/validation.md](references/validation.md)：从静态检查到真机交互的验收矩阵和证据写法。
- [references/sources.md](references/sources.md)：官方链接、阅读快照和覆盖说明。
- [references/hig-readings-a.md](references/hig-readings-a.md) 与 [references/hig-readings-b.md](references/hig-readings-b.md)：173 页逐页阅读覆盖记录；按需审计来源，不作为项目业务规则。

只读取当前任务相关的参考。项目选风格和适配方向时先读 `platform-routing.md` 的平台选型矩阵；需要做控件或表单时再读 `components-and-inputs.md`；需要写 SwiftUI 时再读 `swiftui-engineering.md`。

## 实施顺序

1. 先定义用户目标、成功/失败/取消/重试路径和状态所有权，再选组件和视觉材料。
2. 能用系统控件、系统颜色、系统导航、系统权限、系统分享或系统播放器时优先复用；自定义控件必须补齐可访问名称、键盘/指针/手势替代、焦点反馈和动态字体行为。
3. 把长耗时工作放在合适的异步边界，给出加载、取消、失败和恢复状态；迟到结果不能覆盖更新的意图。敏感数据只在必要时请求，说明用途并尊重拒绝。
4. 先跑与用户问题对应的反例，再做阶段回归。源码通过不等于真实交互通过，模拟器通过不等于传感器、权限、显示器、支付或真机通过。
5. 报告中分别列出已验证、仅静态推断、使用模拟数据、缺少设备/权限而未验证的项目。

## 不要越界

- 不因启用本 skill 改掉用户已有的视觉语言、产品信息架构或技术栈。
- 不把 iOS 的 44 pt、visionOS 的 60 pt、macOS 的 28 pt 等参考尺寸当成跨平台统一常量；具体尺寸随平台、内容、输入和可访问设置验证。
- 不把权限提示、状态栏、Live Activity、App Clip、Apple Pay、Sign in with Apple、Tap to Pay、ARKit 或 gaze 数据当成普通自绘 UI。
- 运行环境缺少 `xcodebuild`、Apple Simulator 或目标真机时，只报告文档核读、源码检查或替代测试的结果；有目标环境时完成对应构建和设备验收。
