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
- [新版本 ruff 常用检查命令](#新版本-ruff-常用检查命令)
- [配置](#配置)
- [参考](#参考)

### 简介
`Ruff` 是一个代码风格/格式检查工具，它可以写一个配置文件来指定代码风格，并且对项目代码进行静态检查、提示、格式化。

新版本使用 `Ruff` 可以安装库 `pip install ruff` 或者使用 `vscode` 编辑器安装 `Ruff` 插件: [Ruff (charliermarsh.ruff)](https://marketplace.visualstudio.com/items?itemName=charliermarsh.ruff)

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

### 新版本 ruff 常用检查命令
```sh
ruff check .
```

### 配置
`vscode` 想要自动格式化, 可以尝试:
- 打开 `setting` 搜索 `format on save` 勾选上
- 安装 `ruff` 插件
- 在安装了 `Ruff` 之后可以对 `vscode` 进行如下配置: 配置文件为 `根目录/.vscode/setting.json`

```json
"[python]": {                                        // 仅对 python 文件生效
    "editor.defaultFormatter": "charliermarsh.ruff", // 默认使用 Ruff 格式化
    "editor.formatOnSave": true,                     // 保存时自动格式化
    "editor.codeActionsOnSave": {
        "source.fixAll.ruff": "explicit",            // 保存时自动修复可修复的 lint 错误
        "source.organizeImports.ruff": "explicit",   // 保存时自动排序 import 语句
    }
},
"notebook.formatOnSave.enabled": true,               // notebook 保存时自动格式化
"notebook.codeActionsOnSave": {
    "source.fixAll": true,                           // notebook 保存时自动修复可修复的 lint 错误
    "source.organizeImports": true                   // notebook 保存时自动排序 import 语句
}
```

有时进行前两条就可以了, 后续有更详细的测试再来更新.

### 参考
- [大一统的 Ruff: All-in-One Linter & Formatter for Python](https://zhuanlan.zhihu.com/p/679246519)