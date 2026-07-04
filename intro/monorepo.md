# Monorepo 入门

## 你要解决什么问题

你在开发 `qtcloud-devops` 这个 CLI 工具，它需要管理一个包含多个组件的代码仓库。假设仓库长这样：

```
qtcloud-devops/
├── src/cli/       ← 一个 Rust CLI 应用
├── src/web/       ← 一个 TypeScript 前端
├── packages/sdk/  ← 一个 Rust 库
└── apps/api/      ← 一个 Python 后端服务
```

每个组件有自己的版本号、自己的构建命令、自己的发布节奏。但它们在同一个 Git 仓库里——monorepo。

你需要设计一种方式，让工具能理解"仓库里有几个组件，每个组件在哪、用什么语言、怎么发版"。

这就是 scope 要解决的问题。

## 什么是 scope

Scope 就是一个命名边界。它把一个名字（`cli`）绑定到一个目录（`src/cli`）。

```
Scope {
    name: "cli",          ← 名字，也是 tag 前缀
    dir: "src/cli",       ← 对应目录
    language: "rust",     ← 编程语言
    build_tool: "cargo",  ← 构建工具
    registry: "crates",   ← 制品库
}
```

有了 scope，工具就能回答三个问题：

| 问题 | 答案 |
|------|------|
| `cli` 的代码在哪？ | `src/cli/` |
| `cli` 的版本号在哪？ | `src/cli/Cargo.toml` |
| `cli` 的 tag 叫什么？ | `cli/v0.1.0` |

## 为什么 scope 是必要的

没有 scope 时，整个仓库只有一个版本号：

```
v0.1.0          ← 这是谁的版本？CLI 的？API 的？
```

发了 `v0.1.0`，但 API 没改代码也被迫跟着发版。代码没改却升了版本，不合理。

加 scope 前缀后：

```
cli/v0.1.0      ← CLI 的 v0.1.0
sdk/v0.1.0      ← SDK 的 v0.1.0，可以和 CLI 同号
api/v0.2.0      ← API 的 v0.2.0，独立版本
```

同一仓库内，不同组件可以有不同的最新版本。

## 三个核心设计决策

### 1. 用 tag 前缀做命名空间

Git tag 本身只是字符串，前缀就是最简单的命名空间。

```rust
// 创建 tag 时，scope 名就是前缀
let tag_name = format!("{}/v{}", scope.name, version);
// → "cli/v0.1.0"

// 解析 tag 时，按前缀分组
let scope_name = tag.split('/').next();
// → "cli"
```

不需要额外的数据库或配置文件来记录"哪个 tag 属于谁"。

### 2. 操作路径 = scope.dir

所有文件操作（读版本号、写 CHANGELOG、更新配置文件）都以 scope 的目录为根：

```rust
let scope_dir = repo_path.join(&scope.dir);
let config_path = scope_dir.join("Cargo.toml");      // src/cli/Cargo.toml
let changelog_path = scope_dir.join("CHANGELOG.md");  // src/cli/CHANGELOG.md
```

根目录的操作路径就是 repo 根：

```rust
let config_path = repo_path.join("Cargo.toml");       // Cargo.toml
```

### 3. 回退行为决定布局规范

当没有契约文件时，工具需要自动发现 scope。扫描哪些目录？

你选择了三个：

```rust
let scan_dirs = &["src", "packages", "apps"];
```

这不是随意的。回顾你的仓库结构：

| 目录 | 组件类型 | 为什么放在这里 |
|------|---------|---------------|
| `src/*` | 应用模块 | CLI 这种随主应用一起发的功能模块 |
| `packages/*` | 库包 | SDK 这类可以独立发版的库 |
| `apps/*` | 独立部署的应用 | 需要独立发布、独立运行的服务 |

这三个目录覆盖了 monorepo 里最常见的组件类型。这就是量潮的**默认单仓规范**。

## Scope 的完整数据流

当你实现 `release publish` 时，scope 贯穿整个流程：

```text
输入: cli/v0.2.0
         │
         ▼
  1. 从版本号提取 scope 名: "cli"
         │
         ▼
  2. 查契约 → 找 name="cli" 的 scope
         │
         ▼
  3. 取 scope.dir → "src/cli"
         │
         ▼
  4. 更新 src/cli/Cargo.toml 的版本号
         │
         ▼
  5. 更新 src/cli/CHANGELOG.md
         │
         ▼
  6. git commit（在仓库根执行）
         │
         ▼
  7. git tag cli/v0.2.0
         │
         ▼
  8. git push origin cli/v0.2.0
```

每一步中，scope 都在回答"操作的路径在哪"和"tag 叫什么"。

## 契约文件

上述的 scope 定义存放在 `.quanttide/devops/contract.yaml` 中：

```yaml
scopes:
  cli:
    dir: src/cli
    language: rust
    build_tool: cargo
    registry: crates
```

没有这个文件时，工具自动扫描 `src/*`、`packages/*`、`apps/*` 下的项目配置文件来推断 scope——也就是默认单仓规范。

## 参考

- [默认单仓规范](/cli/docs/contract/monorepo.md) — 布局约定完整定义
- [Scope 详解](/cli/docs/contract/scope.md) — scope 结构、路径匹配、配置继承
- [契约解析](/cli/docs/contract/config.md) — contract.yaml 格式和覆盖语义
