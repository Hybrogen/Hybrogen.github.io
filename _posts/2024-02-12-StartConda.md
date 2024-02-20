---
layout: post
title: 开始使用 Conda
date: 2024-02-12
description: 记录，方便自己使用
tag: Python
---

### 目录
- [目录](#目录)
- [Linux 中使用 Conda 管理 Python 项目](#linux-中使用-conda-管理-python-项目)
- [Windows 中使用 Conda 管理 Python 项目](#windows-中使用-conda-管理-python-项目)
- [参考文章](#参考文章)

### Linux 中使用 Conda 管理 Python 项目


### Windows 中使用 Conda 管理 Python 项目
1. [下载](https://www.anaconda.com/download)
2. 使用
   - `conda info --envs` 查看现存环境
   - `conda create --name test` 创建名为 test 的新环境
   - `conda activate test` 打开环境 
   - `conda deactivate` 关闭环境
   - `conda create --name test python=3.7.1` 创建时指定 Python 版本
   - `conda remove --name test --all`  删除刚刚创建的 test 环境
   - 修改环境名称: 找到路径 `conda 安装路径\anaconda3\envs` 直接修改文件夹名称即可
3. 问题
   - 如果 cmd 找不到 conda 命令
   > 右键此电脑->属性->高级系统设置->环境变量->系统变量->双击 Path->添加路径: `conda 安装路径\anaconda3\Scripts`
   - 报错无法找到 activate/deactivate 命令
   > 把 `conda 安装路径\anaconda3\condabin` 里找到 bat 脚本复制粘贴到 `conda 安装路径\anaconda3\Scripts` 文件夹里，之后重启 cmd 即可
   - 

### 参考文章
[Python 管理 —— Conda](https://zhuanlan.zhihu.com/p/638540652)