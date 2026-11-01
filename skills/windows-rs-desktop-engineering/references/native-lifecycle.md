# 窗口、线程与资源

## 最小所有权模型

| 对象 | 所有者/释放原则 | 常见错误 |
| --- | --- | --- |
| HWND、控件状态 | 创建窗口的消息线程；明确 DestroyWindow 时机 | 把所有 HWND 放静态 Mutex，持锁 CreateWindow/SendMessage 后回调重入 |
| COM 接口 | apartment 有效范围；引用计数封装自然 Drop | 从 VARIANT 内部指针 `from_raw` 偷走 BSTR，造成重复释放 |
| HANDLE | 对应 API 指定的 CloseHandle/专用释放函数 | 用 CloseHandle 释放物理屏句柄、共享图标或伪句柄 |
| HBRUSH/HFONT/HPEN | 恢复 DC 原选中对象后 DeleteObject | 在每次绘制保留新字体，或删除仍被 DC/窗口类使用的对象 |
| 物理显示器 | 全部返回句柄立即取得 RAII 所有权 | 不支持/多屏歧义的早返回遗漏 DestroyPhysicalMonitor |
| 系统光标 | 区分共享句柄和 SetSystemCursor 所有权转移 | 部分替换失败后不恢复系统指针 |

## Win32 消息路径

UI 状态用线程局部存储或窗口 user data 管理。回调中只记录意图；有重入可能的 FFI、文件写入、COM 调用放到短借用之外。不要用 `unsafe impl Send/Sync` 把窗口线程约束擦掉。

创建与同步子控件会产生通知：用 `initializing/syncing` 守卫，使初始 SetWindowText、BM_SETCHECK 等不保存半成品配置。销毁时也需阻止失焦通知读取已经失效的控件。

后台完成结果携带请求代号；快速反向操作立即使旧代号失效。关闭期间取消在途操作、恢复系统状态、等待最后的配置保存，再结束消息循环。`WM_QUERYENDSESSION` 的返回值决定是否允许注销，不能把普通关闭处理无条件套过去。

## 窗口与输入验收

- 单实例互斥句柄保持整个进程生命周期；第二实例唤起第一实例。测试隔离数据目录可以使用独立互斥身份。
- 明确最小化、关闭隐藏、恢复后退出。自定义标题栏保留 caption hit test、八向缩放、双击、最大化/还原、系统菜单、DPI 和工作区边界。
- 通知图标使用唯一身份和正确版本。v4 通知需按低位事件解析，避免同时响应兼容鼠标事件和 `WM_CONTEXTMENU/NIN_SELECT` 导致重复弹出。
- `RegisterHotKey` 使用 `MOD_NOREPEAT`。新组合先保留注册，再保存；任一步失败保留旧组合。录入期间禁用动作，取消/失焦/关闭释放临时捕获。
- 固定 DPI 与窗口客户区，控件和卡片使用同一个坐标系。图标与文字读取系统字体；高对比度和辅助名称需要独立验证。

API 入口：[Win32 窗口](https://learn.microsoft.com/en-us/windows/win32/winmsg/windows)、[COM apartments](https://learn.microsoft.com/en-us/windows/win32/com/processes--threads--and-apartments)、[通知区域](https://learn.microsoft.com/en-us/windows/win32/shell/notification-area)、[自定义 DWM 框架](https://learn.microsoft.com/en-us/windows/win32/dwm/customframe)。
