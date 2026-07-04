# Monorepo 入门

## 一个问题

假如你有三个组件：CLI 工具、Web 前端、后端 API。你打算怎么管理它们的代码？

**多仓库方案**：建三个仓库，各管各的。

```
github.com/quanttide/qtcloud-devops-cli
github.com/quanttide/qtcloud-devops-web
github.com/quanttide/qtcloud-devops-api
```

**单仓方案**：一个仓库，三个目录。

```
qtcloud-devops/
├── src/cli/
├── src/web/
└── src/api/
```

本教程讲单仓——以及量潮的默认单仓规范。

## 为什么用单仓

多仓库听起来隔离性好，实际用起来很烦：

- **跨仓库改代码**：修一个 bug 要在三个仓库分别提 PR，串行等待
- **版本对齐靠自觉**：CLI v2.0 依赖 API v1.5，但你不知道 API 发没发这个版本
- **全局搜索搜不到**：想查"登录"的代码实现，不知道它在哪个仓库

单仓把一切放在一起：

- **一次 PR 改全部**：CLI + API 的改动一起审
- **版本对齐是物理约束**：同一个提交里，CLI 和 API 的版本就是对齐的
- **全文搜索**：一条 grep 找遍所有代码

## 单仓的挑战

单仓最大的问题是**区分**——一个仓库里怎么让不同组件独立发版？

如果整个仓库只有一个 `v0.1.0` tag，CLI 和 API 就必须同版本号发布。但它们更新频率不同、依赖关系不同，被绑在一起很痛苦。

**答案：scope。**

## Scope：给 tag 加命名空间

Scope 就是给 tag 加前缀：

| 仓库 | tag | 含义 |
|------|-----|------|
| 单仓库 | `cli/v0.1.0` | CLI 组件 v0.1.0 |
| 单仓库 | `api/v0.3.0` | API 组件 v0.3.0 |
| 多仓库 | `v0.1.0` | CLI 仓库的 v0.1.0 |

同样的版本号可以在不同 scope 里各用各的，互不干扰。

## 量潮的默认布局

我们用三个目录组织 scope：

```
<repo-root>/
├── src/*       应用模块
├── packages/*  库包
└── apps/*      独立应用
```

每个子目录下的项目配置文件（Cargo.toml、pyproject.toml 等）决定它能不能成为一个 scope。没有配置文件的目录就是普通目录，不会被当做组件。

### 实际例子

```text
qtcloud-devops/
│
├── src/cli/              ← scope "cli"
│   ├── Cargo.toml               版本号: 0.1.0
│   └── CHANGELOG.md             发布历史
│
├── packages/sdk/         ← scope "sdk"
│   ├── Cargo.toml               版本号: 0.2.0
│   └── CHANGELOG.md
│
├── apps/web/             ← scope "web"
│   ├── package.json             版本号: 1.0.0
│   └── CHANGELOG.md
│
└── README.md
```

发布命令：

```bash
# 发 CLI 的版本
qtcloud-devops release publish -v cli/v0.2.0 -y

# 发 SDK 的版本
qtcloud-devops release publish -v sdk/v0.3.0 -y

# 不指定 scope → 根目录发布
qtcloud-devops release publish -v v1.0.0 -y
```

## 没有 contract.yaml 也能工作

量潮工具默认按上述布局自动发现 scope。新 clone 的仓库直接跑：

```bash
qtcloud-devops release status
# → 自动发现 src/cli 等子目录，列出各 scope 的发布状态
```

不需要手动创建配置文件。需要自定义时才创建 `.quanttide/devops/contract.yaml`。

## 什么时候拆分仓库

单仓不是万能的。以下情况考虑拆：

- **不同发布节奏**：底层库（如 SDK）每周发版，上层应用每月发版，但两者的 CI 互相阻塞
- **不同访问权限**：核心业务代码需要严格审核，实验性代码可以放宽
- **不同语言生态**：一个项目用 `cargo publish`，另一个用 `npm publish`，CI 配置互相不兼容

但大多数团队在早期用单仓就够了，拆分是以后的事。

## 参考

- [默认单仓规范](../../cli/docs/contract/monorepo.md) — 布局约定的完整定义
- [契约解析](../contract/index.md) — contract.yaml 详解
