# 官方来源

核对日期2026-11-01。以下为Microsoft官方API/项目入口；工程取舍和测试矩阵是本skill的归纳。

- [Direct2D入口](https://learn.microsoft.com/en-us/windows/win32/direct2d/direct2d-portal)
- [GDI互操作](https://learn.microsoft.com/en-us/windows/win32/direct2d/direct2d-and-gdi-interoperation-overview)
- [Direct2D资源域](https://learn.microsoft.com/en-us/windows/win32/direct2d/resources-and-resource-domains)
- [DPI](https://learn.microsoft.com/en-us/windows/win32/direct2d/direct2d-and-high-dpi)
- [DirectWrite](https://learn.microsoft.com/en-us/windows/win32/directwrite/direct-write-portal)
- [Rust for Windows](https://github.com/microsoft/windows-rs)、[API reference](https://microsoft.github.io/windows-docs-rs/)

0.58代码片段对应该版本的生成绑定和MSVC构建路径；运行性能必须由实际应用证明。较新上游可提供canvas/composition等focused crate，先核对发布、host与目标系统，不能凭API相似认定可直接替换。
