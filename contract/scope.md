# Scope

## 概念

Scope 是量潮项目中可独立规划、构建和发布的组件。一个项目通常包含多个 scope，例如 `cli`、`web`、`api` 各是一个 scope。

每个 scope 有独立的目录、规划文件和构建配置，scope 之间互不干扰。

## 示例

```yaml
scopes:
  cli:
    dir: src/cli
    language: rust
    build_tool: cargo
  studio:
    dir: src/studio
    language: dart
    build_tool: flutter
```

## 作用

- **规划**：每个 scope 有自己的规划文件（ROADMAP.md），独立管理版本计划
- **构建**：每个 scope 可以使用不同的语言和构建工具
- **发布**：scope 可以独立发布，互不影响

详细配置参考 [Scope 配置](../../specification/contract/scope.md)。
