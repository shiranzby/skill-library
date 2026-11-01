# 控件与设置页

## 选择控件

| 需求 | WinUI 3 控件 | 语义与验收 |
| --- | --- | --- |
| 立即生效的二元设置 | `ToggleSwitch` | 改变即执行；失败回滚并说明；标签简短、描述补充影响 |
| 多选或需要提交的勾选 | `CheckBox` | 选择不等于已经提交；保留草稿和取消语义 |
| 2–5 个互斥选项 | `RadioButtons` | 全部选项可见，键盘箭头导航；不要用两个 radio 表示简单开关 |
| 多于 5–8 个选项或首选项明显 | `ComboBox` | 保留当前值和可访问名称，避免把长集合堆成 radio |
| 数字与范围 | `NumberBox` | 用 `Value` 双向绑定、范围校验、错误反馈；清空时处理 NaN |
| 单个时间 | `TimePicker` | 使用 `SelectedTime`；24 小时制设置 `ClockIdentifier="24HourClock"`；验证空值与分钟步长 |
| 文本、热键或格式化值 | `TextBox`/自定义输入 | 标题持久可见；组合输入、取消、非法值、失败保稿分别测试 |

## 设置页结构

真实 WinUI 3 需要引入 `CommunityToolkit.WinUI.Controls.SettingsControls` 后使用 `CommunityToolkit.WinUI.Controls` 命名空间。Toolkit 的 `SettingsCard` 是辅助控件，不是 `Microsoft.UI.Xaml.Controls` 内置类；不要把它写成 WinUI 核心 API。

```xml
<ScrollView VerticalScrollBarVisibility="Auto">
  <StackPanel MaxWidth="1064" Spacing="4">
    <TextBlock Text="Screen" Style="{StaticResource BodyStrongTextBlockStyle}"/>
    <toolkit:SettingsCard Header="Dim external display"
                          Description="Uses DDC/CI when supported">
      <ToggleSwitch x:Name="DimExternal" Toggled="DimExternal_Toggled"/>
    </toolkit:SettingsCard>
    <toolkit:SettingsCard Header="Restore delay">
      <NumberBox x:Name="RestoreDelay" Minimum="0" Maximum="500"
                 SmallChange="10" Value="140" ValueChanged="RestoreDelay_ValueChanged"/>
    </toolkit:SettingsCard>
    <toolkit:SettingsCard Header="Wake time">
      <TimePicker x:Name="WakeTime" ClockIdentifier="24HourClock"
                  MinuteIncrement="1" SelectedTime="07:00"/>
    </toolkit:SettingsCard>
  </StackPanel>
</ScrollView>
```

使用 SettingsExpander 收纳低频、进阶或 About 内容，最多嵌套一层。开关、单选和输入框要有独立的 Header/AutomationProperties.Name；整卡可点击时必须防止内部按钮重复触发。

## Rust/Win32 对照

Rust 版可以用原生 Win32 控件或自绘卡片复现上面的信息结构。应把每个设置映射成稳定 ID、命令和保存状态，避免让绘制函数直接写注册表、WMI、DDC 或配置文件。原生 Win32 控件不会自动获得 WinUI 的 UIA、主题资源、DPI 和键盘行为，需要单独验收或声明降级。
