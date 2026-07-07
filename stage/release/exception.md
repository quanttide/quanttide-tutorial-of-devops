# 异常情况

## 处理规则

以下四条核心假设指导所有异常情况下的判断。

### Tag 不可移动

Tag 是发布过程中唯一不可重写的锚点。CHANGELOG 和 Release 都是派生制品，可以重建或修复，但 tag 一旦推送就不该动——动了会影响所有拉过这个版本的人。

异常处理从不敢建议移动 tag：补 Release、补 CHANGELOG、确认误推后删除——都在保护 tag 的不可变性。

### CHANGELOG 是规范事实源

CHANGELOG 是人工维护的结构化文档，有分类、有上下文、有格式约束。Release body 来源不定（自动生成 / AI 写 / 随手写），质量不可控。

因此：
- 缺 Release 时敢用 CHANGELOG 补（安全，事实源不变）
- 缺 CHANGELOG 时不能从 Release body 反推（不可靠，以 git log 为准）

### 只补全，不捏造

操作的极限是恢复一致性，不会创造新信息。没有事实源时宁可搁置，也不盲目补全。

异常处理的三个允许动作：
- **补建** — 从既有事实源重建缺失的派生制品
- **清理** — 移除废弃或错误的制品
- **搁置** — 信息不足时标记暂不处理

### 关系不可逆

发布生命周期的依赖方向：

```
commit → tag → CHANGELOG → Release
```

异常处理的方向是逆向追溯——缺什么就从左侧最近的事实源开始补。

## 一、缺 GitHub Release，但 tag + CHANGELOG 齐备

这种情况最常见，通常是发布时忘了执行 `gh release create`，或者 CI 的发布步骤失败了。

**处理方式**：用 CHANGELOG 内容补建 Release。

```bash
gh release create v0.2.0 \
  --title v0.2.0 \
  --notes "$(cat CHANGELOG.md 中 v0.2.0 版本的条目)"
```

这是最安全的修复——tag 和 CHANGELOG 都是已确认的事实源，补建 Release 不改变任何已有信息。

## 二、缺 CHANGELOG，但 tag + Release 存在

Release body 和 CHANGELOG 哪个更可信，取决于 Release body 的来源：

- **GitHub 自动生成的 Release notes**（从 commit 消息粗排）— 质量偏低，丢失分类、混入 chore，不适合直接回写。
- **AI 写的 Release notes** — 可能已经包含了结构化的变更描述，质量不一定差，但和 CHANGELOG 的格式规范（Keep a Changelog）可能有出入。
- **人工随手写的** — 可能不够完整或规范。

总之，Release body 的真实质量要 case by case 判断，不能一概否定。

**处理方式**：以 git log 为源补写 CHANGELOG，同时可以参考 Release body 的内容辅助判断变更分类。

```bash
git log v0.1.0..v0.2.0 --oneline
```

对照 commit 记录手动整理变更分类，遵循 [Keep a Changelog](../../source/conventions/changelog.md) 规范写入 CHANGELOG.md。没有捷径。

## 三、只有 tag，无 CHANGELOG 也无 Release

这是最棘手的情况——tag 只是一个指针，不携带任何发布元数据。没有事实源就谈不上"补全"。

**三个选择**：

1. **确认为误推**（比如开发途中不小心打上去的标签）→ 删除 tag：
   ```bash
   git push --delete origin v0.2.0
   git tag -d v0.2.0
   ```

2. **确认为有效发布**（确实完成了发布动作但只打了 tag）→ 跑一次完整的发布流程，让工具自动补全 CHANGELOG 和 Release：
   ```bash
   qtcloud-devops release publish -v v0.2.0 -y
   ```

3. **无法确认来源**（比如从同事遗留的分支上发现）→ 标记为"孤立 tag"，暂不处理，等有人能确认后再决定。

## 四、多出无关的 Release / tag

一些旧版本废弃后，对应的 Release 或 tag 还留在仓库中，会污染版本列表。

**处理方式**：清理。

```bash
# 删除 tag
git push --delete origin v0.1.0

# 删除 GitHub Release
gh release delete v0.1.0 --yes
```

删除前确认该版本确实不再需要——如果它被 CI 或其他项目引用，删除可能造成问题。
