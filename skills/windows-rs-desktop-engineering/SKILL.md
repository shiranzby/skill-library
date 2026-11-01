---
name: windows-rs-desktop-engineering
description: Build and review Rust Windows desktop applications with microsoft/windows-rs, Win32, COM, WinRT, tray icons, hotkeys, DPI and native resource ownership. Use for Windows API integration or a lightweight Rust desktop implementation; choose the visual design separately and read the Direct2D reference when custom rendering is needed.
---

# Rust Windows 桌面工程

适合 Windows 系统工具、托盘程序、设备控制和需要精确系统集成的 Rust 应用。设计可以选择 Fluent/WinUI 3、现有产品或其它体系；Windows API 绑定本身不提供 WinUI 3 控件树。

## 先确定版本与实现层

- 先读项目 `Cargo.lock`、`Cargo.toml` 和实际生成的 Rust 签名。上游 `master` 的命名空间、feature 和错误 API 会变化；例如旧版 `Win32_Foundation` 与新头文件分组不能混抄。
- `windows` 适合需要广泛 API 的二进制；`windows-sys` 提供原始 FFI，所有权与失败判断由调用者承担。新增可复用库时评估上游 focused crates 与私有 `windows-bindgen` 绑定，避免多个大版本类型进入公共接口。
- 保持 HWND 和 UI 状态在窗口线程，COM 对象遵循 apartment；后台通过类型化命令/完成消息交互。先复制状态，再释放借用，然后调用会回调窗口的 Win32 API。

## 实施路径

1. 列明窗口、消息、托盘、热键、存储、设备接口和恢复记录的所有者。
2. 用 RAII 包装有匹配释放函数的资源。COM 接口 clone 增引用、Drop 释放；句柄、GDI 对象、物理显示器和共享系统图标有不同规则。
3. 把失败哨兵、HRESULT、LastError 和 API 返回状态分开处理；仅在文档定义 LastError 的失败后立即读取，不能把历史错误当当前失败。
4. 输入使用消息分发；`deny(unreachable_patterns, unused_variables)` 能发现把遗漏的消息常量写成绑定模式的错误。热键捕获需要覆盖焦点子控件、冲突和取消。
5. 配置先验证和持久化成功再提交生效状态；后台保存合并后续草稿，退出前等待最新状态。系统修改先落盘原状态并回读恢复。
6. 在实际目标 Windows 上点击、输入、拖动、缩放和截图，记录 release EXE 的哈希；编译不代表托盘、权限、硬件或可访问行为通过。

## 参考按需读取

- [窗口与资源](references/native-lifecycle.md)：重入、关闭/隐藏、DPI、托盘、热键和 COM。
- [绘制与内存](references/rendering-and-memory.md)：GDI、Direct2D/DirectWrite、缓存、working set/private bytes/GDI 对象的测量。
- [官方来源](references/sources.md)：固定上游提交和 API 入口。

## 交付边界

报告真实使用的 API、版本和渲染路径。仿 Fluent 的 Rust 窗口不能称作 `Microsoft.UI.Xaml`；GPU、DWM、WMI/DDC、HDR、屏幕捕获和远控必须分别验证。内存优化保持输入、文字、恢复和可访问语义，并提供相同场景的前后数据。
