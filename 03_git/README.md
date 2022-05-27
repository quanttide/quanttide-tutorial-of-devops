---
title: 代码仓库
author: 张果
created_at: "2021-08-11"
updated_at: "2021-08-11"
status: dev
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# 代码仓库

% 这里暂时分为教学目标、主要内容、关联章节三个部分，后续考虑在此基础上对章README进一步标准化，并尽量做成可注入系统的标准化方案。

## 教学目标

本章的教学目标是以Git作为主要版本管理工具管理代码和文档等资源。

## 主要内容

% 这张目前的表述依然比较口语化，写成论文summary的效果同时又不失简单才可以。

本章计划分为三节。第一节从新手角度讲Git的原理和使用，比如add、commit
、pull、push、clone、branch等基础概念。第二节从团队内多人协作的角度讲常见的问题及解决方案，比如管理分支和PR等。第三节从软件工程规范的角度讲常见的Git工作流实践及使用场景，主要是Git Flow、GitHub Flow、GitLab Flow。

## 关联章节

% 这里暂时只使用手写的方式明确章节之间的关联关系，后续如果可以尽可能写成YAML文件并注入系统。

我们通过第二章介绍的敏捷项目管理和代码仓库的分支、提交、合并等对应，建立更加容易界定边界的项目管理规则。

第四章即将介绍的构建计划将会依赖代码仓库的分支、提交、合并等特性实现。
