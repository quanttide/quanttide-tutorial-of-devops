# 计划

## 核心理念：规划即管理预期

计划阶段回答三个问题：

- **要做什么** — 当前版本的交付范围
- **做了什么** — 已经完成的工作
- **还有什么没做** — 剩余的工作量

### 路线图与里程碑

规划文件是一个**路线图（roadmap）**——描述整体计划、版本间的依赖关系和交付时间线。每个版本标题（`## [X.Y.Z]`）是路线图上的一个**里程碑（milestone）**——标记某个阶段的关键节点或交付物。

> Imagine having a map with no checkpoints. So you know where you're starting your journey and where you need to get, but you don't know what you're supposed to do on your path there.

路线图告诉你方向，里程碑告诉你走到哪了。

## 操作流程

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

只读检查，不会修改文件。检出的常见问题见 ROADMAP 格式参考。

### 清理条目

```bash
qtcloud-devops plan clean [scope]
```

提交前清理已完成的条目，保持规划整洁。删除所有 `[x]` 条目，若分类下无条目则删除空分类。

## 规划文件

ROADMAP.md 是规划阶段的事实源，格式说明详见[约定文件](../../source/conventions/roadmap.md)。

将 ROADMAP 目标拆解为具体执行任务详见 [TODO](../../source/conventions/todo.md)。

## 常见问题

**能否用项目管理工具替代规划文件？**

规划文件与代码同仓，确保规划与实现同步。推荐二者结合使用：项目管理工具（如飞书多维表格）用于跨团队协作和长期规划，代码仓内的规划文件用于版本级粒度规划。
