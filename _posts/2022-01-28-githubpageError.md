---
layout: post
title: github-pages Deployed by @github-pages github-pages 23 hours ago Failure
date: 2022-01-28
author: H_On
description: 记录一个网络上相关文章很少的问题
tag: 题解
---

# Error: Error: No uploaded artifact was found! Please check if there are any errors at build step.
# Liquid Exception: Liquid syntax error (line 128): Variable '{_{0,0}' was not properly terminated with regexp: /\}\}/ in .md

- [开头的废话](#开头的废话)

### 开头的废话
用vscode里的git commit内容之后迟迟不见博客内容更新。

但是进入github项目里看文件已经上传了，那就应该是渲染出问题了叭，

然后发现项目右下角的 Environment 里有 `Failure` 的提示，点进去一看发现果然没显示的内容的更新都 failure 了。

于是我搜搜搜了好久发现[有一个文章](https://gitee.com/oschina/git-osc/issues/I1BLTM?_from=gitee_search)提到 **点进 `Deployed`** 还可以看到更详细的报错，有 `build` 和 `deploy` 两个部分的错误

之后搜build里的报错找到了[另一篇文章](https://gitee.com/oschina/git-osc/issues/I1BLTM?_from=gitee_search)说是md文件里出现了go数组声明语法【？

总之改掉就好了

### 解决方法
因为我写的是c++的题解，虽然已经放在代码块里了，但是github-page还是会渲染错误<br>
原先是这样的<br>
![无法渲染](/images/20220128/p1.png)

修改过后可以渲染的写法是这样的<br>
![可以渲染](/images/20220128/p2.png)

### 有用的参考文章
* [【GitHub pages】关于404错误There isn‘t a GitHub Pages site here.
](https://gitee.com/oschina/git-osc/issues/I1BLTM?_from=gitee_search)
* [部署Page时报错: Liquid syntax error: Variable '{{0, 0}' was not properly terminated with regexp: /\}\}/](https://gitee.com/oschina/git-osc/issues/I1BLTM?_from=gitee_search)