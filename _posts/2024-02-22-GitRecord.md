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
- [文件提交缓存查看](#文件提交缓存查看)
- [创建分支上传远端](#创建分支上传远端)
- [查看修改记录](#查看修改记录)
- [修改提交用户配置信息](#修改提交用户配置信息)
- [合并分支解决冲突](#合并分支解决冲突)
- [差异查看](#差异查看)
- [其他操作](#其他操作)

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

### 文件提交缓存查看
```sh
# 添加文件
git add .
git add path/file_name

# 查看缓存区文件
git ls-files
# 显示文件状态信息
git ls-files -s
# 查看修改但未缓存的文件
git ls-files -m
# 查看未跟踪的文件 .gitignore
git ls-files -o
# 查看所有暂存和未暂存的文件
git ls-files --stage

# 回退缓存区文件
git reset
# 回退到头版本
git reset HEAD <file>
# 丢弃缓存区文件 (谨慎使用)
git reset --hard

# 查看工作区与缓存区的差异
git diff <file>
# 查看暂存区与最后一次提交的差异
git diff --cached
```

### 创建分支上传远端
```sh
git checkout -b newBranchName
git add .
git commit -m "message"
git push origin newBranchName
```

### 查看修改记录
```sh
# 查看某个文件的提交记录
git log filePath
# 查看简单记录列表
git log --pretty=oneline filePath
# 查看所有记录信息和diff
git log -p filePath
# 查看所有提交记录
git log
# 查看某次提交中某个文件的的变化
git show commitCode filePath
# demo
git show f90930137f0464b7cbe285be6a7ca768f7aa6d59 404.html
# 从指定 commitCode 开始查看详细提交记录
git log -p f90930137f0464b7cbe285be6a7ca768f7aa6d59
```

### 修改提交用户配置信息
```sh
git config --global user.name "H_On"
git config --global user.email "your@email.com"
```

### 合并分支解决冲突
```sh
git checkout -b newBranch
git checkout oldBranch
git merge newBranch
# 手动修改冲突文件
git add .
git commit -m "merge newBranch"
# 取消 merge 操作
git merge --abort:
```

### 差异查看
```sh
# 查看分支差异
git diff oldBranch newBranch --stat
# 查看分支详细差别
git diff oldBranch newBranch
# 查看指定文件分支详细差别
git diff oldBranch newBranch filePath
# 查看不同版本的差异
git diff oldCode newCode [--stat]
# 例
git diff 20f4366 66f68c8 --stat
# 用回溯的标记查看版本的差异
git diff Branch~2 Branch [--stat]

# 查看 newBranch 中有，而 oldBranch 中没有的 log
git log newBranch ^oldBranch
# 查看 newBranch 中多提交了哪些内容
git log oldBranch..newBranch
```

### 其他操作
```sh
# push 之前执行这个命令可以进入 vim 修改上次写错的 commit 信息
git commit --amend
# 如果 merge 之后想撤回
git reset --merge
# 查询当前分支状态
git status
```
