# 发布执行

发布执行先选路径：优先走 CLI 发布，只有在项目没有接入工具、工具暂不支持目标平台、异常修复，或工具不可用且管理员明确批准时，才走手动操作。

本教程用 `cli/v0.3.2` 作为示例。实际发布时，把 `cli` 换成自己的 scope，把 `0.3.2` 换成目标版本。

## 第一步：确认发布对象

先回答三个问题：

1. 我要发布整个仓库，还是只发布一个 scope？
2. 目标版本是什么？
3. tag 应该叫什么？

常见 tag 格式：

```text
v0.3.2
v0.3.2-rc.1
cli/v0.3.2
cli/v0.3.2-rc.1
```

单组件项目一般用 `vX.Y.Z`。多组件项目使用 `scope/vX.Y.Z`，比如 CLI 用 `cli/v0.3.2`。

## 第二步：确认发布目标

把本次版本要到达的地方列出来。

| 项目类型 | 常见发布目标 |
|----------|--------------|
| Rust CLI | GitHub Release、crates.io、二进制资产 |
| Python 包 | GitHub Release、PyPI |
| Node 包 | GitHub Release、npm |
| Flutter / Dart 包 | GitHub Release、pub.dev |
| 后端服务 | GitHub Release、容器镜像仓库 |
| 内部工具 | GitHub Release、内部制品库或对象存储 |
| 文档项目 | tag、CHANGELOG、GitHub Release 或文档站点 |

发布目标以项目契约、配置文件、CI workflow 和版本计划为准。不要只看语言就推断。

## 第三步：发布前审计

发布前先运行状态检查：

```bash
qtcloud-devops build status
qtcloud-devops test status
qtcloud-devops release status
```

再运行发布审计：

```bash
qtcloud-devops release audit -v cli/v0.3.2 --scope cli
```

如果项目还没有接入 `qtcloud-devops release audit`，用 [发布审计](audit.md) 里的手动清单检查。

## 第四步：CLI 发布

已接入 `qtcloud-devops` 的项目，默认用 CLI 发布。

发布 rc 版本做 CI 验证：

```bash
qtcloud-devops release publish -v cli/v0.3.2-rc.1 --registry crates -y
```

发布正式版本：

```bash
qtcloud-devops release publish -v cli/v0.3.2 --registry crates -y
```

CLI 发布通常会做这些事：

1. 校验版本号和 tag 格式。
2. 校验配置文件版本、CHANGELOG 和 scope。
3. 创建并推送 tag。
4. 触发发布 CI。
5. 创建 GitHub Release。
6. 发布到声明的制品库或内部制品平台。
7. 生成或上传二进制资产。

发布 CI 使用组织或仓库级 secrets，例如 `CRATES_API_TOKEN`、`PYPI_API_TOKEN`。这些凭证不需要写进代码仓库。

已接入 CLI/CI 的项目，不要绕过工具直接执行 `cargo publish`、`uv publish`、`npm publish`、`flutter pub publish`、`docker push`，也不要手动移动 tag。

## 第五步：手动操作

手动操作是备用路径，不是默认路径。

进入手动操作前，先写清楚原因：

- 项目没有接入 `qtcloud-devops`。
- 发布工具还不支持目标制品库。
- 需要补建缺失的 Release 或资产。
- 工具不可用，且管理员明确批准一次性人工处理。

手动发布的最小流程：

1. 确认 `main` 或发布分支包含目标提交。
2. 确认配置文件版本和 CHANGELOG 条目一致。
3. 确认构建、测试和安全检查通过。
4. 创建并推送 tag。
5. 创建 GitHub Release。
6. 按发布目标上传制品。
7. 做发布后审计。

手动创建 tag 和 Release：

```bash
git tag cli/v0.3.2
git push origin cli/v0.3.2
gh release create cli/v0.3.2 --title "cli/v0.3.2" --notes-file release-notes.md
```

手动发布到不同制品库：

```bash
cargo publish --locked --registry crates-io
uv publish
npm publish
flutter pub publish
docker push ghcr.io/<org>/<image>:<version>
```

手动命令只代表操作入口，不代表发布成功。执行后必须继续做发布后审计。

## 第六步：发布后审计

发布完成后回到 [发布审计](audit.md)，逐项确认：

- tag 是否存在。
- GitHub Release 是否存在。
- registry 是否能查到目标版本。
- 二进制资产、镜像或内部制品是否可下载。
- release CI 是否成功。

只有所有声明的发布目标都通过检查，才能说这个版本发布完成。
