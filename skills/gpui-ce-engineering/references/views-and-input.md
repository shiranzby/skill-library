# 状态、组件与平台

以下是当前审阅 API 的视图片段，需放入具备应用初始化的项目；它不是独立可运行文件。

```rust
use gpui::{Context, IntoElement, Render, Window, div, prelude::*};

struct Counter { value: u32 }
impl Render for Counter {
    fn render(&mut self, _: &mut Window, cx: &mut Context<Self>) -> impl IntoElement {
        div().id("counter").flex().items_center().gap_2()
            .child(format!("Count: {}", self.value))
            .child(div().id("increment").child("Increment")
                .on_click(cx.listener(|this, _, _, cx| {
                    this.value += 1;
                    cx.notify();
                })))
    }
}
```

此片段表达 entity 拥有状态和显式失效，尚未实现一个完整按钮：真实产品还需按钮角色/名称、焦点、Enter/Space、禁用状态、hover/pressed，以及独立 action 命令。不要用 `div().on_click()` 就宣称无障碍控件完成。

## 键盘与输入

- typed actions + `KeyBinding` 通过 key context 限定作用域；`track_focus` 与长期持有的 focus handle 配合。组件被重排或隐藏后焦点可恢复。
- 文字输入读取所选版本的 IME/selection/text-input 示例。中文组合输入、候选位置、剪贴板、撤销、RTL、emoji和多行选择需在目标系统实测。
- 输入框保留草稿；后台保存有 saved/revision/error 状态。渲染刷新不得覆盖编辑中的内容；关闭先处理在途写入与恢复。

## 视觉与布局

Fluent 风格从语义颜色、字体、间距、4/8 epx几何、标题栏与表单语义建立组件库；Apple风格则按桌面/手机/平板规则路由。GPUI的 flex/Tailwind式 API 不是网页DOM，也不自动拥有浏览器可访问行为。

按窗口宽度组合 sidebar、内容、inspector；优先约束重排和虚拟化，不靠固定窗口型号。GPU 后端不证明手机/平板生命周期可用；目标宿主未完成时只评估桌面/窗口化触控路径。
