---
layout: post
title: Django 跨域问题
date: 2023-03-26
description: 记录，方便自己使用
tag: Python
---

### 目录
- [目录](#目录)
- [问题](#问题)
- [Django 跨域](#django-跨域)

### 问题
当前后端不在同一个主机或者说网段时，浏览器加载前端页面在请求后端数据时会检测连接是否跨域，不在同一个 ip 即为跨域，禁止访问。

**解决方法：** 可以在前端使用 nginx 反代理，将本地请求代理到后端服务器。或者在后端服务器添加跨域中间件。

### Django 跨域
首先需要安装跨域组件 `django-cors-headers`，安装命令如下：
```bash
pip install django-cors-headers
```

在生成一个新的 Django 项目时，自动生成的 `setting.py` 中应该是这样的：
```py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

我们需要修改这两个部分的内容：
```py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'corsheaders',# 跨域
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'corsheaders.middleware.CorsMiddleware',# 跨域，只能放在第一个
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware',# 删除静态验证的中间件
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```