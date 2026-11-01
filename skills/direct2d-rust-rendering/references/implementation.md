# 生命周期、版本和代码

## 绘制状态机

```text
创建UI线程 -> 创建设备无关factory
首次绘制 -> 目标/画刷 -> BindDC或关联HWND
BeginDraw -> 按同一DIP坐标布局/绘制 -> EndDraw检查
尺寸/DPI变化 -> 更新绑定/尺寸及布局
目标失效 -> Drop目标及其资源 -> 下次绘制重建
窗口退出/闲置释放 -> Drop明确拥有的缓存
```

若用 Rust `thread_local!` 缓存 Direct2D 的 COM 资源，应在窗口线程的正常退出路径显式清空缓存，再结束线程。不要仅依赖进程退出时的 TLS 析构；这时 Windows 已可能进入线程/图形运行时清理。一次桌面应用对照中，正常退出与恢复后退出均在晚期清理返回异常，提前 `take()`/`drop()` 后两条路径均正常结束。这个结果支持资源释放顺序的修复，不代表所有退出异常都有同一原因。

```rust
// 消息循环结束、窗口停止绘制后，在原 UI 线程调用。
fn release_renderer() {
    PAINTER.with(|cell| drop(cell.borrow_mut().take()));
}
```

文字使用DirectWrite时，布局和命中应引用同一文本、字体、locale与宽度约束。不要拿GDI测量后让DirectWrite独立换行，再用固定字符宽度定位光标。

## HDC目标示例

以下片段采用 `windows = 0.58` 的已知签名，只表达目标创建；嵌入消息循环后才是应用。新版本先核对命名空间与feature，不将0.58样式粘到0.100项目。

```toml
windows = { version = "0.58", features = [
  "Foundation_Numerics", "Win32_Foundation", "Win32_Graphics_Gdi",
  "Win32_Graphics_Direct2D", "Win32_Graphics_Direct2D_Common",
  "Win32_Graphics_Dxgi_Common"
] }
```

```rust
use windows::Win32::Graphics::{Direct2D::*, Direct2D::Common::*,
    Dxgi::Common::DXGI_FORMAT_B8G8R8A8_UNORM};

unsafe fn create_target() -> windows::core::Result<(ID2D1Factory, ID2D1DCRenderTarget)> {
    let factory: ID2D1Factory = D2D1CreateFactory(D2D1_FACTORY_TYPE_SINGLE_THREADED, None)?;
    let target = factory.CreateDCRenderTarget(&D2D1_RENDER_TARGET_PROPERTIES {
        r#type: D2D1_RENDER_TARGET_TYPE_SOFTWARE,
        pixelFormat: D2D1_PIXEL_FORMAT {
            format: DXGI_FORMAT_B8G8R8A8_UNORM, alphaMode: D2D1_ALPHA_MODE_IGNORE,
        },
        dpiX: 96.0, dpiY: 96.0,
        usage: D2D1_RENDER_TARGET_USAGE_NONE, minLevel: D2D1_FEATURE_LEVEL_DEFAULT,
    })?;
    Ok((factory, target))
}
```

随后按每次DC/矩形调用 `BindDC`，BeginDraw/EndDraw之间只做当前帧操作。opaque DC必须先定义背景；半透明窗口、复杂clip和RTL需要独立路径与测试。此处软件目标用于对照，真实选择可由测量决定；它仍会加载Direct2D运行时并分配缓存。
