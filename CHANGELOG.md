# CHANGELOG

## [0.3.0] - 2026-07-04

### Added
- 新增制品库章节（platform/registry/），覆盖 PyPI、crates.io、pub.dev
- 新增配置文件事实源章节（source/config/）
- 新增约定文件事实源章节（source/conventions/），整合 ROADMAP、TODO、CHANGELOG 格式参考

### Changed
- 全面重构目录结构：lifecycle→stage、03_git→source/git、01_quick_start→platform/github（重写为 GitHub 内容）
- 拆分事实源与阶段：格式参考归 source/conventions/，操作流程归 stage/
- version.md 移回 stage/release/
- 03_git/README.md→index.md

### Removed
- 移除 02_scrum、04_pipelines、05_artifacts、06_openapi 目录

## [0.2.0] - 2026-07-04

### Added
- 新增实践理念章节（intro/），包含 DevOps 实践理念概述和 GitOps 教程
- 新增合约章节（contract/），包含 scope 概念教程
- 新增计划教程（lifecycle/plan/），覆盖核心理念、ROADMAP 格式、TODO 拆解
- 新增 CHANGELOG 教程（lifecycle/release/changelog.md）

### Changed
- 重构计划教程结构：分离 ROADMAP 格式参考与计划思路，scope FAQ 移入合约章节

## [0.1.3] - 2026-07-02

### Added
- 新增“小步快跑”作为发布的核心原则

### Changed
- 更新 myst.yml 的标题和描述

### Removed
- 移除旧的生命周期文件（已迁移至 lifecycle/ 目录）

## [0.1.2] - 2026-07-02

### Added
- 新增语义化版本（SemVer）章节，整合自原手册
- 新增第一章大纲内容
- 新增 MyST 配置文件（myst.yml）及 Jupyter Book CI 工作流

### Changed
- 重构文档结构：将发布、工作流等章节重命名为 `index.md`，并调整目录路径
- 按场景重新组织版本号文档，简化 MVP 定义并明确边界
- 将版本号规则移至文档最前方，并将检查步骤调整为第一步

### Fixed
- 修复 myst.yml 中工作流文档的引用路径

### Removed
- 移除独立的 SemVer 目录，内容合并至版本号文档
- 移除版本号文档中的“下一步”章节
- 移除 Jupyter Book 发布工作流（转移至主仓库管理）
- 移除微服务相关内容，已迁移至独立教程

## [v0.1.1] - 2022-10-06

修复重复课时命名异常。

## [v0.1.0] - 2022-10-06

初始版本。
