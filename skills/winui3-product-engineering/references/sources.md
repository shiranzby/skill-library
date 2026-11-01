# 官方来源与阅读记录

阅读日期：2026-11-01。链接是事实依据；本 skill 的建议和测试矩阵是适配层，不是微软的原文要求。

## Microsoft Learn

- [Design Windows apps](https://learn.microsoft.com/en-us/windows/apps/design/)：Fluent 设计入口、布局、颜色、字体、材质、动效、辅助功能。
- [WinUI 3](https://learn.microsoft.com/en-us/windows/apps/winui/winui3/)：WinUI 3 随 Windows App SDK 提供，原生桌面框架、C#/C++ 和 Windows 支持范围。
- [Windows 11 design principles](https://learn.microsoft.com/en-us/windows/apps/design/design-principles)：Effortless、Calm、Personal、Familiar、Complete + Coherent。
- [Design guidelines](https://learn.microsoft.com/en-us/windows/apps/design/guidelines-overview)、[color](https://learn.microsoft.com/en-us/windows/apps/design/signature-experiences/color)、[geometry](https://learn.microsoft.com/en-us/windows/apps/design/signature-experiences/geometry)、[materials](https://learn.microsoft.com/en-us/windows/apps/design/signature-experiences/materials)、[motion](https://learn.microsoft.com/en-us/windows/apps/design/signature-experiences/motion)、[typography](https://learn.microsoft.com/en-us/windows/apps/design/signature-experiences/typography)。
- [Layout overview](https://learn.microsoft.com/en-us/windows/apps/design/layout/)、[responsive design](https://learn.microsoft.com/en-us/windows/apps/design/layout/responsive-design)、[screen sizes and breakpoints](https://learn.microsoft.com/en-us/windows/apps/design/layout/screen-sizes-and-breakpoints-for-responsive-design)。
- [Guidelines for app settings](https://learn.microsoft.com/en-us/windows/apps/design/app-settings/guidelines-for-app-settings)：SettingsCard/SettingsExpander、分组、滚动、即时保存语义。
- [Toggle switches](https://learn.microsoft.com/en-us/windows/apps/develop/ui/controls/toggles)、[RadioButtons](https://learn.microsoft.com/en-us/windows/apps/develop/ui/controls/radio-button)、[NumberBox](https://learn.microsoft.com/en-us/windows/apps/develop/ui/controls/number-box)、[TimePicker](https://learn.microsoft.com/en-us/windows/apps/develop/ui/controls/time-picker)。
- [Title bar design](https://learn.microsoft.com/en-us/windows/apps/design/basics/titlebar-design)、[Title bar customization](https://learn.microsoft.com/en-us/windows/apps/develop/title-bar)、[TitleBar control](https://learn.microsoft.com/en-us/windows/apps/develop/ui/controls/title-bar)：系统标题栏、拖拽区、caption buttons 和交互区域。
- [Accessibility overview](https://learn.microsoft.com/en-us/windows/apps/design/accessibility/accessibility-overview)、[Accessibility testing](https://learn.microsoft.com/en-us/windows/apps/design/accessibility/accessibility-testing)、[Keyboard interactions](https://learn.microsoft.com/en-us/windows/apps/develop/input/keyboard-interactions)。
- [Threading functionality migration](https://learn.microsoft.com/en-us/windows/apps/windows-app-sdk/migrate-to-windows-app-sdk/guides/threading)：Windows App SDK STA、DispatcherQueue 和重入边界。
- [Single-instanced WinUI app](https://learn.microsoft.com/en-us/windows/apps/windows-app-sdk/applifecycle/applifecycle-single-instance)：第二实例重定向与第一窗口激活。
- [Windows App SDK release channels](https://learn.microsoft.com/en-us/windows/apps/windows-app-sdk/stable-channel)、[supported Windows releases](https://learn.microsoft.com/en-us/windows/apps/windows-app-sdk/support)：版本与支持矩阵，交付时重新确认。
- [SettingsCard](https://learn.microsoft.com/en-us/dotnet/communitytoolkit/windows/settingscontrols/settingscard)、[Windows Community Toolkit](https://learn.microsoft.com/en-us/dotnet/communitytoolkit/windows/)：SettingsCard/Expander 的外部 Toolkit 来源与命名空间。

## 官方示例与许可证

- [microsoft/WinUI-Gallery](https://github.com/microsoft/WinUI-Gallery)，阅读 commit `7360ac322414e118eadfb0c7c2f4ecc31a218f59`（2026-10-27）；抽查 `WinUIGallery/Pages/SettingsPage.xaml`、SettingsPage.xaml.cs、AppWindowTitleBar 和控件示例。
- Gallery 的 [LICENSE](https://github.com/microsoft/WinUI-Gallery/blob/7360ac322414e118eadfb0c7c2f4ecc31a218f59/LICENSE) 是 MIT。这里只借鉴结构和 API 用法，不复制业务资源或把它当作当前项目运行证据。
