---
title: 优雅的Commit信息
abbrlink: 50652
cover:
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/demon%20slayer.webp?x-oss-process=style/huyu
description: git commit
date: 2022-11-23 23:15:17
tags:
  - git
---

# 优雅的Commit信息

> 使用[Angular](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-angular)团队提交规范

主要有以下组成
标题行: 必填, 描述主要修改类型和内容
主题内容: 描述为什么修改, 做了什么样的修改, 以及开发的思路等等
页脚注释: 放 Breaking Changes 或 Closed Issues
常用的修改项
type: commit 的类型

> feat: 新特性
> fix: 修改问题
> refactor: 代码重构
> docs: 文档修改
> style: 代码格式修改, 注意不是 css 修改
> test: 测试用例修改
> chore: 其他修改, 比如构建流程, 依赖管理.

scope: commit 影响的范围, 比如: route, component, utils, build...
subject: commit 的概述
body: commit 具体修改内容, 可以分为多行
footer: 一些备注, 通常是 BREAKING CHANGE 或修复的 bug 的链接.

