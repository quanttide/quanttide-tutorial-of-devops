# 配置文件

事实源的另一种形态是**语言框架的配置文件**。这些文件中的字段（如版本号）作为机器可读的事实源，供 CLI 和 CI 系统解析。

```yaml
# Cargo.toml
[package]
version = "0.1.0"
```

```toml
# pyproject.toml
[project]
version = "0.1.0"
```

```json
// package.json
{
  "version": "0.1.0"
}
```

CLI 工具通过约定的字段路径读取版本号，确保一处修改处处同步。
