# GitOps

## 核心理念

GitOps 的核心是以 Git 为单一事实源。在量潮的实践中，这意味着**大量使用开源社区约定的特殊文件作为人机协同的事实源**。

人和机器都能读、能写这些文件：

- **ROADMAP.md**（Keep a Changelog + checkbox）— 规划阶段的事实源
- **TODO.md**（checkbox 任务清单）— 规划阶段的执行拆解
- **CHANGELOG.md**（Keep a Changelog）— 发布阶段的变更记录
- **Git tag**（SemVer）— 发布阶段的版本标记

这些文件遵循成熟的开源约定，人类可以直观理解，AI/CLI 工具也能按规则解析和修改——不需要额外的数据库、API 或平台绑定。

## 实践方式

### 规划阶段

```text
ROADMAP.md  ←  版本目标和进度（人+AI 共同维护）
TODO.md     ←  具体执行拆解（人+AI 共同维护）
```

- 人以 Markdown checkbox 勾选完成状态
- AI 自动统计进度（`plan status`）、验证格式（`plan doctor`）、清理已完成的条目（`plan clean`）
- ROADMAP 的 `[x]` 条目在发布时迁移到 CHANGELOG

### 发布阶段

```text
CHANGELOG.md  ←  版本变更记录（人+AI 共同维护）
Git tag       ←  版本标记（CLI 自动创建）
```

- 发布前 AI 校验版本一致性和 CHANGELOG 完整性
- CLI 自动打标签、推送到远端、创建 Release

## 与传统 GitOps 的区别

传统 GitOps 强调基础设施的声明式部署（Kubernetes、Terraform），量潮的实践更侧重**开发流程的事实源化**——将规划、追踪、发布等阶段的核心信息沉淀为 Git 中遵循社区约定的特殊文件，让 AI 和 CLI 工具能像理解代码一样理解项目状态。
