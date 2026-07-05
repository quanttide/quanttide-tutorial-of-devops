# 制品库

制品库（Registry）是 DevOps 中存储和分发构建产物的平台。量潮涉及三种语言生态的制品库：

- **PyPI** — Python 包，发布 `pip install` 可安装的库
- **crates.io** — Rust 包，发布 `cargo install` 可安装的库
- **pub.dev** — Dart/Flutter 包，发布 `flutter pub add` 可安装的库

scope 配置中的 `registry` 字段指定该组件使用哪个制品库：

```yaml
scopes:
  cli:
    dir: src/cli
    language: rust
    registry: crates
  studio:
    dir: src/studio
    language: dart
    registry: pubdev
  toolkit:
    dir: packages/quanttide-devops-toolkit
    language: python
    registry: pypi
```

各制品库的发布流程由对应的 CI workflow 处理，详见[阶段](../stage/release/index.md)。
