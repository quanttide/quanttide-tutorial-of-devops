# 计划

本节介绍如何用 `qtcloud-devops plan` 命令管理版本规划。

## 核心理念：规划即文档

规划不只是在项目管理工具里建任务。在量潮 DevOps 流程中，规划的核心产物是 **ROADMAP.md**——一份与代码同仓的规划文件，伴随代码一起维护。

ROADMAP 回答三个问题：

- **当前版本要做什么** — 未完成的 checkbox
- **已经做了什么** — 已完成的 checkbox
- **下个版本打算做什么** — 下一个版本标题下的条目

格式说明详见 [ROADMAP 格式](roadmap.md)。

## 操作流程

### 1. 创建 ROADMAP

在新 scope 根目录创建 ROADMAP.md，列出当前版本和下个版本的计划条目：

```markdown
# ROADMAP

## [0.1.0]

### Added
- [ ] project initialization
- [ ] basic CRUD API
- [ ] unit tests

## [0.2.0]

### Added
- [ ] user authentication
- [ ] rate limiting
```

### 2. 查看进度

```bash
# 查看当前 scope 的规划进度
qtcloud-devops plan status

# 查看指定 scope 的规划进度
qtcloud-devops plan status cli
```

`scope` 省略时自动检测当前目录所属 scope。

输出示例：

```text
[cli] 规划进度
────────────────────────────────────────
  [0.2.0]      1/ 4 完成 (25%)
  [0.1.0]      8/ 8 完成 (100%)
────────────────────────────────────────
  总计:  9/12 完成 (75%)
```

### 3. 验证格式

写入或修改 ROADMAP 后，验证格式是否正确：

```bash
qtcloud-devops plan doctor [scope]
```

只读检查，不会修改文件。发现问题后手动修复，或交由 LLM 协助修复。常见格式问题清单见 [ROADMAP 格式](roadmap.md#常见格式问题)。

### 4. 清理已完成条目

提交前清理已完成的条目，保持 ROADMAP 整洁：

```bash
qtcloud-devops plan clean [scope]
```

效果：删除所有 `[x]` 条目，若分类下无条目则删除空分类。

## 日常循环

规划不是一次性工作，而是伴随每次迭代持续更新：

```text
开发前  → plan status        了解当前版本还差什么
开发中  → 完成条目后勾选 [x]    实时标记进度
提交前  → plan doctor        验证格式
       → plan clean         清理已完成条目
       → git commit         提交代码和 ROADMAP 一起
开发后  → plan status        回顾进度，规划下个版本
```

### 示例场景

**场景：开始新版本 v0.2.0 开发**

```bash
# 1. 看看当前进度
qtcloud-devops plan status
# 输出: [0.2.0] 0/3 完成 — 新版本刚启动

# 2. 实现 feature-a，完成后标记 ROADMAP
# 在 ROADMAP.md 中将 [ ] feature a 改为 [x] feature a

# 3. 提交前检查和清理
qtcloud-devops plan doctor        # 验证格式
qtcloud-devops plan clean         # 清理已完成条目

# 4. 提交代码与规划
git add ROADMAP.md src/
git commit -m "feat: implement feature a"
```

## 常见问题

**ROADMAP 和 CHANGELOG 有什么区别？**

ROADMAP 面向未来——规划要做什么；CHANGELOG 记录历史——已经做了什么。ROADMAP 的 `[x]` 条目在发布时会迁移到 CHANGELOG。

**scope 是什么？**

scope 是量潮项目中可独立规划、构建和发布的组件。例如 `cli`、`web`、`api` 各是一个 scope，每个 scope 有自己的 ROADMAP.md。

**能否用项目管理工具替代 ROADMAP？**

ROADMAP 与代码同仓，确保规划与实现同步。推荐二者结合使用：项目管理工具（如飞书多维表格）用于跨团队协作和长期规划，ROADMAP 用于版本级粒度规划。
