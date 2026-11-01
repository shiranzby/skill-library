# 控件、导航和输入

本页汇总 [Components](https://developer.apple.com/design/human-interface-guidelines/components)、[Selection and input](https://developer.apple.com/design/human-interface-guidelines/selection-and-input)、[Menus and actions](https://developer.apple.com/design/human-interface-guidelines/menus-and-actions)、[Accessibility](https://developer.apple.com/design/human-interface-guidelines/accessibility) 与 [VoiceOver](https://developer.apple.com/design/human-interface-guidelines/voiceover) 的实施要点。使用目标平台的原生控件优先；自绘时要补上同等语义和输入路径。

## 先选语义，再选外观

| 任务 | 优先组件 | 边界 |
| --- | --- | --- |
| 触发明确动作 | Button / 系统菜单项 | 文案用动作动词；不要把普通动作伪装成导航或切换。 |
| 二态设置 | Toggle；macOS 窗体分组也可用 checkbox | Toggle 必须让受影响对象清楚；状态不能只靠颜色。 |
| 多个互斥选项 | Segmented control、radio 或 pop-up | 选项较多时用菜单；不要让 segmented 周围堆放容易误焦的控件。 |
| 连续值 | Slider；需要精确输入时配 TextField/Stepper | 说明当前值和单位；大范围数值提供直接输入或较大步进。 |
| 少量文本 | TextField；长文本用 TextEditor/Text view | 可见标签、格式提示、按语境校验；敏感数据用 secure field。 |
| 分组导航 | Tab bar / Sidebar / NavigationSplitView | tab 用于导航而不是动作；窄窗口应切换为合适的单栏路径。 |
| 临时少量选择 | Popover、菜单或 sheet | 一次一个；警告不要放在容易被忽略的 popover。 |
| 不可逆/高影响决定 | Alert 或平台确认流 | 只在确有后果时打断；给出取消和后果说明。 |

## 组件和外观的平台适配

同一个业务对象可以共享数据模型和文案，但组件尺寸、排布、输入提示和材料要按平台路由。下面是从 HIG 页面整理的选择起点；项目已有视觉体系可以在这些约束内调整颜色、字体和形状。

| 平台 | 组件组合 | 外观调整 | 不要直接套用 |
| --- | --- | --- | --- |
| iPhone | NavigationStack、列表、Button/Toggle、sheet、系统键盘 | 单列、触区约 44 pt 起步、动态字体和安全区；主要动作靠近拇指可达区域 | 不把 Mac 菜单栏、密集多列或 hover 反馈压缩进小屏 |
| iPad | NavigationSplitView、侧栏/标签、工具栏、拖放、多选、Popover | 依窗口宽度在分栏与单栏间切换；横竖屏和 Stage Manager 下保持同一层级；Pencil/指针有可见反馈 | 不把 iPhone 全屏 modal 当作所有宽度的唯一导航 |
| Mac | Window、菜单栏、Toolbar、Sidebar、Popover/Panel、TextField | 键鼠焦点、菜单命令和可调整窗口优先；正文和控件可更紧凑，使用系统 titlebar/材料语义 | 不用 44 pt 触摸间距撑大整个桌面，也不把重要命令只放在右键菜单 |
| tvOS | Focus engine、Tab/媒体浏览、系统播放控件 | 大字号、远距对比和焦点层级；动作要能由遥控器完成，减少自由文本输入 | 不以触摸坐标、长表单或鼠标 hover 作为唯一入口 |
| watchOS | List、Page/Tab、通知、Complication/Widget、Digital Crown | 短标题、可扫读数值、Always On 脱敏和低亮度；复杂输入可提供 iPhone 衔接，主要任务仍按产品目标独立可用 | 不把 iPhone 的设置页和复杂编辑缩放到腕上 |
| visionOS | Window/Volume/ImmersiveSpace、ornament、系统 hover、间接手势 | 内容与环境有层次、保持空间稳定；控件中心距和间隔留足，避免头部锁定和过度透明 | 不把 gaze 坐标或 hover 状态当作业务数据；Full Space 和手掌附近区域遵循系统边界 |

自绘控件至少复刻语义状态、焦点、键盘/辅助技术路径和动态字体，而不是只复制圆角和颜色。组件“看起来像”某个平台不代表它获得了该平台的系统行为或 entitlement。

## 尺寸、焦点和可达性

- 尺寸按平台和输入方式分流。HIG 常见参考是 iOS/iPadOS 控件触区约 44 pt、macOS 默认控件约 28 pt 且可小至约 20 pt、visionOS 约 60 pt；watchOS 常见约 44 pt。它们不是跨平台 CSS/WinUI 常量，也不是替代内容预算的硬规则。
- iOS/iPadOS/tvOS/visionOS/watchOS 用系统文字样式支持 Dynamic Type；放大字体时重排、换行或滚动，不裁切文字。macOS 当前 HIG 不把 Dynamic Type 当作通用系统契约，应验证窗口缩放和用户文字设置。
- 最小可触区与视觉尺寸可以分开：用透明 hit area 扩大可点击范围，不能让紧凑桌面布局被手机式控件强行撑开。visionOS 控件要留足视线中心间距（常见指导约 60 pt 中心距、约 16 pt 间隙），避免 hover 重叠。
- 每个交互元素都要有可访问名称、角色、值和状态；VoiceOver 顺序应表达任务顺序。不要只读出颜色或装饰图形，图表按标记/组提供日期、单位和结论。
- 键盘、指针、触摸、Pencil、遥控器、Digital Crown 和辅助技术都是不同输入通道。重要动作至少有一种不依赖精细手势的替代；macOS 触摸是次级输入，不能破坏键鼠布局。
- 颜色、声音、触觉和动画都只是反馈的一部分；提高对比度、减少动态效果、粗体文字和 Assistive Access 等系统设置应得到尊重。
- 当前 [Buttons](https://developer.apple.com/design/human-interface-guidelines/buttons) 通用段建议约44pt命中区，而 [Accessibility](https://developer.apple.com/design/human-interface-guidelines/accessibility) 表按平台给出不同默认/最小尺寸；设计应结合具体控件、输入和密度验证，不机械取一条作为所有平台硬限制。尺寸表中的最小值也不是推荐的默认密度。
- [Eyes](https://developer.apple.com/design/human-interface-guidelines/eyes) 允许由系统进程外应用的预定义自定义 hover，而 Buttons 页另有按钮不支持自定义 hover 的说明。可用性需核对目标 SDK 和具体组件 API；两者都不授予应用读取原始 gaze 或 hover 时机的能力。

## 表单和输入细节

- 只要求完成任务必要的字段；可以选择、粘贴、拖放或系统 picker 时，不要强迫手工输入。数字、日期、货币使用本地化 formatter，不能假设 `.`、`,` 或固定日期顺序。
- 输入校验要选时机：格式稳定且错误明显时即时或离开字段校验；需要完整值的字段在提交前校验。错误放在字段附近并保留原输入。
- TextField 的宽度应反映预期长度；多个字段对齐、等距，Tab/键盘顺序与视觉顺序一致。提供清除、显示/隐藏或辅助按钮时保持清晰的可访问名称。
- Toggle 代表开/关状态，不用来承载“打开菜单/执行一次动作”。有层级依赖时用 checkbox 的缩进和 mixed 状态；超过约五个互斥选项考虑 pop-up/menu。
- 拖放要有键盘/菜单替代；跨容器通常复制、同容器通常移动，放置失败回到源对象并保留选择。不要让拖放成为唯一入口。

## 导航、菜单和层级

- Tab bar 保持可发现、标签稳定，通常控制在少量高频区域；复杂层级可提供 sidebar。Tab view 的每个 pane 自洽，标签描述内容，不用 pop-up 代替少量 tab。
- Split view 在 regular/宽窗口中展示多个 pane，保留当前选择，允许隐藏后从按钮、菜单或快捷键恢复；窄窗口切成单栏或可预测的导航路径。
- macOS 菜单遵循系统顺序和标准快捷键。动作即使当前不可用也保持可发现并禁用，不让动态菜单成为唯一入口。Show/Hide 文案要反映当前状态。
- Settings 页面使用稳定入口、默认值和清晰分组；系统级选项跟随系统 Settings，任务相关选项留在当前任务上下文。不要把每一行都包装成装饰卡片。
- Popover/sheet 只展示当前上下文需要的内容，箭头/位置指向触发源；外部关闭时保存非模态编辑，明确 Cancel 才丢弃；复杂流程改用独立窗口或全屏。

## 工具栏、可撤销动作和多输入补充

- 工具栏承载当前视图的动作和导航；Tab 切换应用区域。iPad/Mac 让系统处理变窄时的溢出，主要末端动作保持可达，Mac 工具栏命令同时出现在菜单栏。参见 [Toolbars](https://developer.apple.com/design/human-interface-guidelines/toolbars)。
- 可撤销的编辑标明具体动作，并把撤销结果滚入可见区域；Mac 保留 Edit 菜单、Command-Z/Shift-Command-Z，手机和平板保留系统撤销手势。参见 [Undo and redo](https://developer.apple.com/design/human-interface-guidelines/undo-and-redo)。
- 手机/平板用键盘布局引导避让输入与提交按钮；visionOS 的键盘是独立窗口，Watch 主要按 content type 提供建议。不要为跨平台统一而自己仿制所有键盘。参见 [Virtual keyboards](https://developer.apple.com/design/human-interface-guidelines/virtual-keyboards)。
- VoiceOver 按标题、分组和语言顺序表达关系；内容和布局改变时发出可访问通知，图表交互提供等价操作，装饰图不进入朗读路径。参见 [VoiceOver](https://developer.apple.com/design/human-interface-guidelines/voiceover)。

## 实施问题

实现每个控件时回答：

1. 初始、加载、成功、空、失败和禁用状态是什么？
2. 用户如何取消、重试、撤销或恢复？
3. 键盘/VoiceOver/高对比度/大字体是否仍能到达同一动作？
4. 状态是否同时由语义、文字/图标和可访问值表达，而非只靠颜色？
5. 回调是否只修改自己的状态，迟到的异步结果是否会覆盖较新的意图？
