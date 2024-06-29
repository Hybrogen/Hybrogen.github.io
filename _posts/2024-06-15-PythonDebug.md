---
layout: post
title: Python 代码测试
date: 2024-06-15
description: 记录如何测试 Python 代码
tag: Python
---

### 目录
- [目录](#目录)
- [简介](#简介)
- [查看到达断点之前的所有经历的函数栈](#查看到达断点之前的所有经历的函数栈)
- [性能测试 Profile](#性能测试-profile)
- [性能测试 austin-tui](#性能测试-austin-tui)
- [参考文章](#参考文章)

### 简介
本文记录了在 `Python` 代码性能或者运行结果不符合预期时应该如何测试.

### 查看到达断点之前的所有经历的函数栈
把这个放在前面是因为感觉比较容易理解.

众所周知, 代码报错的时候 `Python` 会给出程序栈中所有的调用流程, 可以清晰的看到程序是如何到达报错位置的, 而我们可以手动输出栈中内容:

```py
import traceback

# 在想要查看的地方安置这段代码即可
for line in traceback.format_stack():
    print(line, end="")
```

### 性能测试 Profile
首先生成运行监控文件:

```py
from cProfile import Profile
p = Profile()
p.enable()
... # 要测试的内容
p.disable()
p.dump_stats("profile.dump")
```

之后使用 `snakeviz` 生成浏览页面:

```sh
pip install snakeviz
snakeviz profile.dump
# or
python -m snakeviz profile.dump output.html
```

### 性能测试 austin-tui
```py
pip install austin-tui
python -m program
austin-tui -p <PID>
# or
austin-tui python -m program
```

`Windows` 端如果报错无法启动或者找不到 `AustinError` 可以手动安装一下 [austin](https://github.com/P403n1x87/austin/releases) 选择新版本下载 msi 文件, 直接安装即可.


### 参考文章
[Python 管理 —— Conda](https://zhuanlan.zhihu.com/p/638540652)