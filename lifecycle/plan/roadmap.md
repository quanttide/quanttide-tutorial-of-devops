# ROADMAP

ROADMAP.md 是量潮 DevOps 的规划文件，位于每个 scope 的根目录，与代码同仓管理。

## 格式

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

### 语法规则

| 元素 | 格式 | 说明 |
|------|------|------|
| 版本标题 | `## [X.Y.Z]` | 可选后缀如 `— 已发布` |
| 分类标题 | `### Added / Changed / Fixed / Removed / Deprecated / Security` | 保持首字母大写 |
| 已完成 | `- [x] xxx` | 小写 `x`，与 checkbox 标准一致 |
| 未完成 | `- [ ] xxx` | 方括号内为空格 |

## 命令

### 查看进度

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

### 验证格式

```bash
qtcloud-devops plan doctor [scope]
```

只读检查，不会修改文件。检出的常见问题：

- 版本号缺少 `v` 前缀
- 分类标题大小写错误（如 `added` 应为 `Added`）
- checkbox 格式不规范（如 `[*]` 而非 `[x]`）

发现问题后手动修复，或交由 LLM 协助修复。

### 清理条目

```bash
qtcloud-devops plan clean [scope]
```

提交前清理已完成的条目，保持 ROADMAP 整洁。删除所有 `[x]` 条目，若分类下无条目则删除空分类。

## 与 CHANGELOG 的关系

ROADMAP 面向未来——规划要做什么；CHANGELOG 记录历史——已经做了什么。ROADMAP 的 `[x]` 条目在发布时会迁移到 CHANGELOG。
