# 发布异常

## 处理规则

发布异常处理的目标是恢复事实源一致，而不是把一个失败版本包装成成功版本。

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

发布生命周期的基础依赖方向：

```
commit → tag → CHANGELOG → Release
```

如果版本还发布到 registry、镜像仓库或二进制资产，还要继续检查：

```text
Release → registry / image / binary assets / internal artifacts
```

异常处理的方向是逆向追溯。缺什么就从左侧最近的可信事实源开始补；无法确认的部分先搁置，不要编造。

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

## 五、Tag scope 前缀缺失

Tag 属于某个 scope（如 `cli`）但漏写了 scope 前缀，被打成根级别 tag。例如 `v0.1.0-rc.1` 应为 `cli/v0.1.0-rc.1`。这会导致自动化扫描误认为它是一个根级别发布，从而要求根 scope 有对应的 CHANGELOG 和 Release，产生假阳性。

**识别方式**：检查该 tag 对应的 commit 是否实际属于某个 scope 子目录。

**处理方式**：

1. 删除错误的根级别 tag：
   ```bash
   git tag -d v0.1.0-rc.1
   git push --delete origin v0.1.0-rc.1
   ```

2. 在正确的位置重建 tag（指向同一 commit）：
   ```bash
   git tag cli/v0.1.0-rc.1 <commit-sha>
   git push origin cli/v0.1.0-rc.1
   ```

3. 如果该 tag 已有对应的 Release（GitHub Release 创建时用了不带前缀的 tag 名），还需要删除孤立的 Release：
   ```bash
   gh release delete v0.1.0-rc.1 --yes
   ```

4. 补建正确的 Release：
   ```bash
   gh release create cli/v0.1.0-rc.1 --title "cli/v0.1.0-rc.1" --notes "..."
   ```

## 六、registry 发布失败

这种情况常见于 crates.io、PyPI、npm、pub.dev 的凭证错误、包名冲突、版本已存在、依赖不符合 registry 要求。

**识别方式**：GitHub Release 或 tag 已经存在，但制品库查不到对应版本，或者 release CI 中 publish job 失败。

**处理方式**：

1. 查看 release CI 日志，确认失败原因。
   ```bash
   gh run list --limit 5
   gh run view <run-id> --log
   ```

2. 修复凭证、包元数据、依赖来源或 workflow。

3. 重新走项目支持的发布流程。已接入 `qtcloud-devops` 的项目不要直接绕过工具发布：
   ```bash
   qtcloud-devops release publish -v cli/v0.3.2 --registry crates -y
   ```

如果 registry 已经成功发布同一版本，不能覆盖该版本；应评估是否发补丁版本。

## 七、二进制资产缺失

GitHub Release 已创建，但 Linux、Windows、macOS 安装包或其他附件缺失时，不能把二进制交付目标视为完成。

**识别方式**：

```bash
gh release view cli/v0.3.2 --json tagName,url,assets
```

**处理方式**：

1. 查看构建资产的 CI job 是否失败。
2. 修复构建脚本或上传路径。
3. 补建资产并上传到同一个 Release。
4. 在发布记录中说明哪些资产是补建的。

## 八、容器镜像缺失或标签错误

后端服务常见问题是 Git tag 和镜像 tag 不一致，或者 Release 成功但镜像仓库没有对应版本。

**识别方式**：

```bash
docker manifest inspect ghcr.io/<org>/<image>:<version>
```

**处理方式**：

1. 确认镜像 tag 应与发布版本一致。
2. 查看镜像构建和推送 CI。
3. 修复后重新运行发布流程或补跑镜像发布 job。
4. 不要用 `latest` 代替缺失的版本号镜像。

## 九、废弃的 Draft Release 污染

早期开发阶段创建的 Draft Release 不遵循 scope 前缀约定（如 `0.1.0-beta.1`、`0.1.0-beta.2`），且可能缺少 `v` 前缀。这些 Draft Release 没有对应的 tag，但会被自动化扫描误认为是根级别发布。

**识别方式**：`gh release list` 中标记为 `Draft` 的老版本。

**处理方式**：

```bash
gh release delete 0.1.0-beta.1 --yes
gh release delete 0.1.0-beta.2 --yes
```

如果对应的 tag 也存在，一并删除：

```bash
git tag -d 0.1.0-beta.1
git push --delete origin 0.1.0-beta.1
```

## 十、部分发布成功

部分成功是最容易误报的状态。例如 GitHub Release 已经有了，但 crates.io 没有；registry 有了，但二进制资产没有。

**处理方式**：

1. 按 [发布审计](audit.md) 列出每个发布目标。
2. 分别标注成功、失败、未覆盖。
3. 只汇报已经成功的目标，不把部分成功说成全部完成。
4. 对失败目标执行补建、重跑 CI、发补丁版本或搁置。
