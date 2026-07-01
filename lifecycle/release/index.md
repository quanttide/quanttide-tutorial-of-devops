# 发布

本节介绍如何手动完成一次版本发布。如果你使用量潮DevOps云命令行工具，这些步骤会自动执行。

## 版本号规则

遵循语义化版本规范（SemVer）。正式版格式 `vX.Y.Z`，预发布版按开发阶段依次使用 `-alpha.N`、`-beta.N`、`-rc.N` 后缀。

## 操作前检查

确认以下事项后再执行：

- 最新标签与配置文件版本一致
- CHANGELOG 条目已存在
- 工作区干净（无未提交变更）

## 操作流程

### 1. 更新版本号

修改配置文件中的版本字段。

以 Python 项目为例（`pyproject.toml`）：

```diff
- version = "0.1.0"
+ version = "0.2.0"
```

### 2. 更新 CHANGELOG

在 `CHANGELOG.md` 顶部添加当前版本的变更记录：

```markdown
## [0.2.0] - 2026-06-29

### Added
- 新功能描述

### Fixed
- 修复描述
```

格式遵循 Keep a Changelog 规范。变更按 Added、Changed、Fixed、Removed 分类。

### 3. 提交修改

```bash
git add pyproject.toml CHANGELOG.md
git commit -m "chore: bump to v0.2.0"
```

### 4. 打标签

```bash
git tag v0.2.0
git push origin v0.2.0
```

标签格式为 `vX.Y.Z`（单组件项目）或 `scope/vX.Y.Z`（多组件项目，如 `cli/v0.6.0`）。

### 5. 创建 GitHub Release

Release body 须包含该版本的 CHANGELOG 内容：

```bash
gh release create v0.2.0 \
  --title v0.2.0 \
  --notes "CHANGELOG 内容粘贴到这里"
```

