# 应用状态、组件与平台

## 最小应用结构

审阅快照的公开 API 可以按以下结构组织。依赖版本/feature 与函数签名需一起锁定；本示例用于说明状态边界，不代表所有平台均已编译运行。

```toml
[dependencies]
zsui = { version = "=0.3.0-alpha.2", default-features = false,
         features = ["window", "button", "label"] }
```

```rust
use zsui::prelude::{button, column, text, window, Dp, Element, UpdateContext};

#[derive(Clone)]
enum Msg { Increment }
struct State { count: u32 }

fn view(state: &State) -> Element<Msg> {
    column([
        text(format!("Count: {}", state.count)),
        button("Increment").on_click(Msg::Increment),
    ]).gap(Dp::new(12.0)).padding(Dp::new(20.0))
}
fn update(state: &mut State, msg: Msg, _: &mut UpdateContext<'_>) {
    match msg { Msg::Increment => state.count += 1 }
}
fn main() -> Result<(), zsui::stable::Error> {
    window("Counter").size(480, 320)
        .stateful(State { count: 0 }, view, update).run()
}
```

## 组件路由

| 任务 | 评估的 feature | 重点验证 |
| --- | --- | --- |
| 设置/表单 | textbox、toggle、radio、number-box、slider、time-picker | 受控输入、非法草稿、保存失败、Tab/IME |
| 大集合 | virtual-list、paged-list、table、tree | 可见区实例化、稳定 ID、滚动与选择恢复 |
| 导航/文档 | shell、split-view、tabs、document-shell | 按内容预算重排，不能因“大屏”强加侧栏 |
| 系统工具 | tray、hotkey、clipboard、dialog、settings | 目标系统回调、冲突、隐藏唤起、关闭和清理 |
| 辅助功能 | accessibility；Linux Direct 另查 bridge | 名称/值/顺序、真实读屏、跨线程回调 |

默认 Windows 路线是 Win32 宿主、框架自绘，文档所述文字后端与 DirectWrite 参照存在明确差异；不要声称原生 WinUI 控件或字体完全等价。Mac/Linux 的视觉应读取各自 profile。Android 的 Activity 实现和真机验证在审阅快照仍有未完成项；手机和平板适配先核对目标宿主，再设计触区与软键盘路径。

## 存储与 effect

自动保存将 `draft`、`saved`、正在提交的 revision 分开。不要在 View 重建或初始化通知里写盘。保存队列按顺序提交最新草稿，失败保留编辑并显示重试；关闭先 flush 最后修改。跨线程任务只发消息，绘制上下文不进入 worker。图表、音频、远程设备等产品逻辑由应用 adapter 实现。
