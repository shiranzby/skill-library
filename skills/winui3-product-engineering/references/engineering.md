# WinUI 3 工程实现与边界

## 真实 WinUI 3 的基本范式

Windows App SDK 当前稳定版本和支持矩阵会随时间变化；在写项目文件前从官方 release channel 重新确认版本。下列代码是结构示例，不代表本机已经安装 SDK 或编译成功。

```csharp
private AppSettings _saved, _draft;
private bool _initialized, _syncing;
private Task _saveTask = Task.CompletedTask;

private void DimExternal_Toggled(object sender, RoutedEventArgs e)
{
    if (!_initialized || _syncing) return; // 初始化/回写不触发保存
    _draft = _draft with { DimExternal = DimExternal.IsOn };
    StartSave();
}
private void StartSave()
{
    if (_saveTask.IsCompleted) _saveTask = SaveLoopAsync();
}
private async Task SaveLoopAsync()
{
    while (_draft != _saved) // AppSettings 使用值相等的不可变 record
    {
        var requested = _draft;
        try
        {
            Status.Text = "Saving";
            await _store.SaveAtomicallyAsync(requested);
            _saved = requested; // 仅提交成功的版本；后续新草稿继续循环
            Status.Text = "Saved";
        }
        catch (Exception ex)
        {
            Error.Text = ex.Message; // 保留 _draft 与输入，重试调用 StartSave
            return;
        }
    }
}
private async Task FlushBeforeCloseAsync()
{
    StartSave();
    await _saveTask;
    if (_draft != _saved) throw new IOException("Settings are not saved");
}
```

上例只覆盖单进程配置队列；初始化应同时设置 saved/draft 后再启用通知。真实代码还应处理跨进程写入、取消、原子文件替换和多通道部分成功；取消不能回滚已经落盘的副作用。需要回 UI 线程时使用 `DispatcherQueue.TryEnqueue`；Windows App SDK 的标准 STA 不提供 UWP ASTA 的同等防重入保证。不要在 Toggle 事件里同步调用 WMI、DDC、网络或长时间文件 IO。

## 标题栏与单实例

- Windows App SDK 1.7 起有 `TitleBar` 控件；也可使用 `Window.ExtendsContentIntoTitleBar`、`Window.SetTitleBar` 和 `AppWindowTitleBar`。自定义标题栏要保留系统 caption buttons、拖拽区域、交互控件的 passthrough 和窗口缩放。
- 真 WinUI 单实例不是“检查进程名”：官方做法是在创建窗口前进行重定向，第二实例激活第一实例并传递启动参数。Rust/Win32 可用命名互斥体和窗口消息，但必须确认互斥生命周期、失败启动和旧实例唤起都正确。
- 托盘工具的“关闭窗口时隐藏/退出”要明确区分窗口生命周期与进程生命周期；隐藏后热键和托盘入口仍需可用。

## 版本与项目事实

- WinUI 3 随 Windows App SDK 提供，官方文档列出 C#、C++/WinRT 和 Rust（windows-rs API）等 Windows 开发路线；这不等于 Rust 可以直接编译 XAML WinUI 页面。
- Windows App SDK 的受支持 Windows 范围依版本和功能而定；每个SDK、打包方式和运行时部署均按官方矩阵核验，不把一个历史最低版本当成所有新API的保证。
- WinUI Gallery 的当前示例使用 `Microsoft.WindowsAppSDK`、Community Toolkit SettingsControls，并且仓库是 MIT；读取示例时记录 commit。不要复制其资源或把 Gallery 的依赖隐式加入业务项目。

## 仿 Fluent 的 Rust 适配

保留原有 Windows 风格时，推荐共享：配置模型、命令层、错误码、保存协议、焦点/键盘测试和几何检查。UI 框架可保持 Rust + windows crate；把 `SettingsCard` 当作布局概念，把 Toggle/Radio/NumberBox/TimePicker 当作语义合同。交付写清“视觉参考 Fluent，技术实现 Win32/Rust”，并提供未验证的 UIA、系统材质、窗口兼容边界。
