---
layout: post
title: 如何开始使用Django
date: 2022-01-10
description: 记录，方便自己使用
tag: Django
---

# Django的使用手册

- [Django的使用手册](#django的使用手册)
    - [安装django环境](#安装django环境)
    - [开始一个新的网站](#开始一个新的网站)

### 安装django环境
`pip3 install django`

### 开始一个新的网站
1. 创建一个项目`django-admin.py startproject `
2. 修改项目中的文件`settings.py`中的`ALLOWED_HOSTS = ['*']`。`*` 表示任何ip都可以访问，也可以写一个或多个指定的访问ip或域名，中间用逗号分隔，如：`['127.0.0.1', 'localhost', 'xxx.com']`
3. 在跟 `urls.py` 同目录下 **新建** 一个文件 `views.py` 写入一个简单的http回应
    ```py
    from django.shortcuts import HttpResponse

    def index(request):
        return HttpResponse('<h1 style="text-align: center">你好啊，这是一个自定义的页面哦</h1>')
    ```
4. 修改 `urls.py` 这个文件内容，添加一个响应
    * 这里的请求路径为空，即`ip[:端口]`访问的返回页面
    * 如果要新增一个页面，请求路径里写`/xxx`
    * 如果是一个请求，请求路径里写`xxx`
    ```py
    from django.contrib import admin
    from django.urls import path

    from . import views

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', views.index),
    ]
    ```
5. 在项目根目录下（就是有 `manage.py` 的目录下）运行命令 `python3 manage.py runserver 0.0.0.0:8000` 然后在浏览器里输入 `ip:端口` 查看一下页面，如：`localhost:8000`（记得参照第二步在setting里添加你要访问的ip）