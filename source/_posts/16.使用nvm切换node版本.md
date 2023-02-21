---
title: 使用nvm切换node版本
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/风暴之眼.webp?x-oss-process=style/huyu
tags:
  - nvm
abbrlink: 30429
date: 2021-07-31 16:26:04
---
下载nvm（在nvm中下载nodejs，无需提前安装nodejs）
https://github.com/coreybutler/nvm-windows/releases

> 选择安装位置：
> 使用命令nvm判断是否安装成功

#### 安装nodejs
##### 1、查看可安装版本：

```shell
nvm list [available]
```
##### 2、安装

```shell
nvm install 11.13.0
nvm use [version] [arch] ：使用制定版本node，可指定32/64位。
```
##### 3、使用

```shell
nvm use 11.13.0
```
##### 4、卸载

```shell
nvm uninstall 11.13.0
```
