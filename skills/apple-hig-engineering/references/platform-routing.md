# 平台路由和能力边界

官方入口：[Designing for iOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-ios)、[iPadOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-ipados)、[macOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-macos)、[tvOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-tvos)、[visionOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-visionos)、[watchOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-watchos)。先按平台路由，再选择共享组件或业务模型。

## 平台决策表

| 平台 | 主要环境和输入 | 设计/代码优先级 | 典型边界 |
| --- | --- | --- | --- |
| iOS | 单手触摸、通知、短任务、方向变化 | 以系统导航和 44 pt 左右触区为起点；Dynamic Type、VoiceOver、权限和离线状态必须可用 | 不把 iPad 多栏或 macOS 菜单直接缩小；App Clip、Apple Pay、App Shortcuts 等要核对 entitlement/系统版本。 |
| iPadOS | 大屏、多任务、可变窗口；触摸、键鼠、Pencil | Split view、拖放、键盘和 Pencil 互相补充；窗口变窄时切换到明确单栏路径 | 大屏不代表无限宽；Pencil hover、Stage Manager、外接键盘需在目标设备验证。 |
| macOS | 长时工作、多窗口、精确指针、菜单和快捷键 | 菜单栏、工具栏、Settings 窗口、文件模型和可调整尺寸优先；默认正文参考约 13 pt、常见控件约 28 pt | 不把 iOS 44 pt 当默认；触摸是次级输入。当前 HIG 不提供 iOS 式 Dynamic Type 契约，要验证窗口与文字设置。 |
| tvOS | 远距离观看、遥控器焦点、音视频 | 焦点路径、清晰的大字、少层级、播放和多用户切换优先 | 不能假设触摸、键盘或复杂输入可用；真机检查焦点、返回和观看距离。 |
| visionOS | 眼看+手势的间接输入、空间窗口/体积/沉浸空间 | 选择完成任务所需的最低沉浸度；居中、舒适距离、静止参考、60 pt 左右控件中心距和 16 pt 左右间隔作为常见指导 | App 不能读取用户 gaze 原始数据；自定义 hover 由系统处理。不要把内容锚在头部，不要未经许可启动 Full Space，手掌附近留给系统 overlay。 |
| watchOS | 小屏、抬腕、Digital Crown、通知、Always On | 可扫读、短操作、少层级；复杂输入可提供 iPhone 衔接，主要任务按产品定位独立可用；避免让人盯着长 spinner | 表盘/Complication/Widget 与普通页面不同；watchOS 没有可直接调用的系统 Game Center UI，能力须按 SDK 核实。 |

## 按项目选风格的矩阵

这张表用于在项目开始时选取布局和组件密度。它描述适配方向，不要求所有项目复制 Apple 的外观；已有品牌、Swiss、Fluent 或自定义视觉可以保留，再按输入和窗口约束调整。

| 项目/平台 | 窗口与布局 | 组件与输入 | 外观与密度 | 首要验收 |
| --- | --- | --- | --- | --- |
| iPhone / 紧凑移动端 | 单列、边缘安全区、纵向优先；横屏或多任务时重排，不把关键内容藏在横向滚动里 | 触摸为主，系统导航、按钮、列表、sheet；手势要有按钮/VoiceOver 替代 | 44 pt 左右触区起点、清楚的层级和大号正文；系统色/动态字体，少量固定工具栏 | 单手完成主任务、旋转、深浅色、最大字号、VoiceOver、离线/权限拒绝 |
| iPad / 平板 | 横竖屏、Split view、可变窗口和多任务；宽屏显示多栏，窄屏切单栏/导航栈 | 触摸、键鼠、指针、Pencil 可切换；拖放、快捷键和多选与触摸路径等价 | 内容密度高于 iPhone，但保留可读留白；分栏边界清楚，工具栏不遮住正文 | 方向变化、窗口缩放/Stage Manager、多任务恢复、键盘 Tab、指针 hover、Pencil/拖放 |
| Mac / 桌面端 | 可调整多窗口、面板、Split view；菜单栏和工具栏承担全局/窗口命令 | 鼠标/触控板、键盘快捷键为主；右键菜单、Tab 顺序、精确选择，触摸仅作补充 | 约 13 pt 正文、约 28 pt 常见控件起点；菜单/工具栏/侧栏优先，避免手机式大卡片和全屏 modal | 打开/关闭/最小化/全屏、窗口失焦恢复、菜单固定可发现、快捷键、长文本/多窗口 |
| tvOS / 远距屏幕 | 单主视图、清楚的焦点层级、横向媒体浏览；避免深层表单和密集列表 | Siri Remote/系统焦点为主；焦点移动、返回、播放控制和多用户路径 | 大字、高对比、少控件、较长观看距离；焦点 ring/缩放由系统语义表达 | 真机观看距离、焦点路径、遥控器返回、字幕/音频、加载/错误和 PIP |
| watchOS / 腕上短任务 | 单屏优先、少层级、垂直 page/列表；复杂编辑转 iPhone | 触摸、Digital Crown、通知、Always On；减少输入，避免长滚动和长等待 | 可扫读、短标签、关键数值突出；为常显和低亮度准备脱敏/高对比状态 | 抬腕/放下、Crown 导航、通知返回、Always On、断连、短任务完成与错误恢复 |
| visionOS / 空间应用 | Window/Volume/Full Space 按任务选择；保持环境可见、避免窗口堆叠和头部锁定 | 眼看+间接手势为主，必要时键盘/触控板/语音；不采集原始 gaze | 内容分层、清楚深度和静止参考；控件中心距约 60 pt、间隔约 16 pt 作为常见起点，不给文字滥加深度 | Shared Space 激活/失焦、重定位、视线舒适、空间音频、手部 overlay、离开/恢复和权限 |

