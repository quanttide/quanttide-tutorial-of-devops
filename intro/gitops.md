# GitOps

## 核心理念

GitOps 的核心是以 Git 为单一事实源。在量潮的实践中，这意味着**大量使用开源社区约定的特殊文件作为人机协同的事实源**。

人和机器都能读、能写这些文件：

- **contract.yaml**（契约四维模型）— scope、stage、platform、source 定义
- **ROADMAP.md**（Keep a Changelog + checkbox）— 规划阶段的事实源
- **TODO.md**（checkbox 任务清单）— 规划阶段的执行拆解
- **CHANGELOG.md**（Keep a Changelog）— 发布阶段的变更记录
- **Git tag**（SemVer）— 发布阶段的版本标记

这些文件遵循成熟的开源约定，人类可以直观理解，AI/CLI 工具也能按规则解析和修改——不需要额外的数据库、API 或平台绑定。

### 约定即协议，不需要额外 Schema

这些特殊文件用社区约定代替了自定义 DSL。Keep a Changelog 规定版本格式和分类，Markdown checkbox 标记完成状态，契约 YAML 的结构在社区项目中随处可见。LLM 在训练数据中看过无数遍同类格式，零样本就能解析，不需要维护一份专门的 Schema 定义。

## 实践方式

### 定义阶段

contract.yaml 定义 scope 的目录、语言、构建工具等配置。规划文件的路径由此决定：

```yaml
scopes:
  cli:
    dir: src/cli
    language: rust
    build_tool: cargo
```

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

传统 GitOps 把 Git 当**部署管道**用——仓库变更触发集群同步（push → sync），关注的是基础设施状态。

量潮的实践把 Git 当**协作总线**用——人、AI、CLI 读写同一份文件，靠 PR diff 协调变更、blame 追溯归因、branch 隔离进度。关注的是开发流程本身的透明和可追溯。

一个是 Git → 基础设施，一个是 Git → 开发流程事实源。
