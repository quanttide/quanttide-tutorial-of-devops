# TODO

## 核心理念：从日志到任务

TODO.md 是开发日志中提取的可执行任务清单。它位于 scope 根目录，作为 ROADMAP 的上游输入。

工作流：

```text
开发日志（journal） →  LLM 抽取  →  TODO.md  →  整理后  →  ROADMAP.md
```

不是每个 TODO 都会进入 ROADMAP——先记下来，整理时决定哪些值得放进版本计划。

## 格式

TODO.md 使用与 ROADMAP 相同的 checkbox 格式，按日期分组：

```markdown
# TODO

## 2026-07-04

- [ ] 重构 API 路由，提升响应速度
- [ ] 补充单元测试覆盖率
```

## 生成

通过 `qtadmin` 从开发日志抽取：

```bash
qtadmin knowl extract --type todo --input journal.json
```

LLM 从日志中识别可执行的意图，去重后追加到 TODO.md。每项输出包含任务描述和第一步建议：

```text
可执行: 3
  [ ] 重构 API 路由  → 评估当前路由结构
  [ ] 补充单元测试    → 识别未覆盖模块
  [ ] 升级依赖版本    → 检查 breaking changes

已更新: /path/to/TODO.md
  新增: 2 条
```

## TODO → ROADMAP

TODO.md 是流水账，ROADMAP 是版本计划。从 TODO 中筛选对当前版本有价值的条目，写入 ROADMAP：

1. 审视 TODO.md 中的未完成任务
2. 判断是否属于当前版本范围
3. 写入 ROADMAP 对应版本的分类下
4. 完成后在 TODO.md 中标记 `[x]`