选型顺序：先确认主要设备和输入，再决定单列/分栏/窗口，再选系统组件，最后调整颜色、材料、圆角和动效。外观相似不能证明交互适配；每个平台都要按本行的验收重点取证。

## 尺寸和文字参考

这些是 HIG 当前页面中的平台参考，不是验收硬编码：

| 平台 | 正文/系统文字参考 | 常见触区或控件参考 |
| --- | --- | --- |
| iOS/iPadOS | 约 17 pt 起步并支持 Dynamic Type | 常见控件约 44 pt；小于此值需有明确布局和输入理由 |
| macOS | 约 13 pt 系统正文 | 默认约 28 pt，某些紧凑控件可约 20 pt；键鼠精确定位优先 |
| tvOS | 约 29 pt 参考 | 按远距观看和焦点目标验证，不套触摸最小值 |
| visionOS | 约 17 pt 参考 | 常见交互目标约 60 pt，间隔与舒适距离依场景实测 |
| watchOS | 约 16 pt 参考 | 常见触区约 44 pt；内容应可扫读并适应 Always On |

中文、阿拉伯文和长本地化字符串可能改变这些度量。用系统文字样式和布局约束，验证最大字号、横竖屏、窄窗口和高对比度。

## 系统能力不要越权

- **权限和传感器**：摄像头、麦克风、位置、健康、蓝牙、本地网络、ARKit、跟踪等只在实际需要时请求；能力、用途文案、拒绝和 Settings 变更都由系统 API 决定。
- **Live Activities / Widgets / Always On**：它们是有限时、可扫读的系统入口，不是普通窗口的替代品；结束时间、敏感数据脱敏、深链对象、刷新限制和设备镜像要按文档和真机验证。Mac 上 Live Activity 主要是配对 iPhone 的镜像入口，不能据此宣称 Mac 本机 ActivityKit。
- **Apple Pay / IAP / Tap to Pay / Sign in with Apple / ID Verifier**：区分数字商品、实体商品、身份验证和支付读取；entitlement、管理员、服务商、地区和系统版本不足时不得自造仿制界面。
- **App Clip / App Shortcuts / Action Button / Camera Control / Pencil / Digital Crown**：仅在支持的平台、硬件和生命周期内提供；没有能力时给出合理回退，不把动作当成所有平台共有入口。
- **visionOS gaze 和 hover**：应用使用系统提供的交互反馈，不采集或假设原始 gaze 坐标。空间布局按舒适和环境感知检查，不能只用平面截图验收。

## 借鉴到非 Apple 项目

可迁移的是任务优先、系统语义、可访问、多输入、状态可见、失败可恢复和平台分流。不可直接迁移的是 Apple 的控件外观、SF Symbols、系统字体文件、权限弹窗、Apple 品牌、44/60 pt 常量和任何 entitlement 行为。Windows、Rust、网页项目应使用自身平台 API，并在报告中标明“借鉴原则”与“真实实现”。
