# 发布审计

发布审计分两次做：发布前审计回答“现在能不能发”，发布后审计回答“是不是真的发成了”。

## 自动审计

项目接入 `qtcloud-devops` 时，先用自动审计。

```bash
qtcloud-devops release status
qtcloud-devops release audit -v cli/v0.3.2 --scope cli
```

还可以补充构建和测试状态：

```bash
qtcloud-devops build status
qtcloud-devops test status
```

自动审计失败时，不要继续发布。先修复失败项，再重新审计。

## 手动审计

没有自动审计能力，或者发布目标比较特殊时，用手动清单补足。

### 发布前清单

发布前逐项确认：

- 发布对象明确：仓库、scope、目录、版本、tag 都清楚。
- 发布目标明确：GitHub Release、registry、镜像、二进制资产、内部制品库没有遗漏。
- 版本一致：配置文件版本、CHANGELOG 条目、目标 tag 一致。
- CHANGELOG 可读：按 Added、Changed、Fixed、Removed 等分类，不是 commit 列表。
- 工作区干净：没有未提交改动或临时文件。
- 目标提交正确：发布提交已经在 `main` 或项目约定的发布分支上。
- 构建测试通过：build、test、lint、coverage 或平台构建已经通过。
- 凭证安全：token 和密钥在组织、仓库或受控环境中配置，没有写进仓库。

### 发布后清单

发布后逐项确认：

- tag 已推送到远端。
- tag 指向本次发布提交。
- GitHub Release 已创建。
- Release 正文来自 CHANGELOG 对应版本。
- crates.io、PyPI、npm、pub.dev 或镜像仓库能查到目标版本。
- 二进制资产、安装包、镜像或内部制品能下载。
- release CI 成功。
- 交付说明没有把失败目标写成已完成。

## 常用检查命令

检查本地和远端 tag：

```bash
git tag --list 'cli/v*'
git ls-remote --tags origin 'cli/v*'
```

检查 GitHub Release：

```bash
gh release view cli/v0.3.2 --json tagName,url,assets
```

检查 GitHub Actions：

```bash
gh run list --limit 5
gh run view <run-id> --json status,conclusion,url
```

检查 crates.io：

```bash
cargo info <crate> --registry crates-io
```

检查 PyPI：

```bash
python -m pip index versions <package>
```

检查 npm：

```bash
npm view <package> version
```

检查容器镜像：

```bash
docker manifest inspect ghcr.io/<org>/<image>:<version>
```

## 管理员与 AI 怎么配合

管理员负责判断“该不该发”：业务上是否需要发、版本号是否合适、发布目标是否完整、是否允许手动备用路径。

AI 负责判断“能不能发”：事实源是否一致、命令是否通过、文档是否闭环、凭证是否安全、发布结果是否能查到。

常见分歧按下面处理：

- 管理员说要发布，但 CHANGELOG 缺失：先补 CHANGELOG。
- 自动审计通过，但发布目标没列全：先补发布目标。
- 手动操作更快，但项目已有 CLI/CI：继续走 CLI/CI。
- GitHub Release 成功，但 registry 失败：只能说 GitHub Release 成功，不能说版本发布完成。
- registry 成功，但二进制资产缺失：记录为部分成功，补资产或进入异常处理。

## 审计记录模板

每次发布后记录一段简短结论：

```markdown
发布对象：<仓库>/<scope>
版本：<scope/vX.Y.Z>
发布路径：CLI 发布 / 手动操作
发布目标：GitHub Release、crates.io、二进制资产
发布前审计：通过 / 未通过，原因
发布后审计：通过 / 部分通过 / 未通过，证据链接
遗留问题：无 / 待补建资产 / 待重跑 CI
```

