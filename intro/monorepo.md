# Monorepo 入门

## 什么是 monorepo

Monorepo（单一仓库）是把多个项目的代码放在同一个 Git 仓库里管理的策略。

**多仓库**（每个项目独立建仓）：

```
github.com/your-org/cli
github.com/your-org/web
github.com/your-org/api
```

**Monorepo**（所有项目在一个仓）：

```
your-project/
├── cli/
├── web/
└── api/
```

就这么简单——一个仓库、多个目录、各管各的。

## 为什么用 monorepo

多仓库的痛点：

- 改一个跨组件的功能要串行提三四个 PR
- 全局搜索搜不全，忘了代码在哪个仓库
- 版本依赖只能靠文档喊话，没人看
- 新人入职要 clone 五六个仓库才能开始干活

Monorepo 的做法：

- 一个 PR 改完所有相关代码，一起审
- 一条 `grep` 搜遍所有代码
- 版本信息写在同一个仓库的配置里，不需要跨仓库对齐
- clone 一次就够了

## monorepo 的挑战

好处说完说代价。Monorepo 最大的问题是**区分**——一个仓库里的不同组件需要独立管理。

### 版本号冲突

如果没有约定，整个仓库只有一个版本号：

```
v0.1.0
```

但这个版本号是 CLI 的？API 的？还是整个仓库的？说不清。

**做法**：tag 加前缀。

```
cli/v0.1.0       ← CLI 的版本
api/v0.3.0       ← API 的版本，和 CLI 不同
```

同一个版本号可以在不同组件里各用各的。

### 目录太大

代码多了，`git clone` 越来越慢。早期可以忍，真到忍不了的时候用 `git sparse-checkout` 只拉需要的目录。

```
git sparse-checkout set cli/ api/
```

### CI 触发频繁

任何目录的改动都会触发全部 CI。解法是按路径过滤：

```yaml
# GitHub Actions
on:
  push:
    paths:
      - 'cli/**'
      - 'api/**'
```

## 常见的 monorepo 布局

Monorepo 怎么组织目录取决于组件类型。这是几种常见风格：

**按功能分（`src/`）**

```
project/
└── src/
    ├── cli/          命令行工具
    ├── web/          前端页面
    └── core/         公共库
```

适合应用代码，所有模块最终集成在一起发布。

**按包分（`packages/`）**

```
project/
└── packages/
    ├── utils/        工具函数
    ├── components/    UI 组件
    └── config/        配置规则
```

适合库和 SDK 的 workspace。

**按应用分（`apps/`）**

```
project/
└── apps/
    ├── admin/        管理后台
    ├── api/          REST 服务
    └── worker/       后台任务
```

适合独立部署的服务，每个目录都是一个可运行的应用。

这三种可以混用——一个仓库同时有 `src/`、`packages/`、`apps/` 分别管理不同类型的组件。

## monorepo 适合谁

**适合**：
- 项目早期到中期，团队几十人以内
- 组件之间有紧密的依赖关系
- 你想减少"跨仓库沟通"的管理成本

**不适合**：
- 组件之间几乎没有依赖（强行放一起没有意义）
- 不同组件的权限需要严格隔离（如核心业务 vs 实验性项目）
- 不同组件的发布节奏差异极大（底层库每天发版，上层应用每月发版）

## 参考

- [Monorepo 模式](https://monorepo.tools/) — 关于 monorepo 的工具和实践（英文）
- [量潮默认单仓规范](/cli/docs/contract/monorepo.md) — 具体的目录布局约定
