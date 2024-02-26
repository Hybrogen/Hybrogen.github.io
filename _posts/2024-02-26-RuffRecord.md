---
layout: post
title: Ruff 记录
date: 2024-02-26
description: 记录，方便自己使用
tag: 杂谈
---

### 目录
- [目录](#目录)
- [简介](#简介)
- [常用指令](#常用指令)

### 简介
Ruff 是一个代码风格/格式检查工具，它可以写一个配置文件来指定代码风格，并且对项目代码进行静态检查、提示、格式化。

### 常用指令
```sh
# 格式化指定文件
ruff format filePath
# 格式化已打开的目录下所有文件
ruff format .
# 检查指定路径文件代码格式是否符合配置
ruff format --check filePath
# 检查工程里的文件是否需要格式化
ruff format --check .
# 检查指定路径文件是否需要格式化，并列出会修改哪些地方
ruff format --diff filePath
ruff format --diff .
```
