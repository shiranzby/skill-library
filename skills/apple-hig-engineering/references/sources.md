# 来源和覆盖说明

## 官方入口

- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines)
- [Getting started](https://developer.apple.com/design/human-interface-guidelines/getting-started)
- [Design principles](https://developer.apple.com/design/human-interface-guidelines/design-principles)
- [Foundations](https://developer.apple.com/design/human-interface-guidelines/foundations)
- [Patterns](https://developer.apple.com/design/human-interface-guidelines/patterns)
- [Components](https://developer.apple.com/design/human-interface-guidelines/components)
- [Inputs](https://developer.apple.com/design/human-interface-guidelines/inputs)
- [Technologies](https://developer.apple.com/design/human-interface-guidelines/technologies)
- [SwiftUI documentation](https://developer.apple.com/documentation/swiftui)
- [Apple accessibility documentation](https://developer.apple.com/accessibility/)

## 本次阅读快照

- 抓取日期：2026-11-01。
- 官方 DocC 目录：173 个页面；每页保存标题、URL、字数、SHA-256 和抓取状态。
- 原始核对缓存位于本项目的 `checks/reference-cache/apple/`，包括 `coverage.json`、分区阅读块和阅读进度。缓存用于覆盖核对，不作为 skill 的运行时依赖，也没有把 Apple 原文复制到此目录。
- 早期把部分目录/开头核对误记为全文阅读的结论已撤回；随后 A 分区86页按两组43页重新核读，B 分区87页逐批补读，其中最后13页由主代理完成。遇到工具截断的段落已单独补读。
- 当前173页的**文本快照**均完成正文、平台分支、表格、图片替代说明、资源和变更表核读；逐页原创提炼见 [A记录](hig-readings-a.md)、[B记录](hig-readings-b.md)，URL/行数/SHA-256见 [覆盖清单](hig-coverage.json)。不把这一结果扩张为所有图片/视频、链接的SDK文档或未来更新已读。
- 代码范式是本 skill 的原创示例，必须在目标 SDK 上重新编译；本次没有 Apple SDK 构建或设备运行证据。

## 事实和建议的分界

- 带官方链接的 API、平台、权限、尺寸或组件描述是来源事实的摘要；页面内容可能随 Apple 更新，实施前应重新打开链接并核对最低系统版本。
- `design-principles.md`、`components-and-inputs.md`、`platform-routing.md` 中的流程、检查表和跨平台迁移措辞是工程建议，不是 Apple 认证或审核承诺。
- 本 skill 不授予 Apple API entitlement，不替代 App Review Guidelines、隐私政策、SDK 文档、法律/医疗/支付要求或真实设备测试。
