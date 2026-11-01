---
name: gpui-ce-engineering
description: Design and implement Rust desktop applications using the GPUI Community Edition fork, with Entity state, Render views, typed actions, focus, virtualized lists and GPU rendering. Use for GPUI-CE architecture or app implementation; inspect the pinned fork version and platform capabilities, and choose Fluent, Apple or another visual system explicitly.
---

# GPUI Community Edition 工程

用于明确选择 [GPUI-CE](https://github.com/gpui-ce/gpui-ce) 的 Rust 桌面应用。适合需要组合布局、自定义绘制、大集合或编辑器式交互的项目；产品外观按用户选择建立 token 和组件，不能把 GPU 渲染等同于 WinUI 3。

## 开始前

- 核对 fork、crate package、lib 名、commit/版本、MSRV、平台 feature。审阅快照的 package 为 `gpui-ce`，Rust 中导入的库名为 `gpui`；CE 与 Zed 上游正在分化，示例不可跨版本混用。
- 先运行当前版本最小例子，验证 Windows/MSVC、macOS/Metal 或 Linux 图形会话依赖。目标平台的 IME、无障碍、GPU/驱动和窗口服务分别检查。
- 保持业务状态与渲染对象独立；不要为切换框架重写已验证的硬件/恢复/存储核心。

## 状态与交互

1. 长期状态由 `Entity<T>` 等模型持有，`Render` 只组合界面。使用稳定 element ID 保留焦点、滚动与交互身份。
2. 点击和动作通过 `cx.listener` 回到拥有者，状态改变后 `cx.notify()`；渲染时不阻塞，也不启动无界副作用。
3. 键盘命令声明 typed actions，设置 key context、focus handle 和 action handler。窗口内 keybind 不等于系统全局热键。
4. 后台任务使用符合该版本 API 的 executor/weak entity 更新路径，检查取消和 revision，视图关闭后不能保活大型任务或回写旧状态。
5. 同高大列表优先 `uniform_list`；变高内容选合适的 list 机制，不为未显示的所有行创建 entity、纹理和文本布局。

## 参考

- [代码与组件](references/views-and-input.md)：API范式、输入、主题和平台路由。
- [渲染与性能](references/rendering-and-performance.md)：GPU/字体/缓存预算及真实比较。
- [固定来源](references/sources.md)：当前CE快照与目标平台说明。

交付同时给出当前项目构建、键鼠/IME、截图、窗口行为和持久化证据；自定义 Fluent 外观需自行补键盘、UIA、主题、标题栏和系统集成。
