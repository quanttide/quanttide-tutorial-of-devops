# DevOps 实践理念

## 核心思想

量潮的 DevOps 实践围绕一条主线：**用 Git 中遵循社区约定的特殊文件，做人机协同的事实源**。

每一阶段的进度和状态都沉淀为代码仓库中的文件，而不是藏在项目管理工具、数据库或聊天记录里。人和 AI 共享同一套事实源。

## 四大特色

### AI 是一等公民

特殊文件（ROADMAP.md、TODO.md、CHANGELOG.md）遵循 Keep a Changelog、Markdown checkbox 等成熟社区规范。LLM 能稳定解析和修改这些格式，不只是"读"，还能参与维护——统计进度、验证格式、清理条目、生成 CHANGELOG。

AI 是协作流程中的参与者，不是事后接入的辅助工具。

### 全生命周期覆盖

不止在部署环节用 Git，而是从规划到发布的整条链路都有 Git 中的事实源：

```text
contract.yaml  →  定 scope
ROADMAP.md    →  定目标
TODO.md       →  拆执行
CHANGELOG.md  →  记历史
Git tag       →  发版本
```

### 约定即平台，零绑定

事实源是社区约定的文件格式（Keep a Changelog、SemVer、checkbox），不是 Jira、Notion、飞书多维表格。换 Git 平台不用迁移数据，改个远端地址就行。

约定相关，平台无关。

### 闭环在同一个仓库

合约、规划、执行、变更记录都在同一个仓库里，版本一起走，PR 一起审，不用跨系统追状态。

