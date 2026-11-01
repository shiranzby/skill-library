# 画质与资源验收

- 同一字体、窗口客户区和DPI比较GDI/Direct2D；分别检查直线pixel snapping、圆弧抗锯齿、文字基线和透明边缘。
- 100/125/150/175/200%及混合显示器移动，最大化、还原和连续缩放；DIP和物理像素都记录。
- hover/pressed/focus/selected/disabled、错误、输入草稿和滚动中间态均截图。截图只能验证被捕获的内容；排除捕获的覆盖窗与物理背光应另取证。
- 中文组合输入、RTL、emoji、混合字重、fallback、选择和caret；几何相同不自动证明像素等价，像素近似也不掩盖错误glyph。
- BeginDraw/EndDraw错误、目标重建、最小化/失焦/设备变化、HWND关闭；资源失败保留可操作回退。
- 内存报告至少列private commit、working set、GDI/USER、句柄、CPU/GPU表面与峰值。原始像素缓冲估算 `stride × physical_height`，并计入双缓冲、atlas、布局与驱动。

绘制目标/画刷可复用，但缓存要有上限。比较冷启动、首次绘制、稳定显示、隐藏/恢复循环；至少重复采样并说明噪声。不要以强制working-set trim制造更小截图，也不把CPU软件目标标成GPU加速。
