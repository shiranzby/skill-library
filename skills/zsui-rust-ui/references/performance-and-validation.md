# 性能与目标平台验收

先建立当前应用 release 基线，再考虑减少 feature、延迟系统服务、虚拟化列表、边界清晰的缓存与隐藏时释放视图资源。上游的固定场景测量只能说明该场景，不能直接当作你的应用内存。

- 共享只读字体与窗口私有 mutable glyph/layout 缓存要分开；字体映射、文字段、渲染帧和 UIA 快照必须有可追踪所有者。
- 绘制缓冲预算包括尺寸、DPI、stride、临时峰值及系统表面。小 scratch buffer 不代表进程总内存小；把一次完整更新拆成多次呈现可能产生撕裂。
- 字体优化先核对实际 face、fallback、glyph/cluster、换行、基线，再看像素。中文、RTL、emoji、粗体、选择与 IME 不能被“更低占用”忽略。
- 上游文字对照报告保留若干 fallback/字重差异；读取所选版本的实际报告，不能将 DirectWrite 用作 oracle 等同于完全一致。
- 真实生命周期覆盖 visible/hidden/restored、50次循环、多窗口与混合DPI，分别记录 private bytes、working set、GDI/USER、句柄、绘制耗时和峰值。

验收分为编译/契约、目标平台真实窗口、系统集成三层。Windows 运行不能证明 AppKit、Linux、Android 或读屏已通过。先复现输入、焦点、剪贴板、原生菜单、托盘与文件对话框，再做阶段全量回归。截图保留当前构建标识、场景、窗口/DPI和状态。
