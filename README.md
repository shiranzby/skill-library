# Personal Skill Library

个人可复用的 Codex Skill 仓库。每个 Skill 保持独立目录，入口文件为 `SKILL.md`；项目专属业务、账号、路径和历史测试证据不放进通用 Skill。

## 当前 Skill

### `swiss-product-engineering`

以瑞士国际主义的网格、基线和信息秩序为主，以 Apple 的可逆交互、焦点、反馈和无障碍细节为辅，覆盖：

- 页面／布局／主题／动效与 21 类常用控件；
- 设计风、数据风、卡片风的任务差异，以及窄屏单按钮模式切换；
- 长域名／URL 的完整展示、共同内边缘、抽屉动效与整行交互语义；
- 前端、后端或 IPC、存储、外部适配和异步状态边界；
- 单元、契约、真实交互、持久化、几何、目视与实网分层验收；
- 真实前端构建的静态演示、生产发布证据和应用鉴权边界；
- 日用、开发、测试、候选的隔离、回滚和交付表达。

调用方式：

```text
使用 $swiss-product-engineering，按当前项目需求设计并实现……
```

Skill 目录：`skills/swiss-product-engineering/`

## 校验

在 Skill 目录变更后运行：

```powershell
python C:/Users/Administrator/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/swiss-product-engineering
```

该命令校验 YAML、命名和未完成占位符；它不替代当前产品的构建、交互和视觉验收。
