---
layout: post
title: Python 使用技巧
date: 2024-02-13
description: 记录，方便自己使用
tag: Python
---

### 目录
- [目录](#目录)
- [pip 下载速度慢怎么办](#pip-下载速度慢怎么办)
- [创建本地环境](#创建本地环境)
- [vscode config](#vscode-config)
- [参考文章](#参考文章)

### pip 下载速度慢怎么办
- 临时换源: `pip install -i https://pypi.douban.com/simple/ pyside6`

### 创建本地环境
`Win`
```bat
py -3 -m venv .venv || exit /b
CALL .\.venv\Scripts\activate || exit /b
python -m ensurepip
python -m pip install --upgrade pip
```

`Linux`
```sh
python3 -m venv .venv
. .venv/bin/activate
python -m ensurepip
python -m pip install --upgrade pip
```

### vscode config
- [`.vscode/extensions.json`]()
- [`.vscode/setting.json`]()

### 参考文章
[Python 管理 —— Conda](https://zhuanlan.zhihu.com/p/638540652)