---
layout: post
title: Git 入门
date: 2024-02-22
description: 记录，方便自己使用
tag: Git
---

### 目录
- [目录](#目录)
- [获取项目](#获取项目)
- [拉取项目更新本地](#拉取项目更新本地)
- [本地更新上传](#本地更新上传)
- [分支管理](#分支管理)
- [创建分支上传远端](#创建分支上传远端)

### 获取项目
```sh
git clone projectUrl
cd projectDir
```

### 拉取项目更新本地
```sh
cd projectDir
git pull
# 合并指定分支
git pull origin remoteBranchName:localBranchName
```

### 本地更新上传
```sh
cd projectDir
git add .
git commit -m "版本更新提示信息"
git push
# 上传指定分支
git push origin localBranchName
```

### 分支管理
```sh
# 查看本地分支
git branch
# 查看本地分支最后上传记录
git branch -v
# 查看远程分支
git branch -r
# 创建分支
git branch newBranchName
# 创建分支同时切换
git checkout -b newBranchName
# 切换分支
git checkout master
# 删除分支
git branch -d branchName
```

其他的
```sh
# 查看所有分支（远程和本地）
git branch -a
# 查看哪些分支合并到当前分支
git branch --merged
# 查看所有未合并工作的分支
git branch --no-merged
# 如果删除失败可以强制删除
git branch -D branchName
# 删除远程分支
git push origin :remoteBranchName
```

### 创建分支上传远端
```sh
git checkout -b newBranchName
git add .
git commit -m "message"
git push origin newBranchName
```
