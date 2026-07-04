# GitOps

## 核心理念

GitOps 是一种以 Git 为单一事实源的运维模式。一切基础设施和应用程序的期望状态都声明在 Git 仓库中，任何变更通过 Pull Request 发起，合并后自动同步到实际环境。

```
声明式配置（Git） →  Pull Request  →  合并  →  自动同步  →  实际环境
```

## 核心原则

**声明式配置**

不写"如何做"的脚本，只写"要什么状态"的描述文件。Kubernetes YAML、Terraform HCL 都是声明式。

**Git 为事实源**

实际环境的状态由 Git 仓库中的声明决定。手动修改环境会被自动覆盖回 Git 描述的状态。

**自动化同步**

仓库变更后，Operator 或 CI/CD Pipeline 自动将新状态推送到目标环境，无需人工登录服务器。

## 与传统 CI/CD 的区别

| | 传统 CI/CD | GitOps |
|--|-----------|--------|
| 触发方式 | CI 触发 CD | Git 仓库变更触发 |
| 部署工具 | 手动或脚本推送 | Operator 自动拉取 |
| 环境漂移 | 常见，需额外检测 | 自动回正 |
| 回滚 | 重新运行部署流水线 | `git revert` 即可 |

## 工作流示例

```bash
# 1. 修改配置
vim deployment.yaml

# 2. 提交 PR
git checkout -b update-replicas
git add deployment.yaml
git commit -m "feat: 将副本数调整为 3"
git push origin update-replicas

# 3. PR 审核合并后，Operator 自动同步
# 环境自动更新为 3 个副本
```

## 适用场景

- **Kubernetes 集群管理**：Flux、Argo CD 等工具自动同步
- **基础设施即代码**：Terraform、Pulumi 配置纳入 Git 管理
- **策略即代码**：OPA、Kyverno 策略随代码版本化
