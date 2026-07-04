# GitOps

## 核心理念

GitOps 是一种以 Git 为单一事实源的运维模式。一切基础设施和应用程序的期望状态都声明在 Git 仓库中，任何变更通过 Pull Request 发起，合并后自动同步到实际环境。

## 核心原则

**声明式配置**

不写"如何做"的脚本，只写"要什么状态"的描述文件。

**Git 为事实源**

实际环境的状态由 Git 仓库中的声明决定。手动修改环境会被自动覆盖回 Git 描述的状态。

**自动化同步**

仓库变更后，CI/CD Pipeline 自动将新状态推送到目标环境，无需人工登录服务器。

## 量潮的实践

### GitHub Actions + GitHub Pages

量潮文档站采用 GitOps 方式部署。文档源码提交到 `docs/` 目录，推送后 Actions 自动构建并发布到 GitHub Pages：

配置位于 `.github/workflows/deploy-docs.yml`：

```yaml
name: Deploy docs to GitHub Pages

on:
  push:
    branches: [main]
    paths:
      - docs/**
      - .github/workflows/deploy-docs.yml

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm install -g mystmd
      - run: myst build --dir docs
      - uses: actions/upload-pages-artifact@v3
        with:
          path: docs/_build/html

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/deploy-pages@v4
```

### 工作流

```bash
# 1. 修改文档
vim docs/handbook/lifecycle/plan.md

# 2. 提交 PR
git checkout -b update-plan-doc
git add docs/
git commit -m "docs: 整理 plan 阶段文档"
git push origin update-plan-doc

# 3. PR 审核合并后，Actions 自动构建并部署
# 文档站自动更新
```

所有部署配置（workflow YAML、Pages 设置）都和源码一起版本化管理，修改配置同样走 PR 流程。

## 适用场景

- **文档站部署**：MyST Markdown → GitHub Pages，推送即发布
- **静态站点托管**：SPA 应用通过 GitHub Pages 部署
- **API 文档发布**：OpenAPI 规范变更后自动重新生成文档站
