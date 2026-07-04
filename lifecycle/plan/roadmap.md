# ROADMAP 格式

## 文件位置

ROADMAP.md 位于每个 scope 的根目录，与代码同仓管理。

## 格式规则

使用 Keep a Changelog 风格 + checkbox 任务清单：

```markdown
# ROADMAP

> 格式：Keep a Changelog + checkbox 任务清单。

## [0.2.0]

### Added
- [ ] new feature a
- [ ] new feature b

### Fixed
- [x] bug fix

## [0.1.0]

### Added
- [x] release plan
- [x] test coverage
```

## 语法规则

| 元素 | 格式 | 说明 |
|------|------|------|
| 版本标题 | `## [X.Y.Z]` | 可选后缀如 `— 已发布` |
| 分类标题 | `### Added / Changed / Fixed / Removed / Deprecated / Security` | 保持首字母大写 |
| 已完成 | `- [x] xxx` | 小写 `x`，与 checkbox 标准一致 |
| 未完成 | `- [ ] xxx` | 方括号内为空格 |

## 常见格式问题

以下问题可通过 `plan doctor` 检出：

- 版本号缺少 `v` 前缀
- 分类标题大小写错误（如 `added` 应为 `Added`）
- checkbox 格式不规范（如 `[*]` 而非 `[x]`）

## 与 CHANGELOG 的关系

ROADMAP 面向未来——规划要做什么；CHANGELOG 记录历史——已经做了什么。ROADMAP 的 `[x]` 条目在发布时会迁移到 CHANGELOG。
