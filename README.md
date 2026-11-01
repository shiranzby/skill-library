# Personal Skill Library

个人可复用的 Codex Skill 仓库。设计选项与 Rust 实现选项可组合使用，每个 Skill 以独立目录维护。

## 选择设计与技术

设计风格与技术实现分别选择；已有项目的外观、交互和技术约束优先。系统工具可组合 **WinUI 3/Fluent 设计 + windows-rs 实现**。

| 类型 | Skill | 适用场景 |
| --- | --- | --- |
| 设计 | [Apple HIG](skills/apple-hig-engineering/SKILL.md) | iPhone、iPad、Mac；另含电视、手表、空间设备分流。173页文本快照及逐页原创提炼 |
| 设计 | [WinUI 3 / Fluent](skills/winui3-product-engineering/SKILL.md) | Windows桌面系统工具、平板触控、窗口化与分屏；区分真实XAML与其它栈仿Fluent |
| 设计与工程 | [Swiss](skills/swiss-product-engineering/SKILL.md) | 用户选择瑞士国际主义时使用；工程验收方法可按需复用 |
| Rust实现 | [windows-rs](skills/windows-rs-desktop-engineering/SKILL.md) | 轻量Win32窗口、COM、设备、托盘、热键和原生资源 |
| Rust实现 | [ZSUI](skills/zsui-rust-ui/SKILL.md) | 共享状态/消息/声明式组件和平台宿主；预发布能力逐平台验收 |
| Rust实现 | [GPUI-CE](skills/gpui-ce-engineering/SKILL.md) | Entity、GPU绘制、自定义组件、复杂交互和虚拟列表 |
| Rust绘制 | [Direct2D / DirectWrite](skills/direct2d-rust-rendering/SKILL.md) | 矢量抗锯齿、文字布局、HDC互操作和设备资源；独立测CPU/GPU占用 |

### 调用示例

```text
使用 $winui3-product-engineering 和 $windows-rs-desktop-engineering，
保持 Windows 系统工具风格，实现 Rust 托盘设置页，验证全部控件、自动保存和内存。

使用 $apple-hig-engineering，为 iPhone、iPad、Mac 分别选择导航、组件、外观和输入适配。

使用 $zsui-rust-ui，为现有 Rust 项目评估跨桌面宿主，保留产品状态与存储逻辑。

使用 $gpui-ce-engineering，构建支持键盘、IME、虚拟列表和状态恢复的桌面界面。

使用 $direct2d-rust-rendering，对当前 GDI 页面做同场景画质、DPI和内存对照，再决定渲染路线。
```

## 安装与复用

将需要的 `skills/<skill-name>` 整个目录安装到 Codex 的 skills 目录；每个目录包含入口、UI元数据和按需引用资料，可单独复制。可使用 Codex 自带的 `$skill-installer` 从本仓库选择这些子目录安装。仓库里的存在不等于当前会话已安装；安装后按宿主要求刷新技能列表。

也可直接把所需目录的 `SKILL.md` 路径交给代理读取。不要只复制入口而遗漏 `references/`。

## 校验与证据

- 各目录可运行 Codex `skill-creator` 的 `scripts/quick_validate.py <skill-dir>` 检查命名、frontmatter和占位符。
- 检查 Markdown 内部相对链接、YAML元数据以及引用文件；新代码在目标工具链/系统重新编译和测试。
- Apple覆盖是2026-11-01的173页**文本快照**，含平台分支、表格和图像替代说明；原始媒体、外链SDK和设备运行不在这个声明内。早期不完整阅读声明已撤回并补齐，见技能来源记录。
- Rust三个仓库为固定提交的定向文档/源码核查，未宣称通读全部代码或替所有应用完成性能测量。
- 上游版本、预发布状态、平台能力和许可在实施前复核。真实性、交互、截图、持久化、硬件与内存分别取证。
