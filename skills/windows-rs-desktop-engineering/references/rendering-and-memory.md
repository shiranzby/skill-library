# 绘制路线与占用

| 路线 | 适合场景 | 实施约束 |
| --- | --- | --- |
| 原生控件 + GDI | 小型设置页、托盘工具、常规表单 | 原生输入/焦点保留，自绘语义另验；透明、圆弧和复杂文字仍需核对 |
| Direct2D + GDI DC | 逐步引入平滑矢量绘制 | `ID2D1DCRenderTarget` 可接现有 HDC，注意 pixel format、BindDC、EndDraw 和目标失效 |
| Direct2D + DirectWrite | 大量文本、缩放、矢量或自定义复杂界面 | shaping/字体回退、布局命中与实际绘制共用度量；设备资源按生命周期重建 |
| focused window/canvas/composition crates | 新项目需上游更高层封装时 | 查当前发布版本、host feature 和运行要求，不能套到旧锁定版本 |

Direct2D 与 DirectWrite 是绘制/文字服务，WinUI 3 是控件框架；采用前者仍需自己完成布局、输入、主题、UIA 等。

## 原生控件自绘的重绘边界

- 原生按钮可在 `WM_SETTEXT`、`BM_SETSTATE` 等处理期间直接绘制。仅接管 `WM_PAINT` 时，检查这些变化后是否又出现默认主题外观；可采用 owner-draw，或在相关消息完成后使自绘区域失效。不要给每条消息附加全窗重画。
- 移动一组子控件后，要验证父窗自绘标题和子控件的失效区域完整；必要时在布局完成、状态借用已释放后重绘整个窗口层级。
- 自绘滑块的 thumb 若大于原生 thumb，原生损坏区域可能清不掉旧形状；应覆盖自绘的旧/新边界，或在位置变化后使完整控件失效。
- 同一状态在连续编辑、取消捕获和关闭对话框后截图，再与完整重绘后的图像对照。标题消失、按钮颜色回退和滑块残影可以在功能断言全部通过时仍然存在。

跨进程测试不要把测试进程的 `RECT*` 等指针直接放进控件的 `WM_USER` 消息。系统只对系统消息范围自动封送；`SendMessageTimeout` 不会补上应用消息的参数封送。优先使用窗口几何/可访问接口、标量查询或明确的测试 IPC。见 [SendMessage 的跨进程规则](https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-sendmessage)。

## 内存比较方法

固定 release 构建、架构、数据、显示器/DPI 和采样阶段。分别记录冷启动托盘、首次面板、隐藏、恢复、重复循环与长时空闲。至少记录：

- Working set（含共享映像页）、Private bytes/commit、峰值；不要把进程工作集全部当 Rust 堆。
- GDI/USER 对象、句柄与线程；确认循环后的资源所有权稳定。
- 有 GPU 时另记 dedicated/shared GPU memory；不能只看 CPU 私有内存。
- 二进制大小、冷启动和首次交互延迟；延迟初始化应同时测首次使用代价。

只有真正用到才连接 COM 提供程序、加载字体库或创建设备；恢复记录存在时立即走恢复路径。可读字体缓存、控件布局、画刷和 scratch surface 设置明确所有者与上限。不要通过强制 working-set trimming 隐藏驻留占用，也不要为省缓存牺牲 IME、字形回退或帧完整性。

Direct2D 的进程开销需按当前应用实测；小型面板不预设 GPU 绘制会降低内存。缓存资源属于渲染目标，目标丢失后全部重新创建；隐藏时可按重建成本释放设备缓存。

来源：[GDI 互操作](https://learn.microsoft.com/en-us/windows/win32/direct2d/direct2d-and-gdi-interoperation-overview)、[DirectWrite](https://learn.microsoft.com/en-us/windows/win32/directwrite/direct-write-portal)、[GetProcessMemoryInfo](https://learn.microsoft.com/en-us/windows/win32/api/psapi/nf-psapi-getprocessmemoryinfo)、[GetGuiResources](https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-getguiresources)。
