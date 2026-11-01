# SwiftUI 工程范式

本页是可迁移的代码结构，不是对某个 SDK 版本的编译承诺。开始实现前核对 [SwiftUI](https://developer.apple.com/documentation/swiftui)、[Observation](https://developer.apple.com/documentation/observation)、目标 deployment target 和对应系统 API。示例使用较稳定的 `ObservableObject`/`@Published` 形态，若项目已采用 Observation，可等价迁移。

## 状态要由一个模型拥有

把界面状态、持久化状态和系统副作用分开；子视图只通过 `Binding` 或意图方法修改状态。不要让每一行同时读写 UserDefaults、网络和 UI。

```swift
import Combine
import Foundation

enum LoadPhase<Value> {
    case idle
    case loading(previous: Value?)
    case loaded(Value)
    case failed(previous: Value?, message: String)
}

extension LoadPhase {
    var value: Value? {
        switch self {
        case .idle: return nil
        case .loading(let previous), .failed(let previous, _): return previous
        case .loaded(let value): return value
        }
    }
}

@MainActor
final class SettingsViewModel: ObservableObject {
    @Published private(set) var phase: LoadPhase<Settings> = .idle
    @Published private(set) var draft = Settings()
    @Published private(set) var saveMessage: String?

    private let repository: SettingsRepository
    private var loadTask: Task<Void, Never>?
    private var saveTask: Task<Void, Never>?
    private var revision = 0
    private var loadID = UUID()

    init(repository: SettingsRepository) { self.repository = repository }

    func refresh() {
        loadTask?.cancel()
        let requestID = UUID()
        loadID = requestID
        let editingRevision = revision
        let previous = draft
        phase = .loading(previous: previous)
        loadTask = Task { [weak self] in
            guard let self else { return }
            do {
                let value = try await repository.load()
                try Task.checkCancellation()
                guard requestID == loadID, editingRevision == revision else { return }
                draft = value
                phase = .loaded(value)
            } catch is CancellationError {
                // 取消不是错误，不覆盖当前输入。
            } catch {
                guard requestID == loadID, editingRevision == revision else { return }
                phase = .failed(previous: previous, message: error.localizedDescription)
            }
        }
    }

    func edit(_ change: (inout Settings) -> Void) {
        loadTask?.cancel()
        change(&draft)
        phase = .loaded(draft)
        revision += 1
        let request = revision
        let value = draft
        saveMessage = "保存中…"
        saveTask?.cancel()
        saveTask = Task { [weak self] in
            guard let self else { return }
            do {
                // debounce 只减少写入，不是保存成功的证明。
                try await Task.sleep(nanoseconds: 250_000_000)
                try await repository.save(value, revision: request)
                try Task.checkCancellation()
                guard request == revision else { return }
                saveMessage = "已保存"
            } catch is CancellationError {
                // 新编辑或视图消失时，旧写入可取消。
            } catch {
                guard request == revision else { return }
                saveMessage = "保存失败：\(error.localizedDescription)"
                // draft 保留，界面应提供重试而不是抹掉用户输入。
            }
        }
    }
}
```

上面是模型片段，`Settings` 和 `SettingsRepository` 由项目实现；不是可直接粘贴编译的完整应用。`Settings` 若跨 actor 传递，应满足目标 Swift 并发模式的 `Sendable` 要求。`loadID` 与编辑 revision 一起阻止旧刷新覆盖新编辑。

`SettingsRepository` 应在 actor、后台线程或系统异步 API 中执行文件/网络/数据库操作。**UI 的 token 检查只保证显示最新，不保证磁盘或服务器最终保存最新。** Repository 必须实现相同的版本契约：

- 单一拥有者生成单调 revision；多个窗口应共享拥有者或使用数据库/服务器版本，不能各自从零计数写同一文件。
- 串行提交，并在真正写入点拒绝落后 revision；不能仅在任务创建时检查。成功提交后才记录该 revision，失败保留 draft 并让重试使用最新值。
- 文件先写同目录临时文件、刷新并原子替换；网络使用服务器版本/幂等键。底层不可取消时也必须防止旧请求晚到覆盖新值，不能把 `Task.cancel()` 当作写入已撤销。
- 关闭设置页/应用前若有待保存值，按产品约定 flush 或提示未保存；SwiftUI `.task` 的视图生命周期不应默默丢弃持久化写入。

## 加载、刷新和错误恢复

SwiftUI 的 `.task(id:)` 会在 id 变化或视图离开时取消任务，适合搜索和详情页。保留旧数据可以避免界面在刷新时闪成空白：

```swift
struct ResultView: View {
    let query: String
    let client: SearchClient
    @State private var phase: LoadPhase<[SearchItem]> = .idle
    @State private var attempt = UUID()

    var body: some View {
        content
            .task(id: query) { await load() }
    }

    @ViewBuilder private var content: some View {
        switch phase {
        case .idle: Text("准备加载")
        case .loading(let previous):
            if let previous {
                List(previous.indices, id: \.self) { index in
                    Text(String(describing: previous[index]))
                }
            }
            else { ProgressView("加载中…") }
        case .loaded(let values):
            List(values.indices, id: \.self) { index in
                Text(String(describing: values[index]))
            }
        case .failed(let previous, let message):
            VStack {
                if let previous {
                    List(previous.indices, id: \.self) { index in
                        Text(String(describing: previous[index]))
                    }
                }
                Text(message)
                Button("重试") { Task { await load() } }
            }
        }
    }

    @MainActor private func load() async {
        let requestID = UUID()
        attempt = requestID
        let requestQuery = query
        let old = phase.value
        phase = .loading(previous: old)
        do {
            let values = try await client.search(requestQuery)
            try Task.checkCancellation()
            guard attempt == requestID, requestQuery == query else { return }
            phase = .loaded(values)
        } catch is CancellationError {
            // 视图离开或 query 改变，不显示错误。
        } catch {
            guard !Task.isCancelled, attempt == requestID, requestQuery == query else { return }
            phase = .failed(previous: old, message: error.localizedDescription)
        }
    }
}
```

`SearchClient` 与 `SearchItem` 为项目类型，示例省略服务实现；渲染时应使用稳定业务 ID（示例用 indices 仅展示状态切换），并给空数组单独的无结果提示。`.task(id:)` 的自动取消只覆盖它启动的任务；上例重试按钮创建的是独立任务，因此还用 attempt 身份丢弃迟到结果，生产实现也可把重试合并到同一个受控任务。

## 取消、确认和不可逆动作

- 把取消传到 API：`try Task.checkCancellation()`、取消 URLSession task、停止媒体/传感器，并在 UI 中回到可操作状态。
- 破坏性动作先展示对象、范围和后果；确认后再执行。可撤销动作执行后给 Undo/恢复入口，避免为普通开关增加确认弹窗。
- 请求权限时先说明真实用途，再调用系统授权 API；不要用自绘“Allow”按钮伪装系统弹窗。

## UndoManager 范式

将撤销注册在产生变化的模型上，确保每次新编辑都记录旧值，且动作名能让菜单和 VoiceOver 读懂：

```swift
@MainActor
final class EditorModel: NSObject, ObservableObject {
    @Published private(set) var title = ""

    func setTitle(_ value: String, undoManager: UndoManager?) {
        let old = title
        guard old != value else { return }
        title = value
        undoManager?.registerUndo(withTarget: self) { target in
            target.setTitle(old, undoManager: undoManager)
        }
        undoManager?.setActionName("修改标题")
    }
}
```

在 SwiftUI 视图中通过 `@Environment(\.undoManager)` 取得当前环境的 UndoManager；没有可用管理器时仍应完成操作。测试撤销、重做、连续编辑、取消编辑和持久化顺序，避免把未保存的临时输入误当作可撤销的已提交操作。

## SwiftUI 代码边界

- `@State` 只拥有视图局部值；跨视图共享状态放到模型或环境；`@Binding` 表示单向拥有者之外的可编辑投影。
- 不在 `body` 中启动不可控的副作用；用 `.task(id:)`、明确的生命周期回调或模型方法，并使其可取消/可重复。
- 任何示例是否能编译取决于 deployment target、import、`Sendable` 约束和项目类型；在目标 SDK 上运行 `swiftc`/`xcodebuild` 和 UI 测试后才能宣称通过。
