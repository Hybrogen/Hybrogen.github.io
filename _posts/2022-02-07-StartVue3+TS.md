---
layout: post
title: 开始学习 Vue3 + TypeScript 组成的前端系统
date: 2022-02-07
author: H_On
description: 做个记录
tag: 前端
---

- [安装环境](#安装环境)
- [配置文件](#配置文件)
  - [nginx 配置](#nginx-配置)
  - [新建网页文件](#新建网页文件)
  - [生成配置文件](#生成配置文件)
- [下载依赖](#下载依赖)
- [配置打包命令](#配置打包命令)
- [打包运行](#打包运行)
- [打包多个 ts 文件](#打包多个-ts-文件)
- [开始 vue](#开始-vue)
- [参考资料](#参考资料)

### 安装环境
猹这边是没有在windows下学习，直接使用Debian系统练习，但安装 `nodejs` 之后的大部分命令都一样

1. 据说需要安装 nodejs `apt install nodejs npm`
2. 安装 TypeScript 环境 `npm install -g typescript`
3. 安装 nginx 用于代理网页便于远程查看 `apt install nginx-light`
  <br>如果是windows的朋友直接点击html文件查看即可，不需要安装这个

### 配置文件

#### nginx 配置
Debian 一般都是这个路径 `/etc/nginx/`

在 `conf.d` 文件夹下添加自己的 conf
```conf
# vim /etc/nginx/conf.d/lenrnTS.conf

server {
    listen 8888;
    server_name xxx.com;
    location / {
        root /etc/nginx/html/VuePlusTS/;
    }
}
```

添加或修改配置文件之后，都要重启一下 nginx 的服务来启用新的配置
```bash
# Debian 系统的nginx可执行程序通常在这里 /usr/sbin/nginx
# 检查配置文件是否合法可执行
/usr/sbin/nginx -t
# 重启 nginx 服务
/usr/sbin/nginx -s reload
```

通过访问 `xxx.com:8888` 来远程打开网页
> xxx.com 应替换为服务器的 ip 或 域名

#### 新建网页文件
网站的根目录放在 `/etc/nginx/html/` 下便于 `nginx` 管理，事实上放哪里都可以，直接配置文件里一致即可。

在 `VuePlusTS/` 文件夹下新建 **三个文件夹** 和 **三个文件**
<br>最终文件结构是这样的
```sh
.
├── build
│   └── webpack.config.js
├── public
│   └── index.html
└── src
    └── main.ts

3 directories, 3 files
```

文件的内容分别是这三部分，参考的都是 [24kcs/vue_study](https://github.com/24kcs/vue3_study) 的内容
<br>因为他的github博客页面没了，所以这里不记录了，我看的教程视频链接会放在文末的[参考资料](#参考资料)里

1. 入口JS: src/main.ts<br>
  随便写点什么都可以
    ```typescript
    document.write('Hello Webpack TS!')
    ```

2. 入口页面: public/index.html
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title> H_On webpack&TS </title>
    </head>
    <body>
      
    </body>
    </html>
    ```

3. webpack相关配置: build/webpack.config.js
    ```javascript
    const {CleanWebpackPlugin} = require('clean-webpack-plugin')
    const HtmlWebpackPlugin = require('html-webpack-plugin')
    const path = require('path')

    const isProd = process.env.NODE_ENV === 'production' // 是否生产环境

    function resolve (dir) {
      return path.resolve(__dirname, '..', dir)
    }

    module.exports = {
      mode: isProd ? 'production' : 'development',
      entry: {
        app: './src/main.ts'
      },

      output: {
        path: resolve('dist'),
        filename: '[name].[contenthash:8].js'
      },

      module: {
        rules: [
          {
            test: /\.tsx?$/,
            use: 'ts-loader',
            include: [resolve('src')]
          }
        ]
      },

      plugins: [
        new CleanWebpackPlugin({
        }),

        new HtmlWebpackPlugin({
          template: './public/index.html'
        })
      ],

      resolve: {
        extensions: ['.ts', '.tsx', '.js']
      },

      devtool: isProd ? 'cheap-module-source-map' : 'cheap-module-eval-source-map',

      devServer: {
        host: 'localhost', // 主机名
        stats: 'errors-only', // 打包日志输出输出错误信息
        port: 8081,
        open: true
      },
    }
    ```

#### 生成配置文件

进入网站根目录

1. 使用命令 `npm init -y` 生成配置文件 `package.json` 【需要修改
2. 使用命令 `tsc --init` 生成ts的配置文件 `tsconfig.json` 【无需修改

生成完毕之后文件结构应该是这样的
```bash
.
├── build
│   └── webpack.config.js
├── package.json
├── public
│   └── index.html
├── src
│   └── main.ts
└── tsconfig.json

3 directories, 5 files
```

### 下载依赖
在打包之前我们需要下载TS代码打包需要的库包
```bash
# 基础包
npm install -D typescript
# ts内容编译依赖
npm install -D webpack@4.41.5 webpack-cli@3.3.10
# 用于启动开发服务器
npm install -D webpack-dev-server@3.10.2
# 打包html
html-webpack-plugin
# 清除打包好的js文件
clean-webpack-plugin
# 编译处理ts文件
ts-loader
# 涉及到跨平台的命令
cross-env
```

以上是教程里的写法，还指定了一些包的版本

这里我直接头铁安装最新版了
```bash
npm install -D typescript webpack webpack-cli webpack-dev-server html-webpack-plugin clean-webpack-plugin ts-loader cross-env
```

### 配置打包命令
修改 `package.json` 这个文件里的，`"scripts"` 字段，修改为：
```json
"scripts": {
    "dev": "cross-env NODE_ENV=development webpack-dev-server --config build/webpack.config.js",
    "build": "cross-env NODE_ENV=production webpack --config build/webpack.config.js"
},
```

* 视频中说 `cross-env` 这个用于跨平台的命令，在windows里不认识，linux系统里认识
  <br>为了使其在任何环境里都认识，因此进行了这两条配置

### 打包运行
万事俱备，这时候你就可以查看成果了

* windows用户的同学，在根目录打开 cmd 使用命令 `npm run dev` 应该会自动弹出浏览器查看运行结果
* 猹这边直接使用打包命令 `npm run build` 来生成打包之后的文件
  * 打包之后会生成 dist 文件夹，里面就是打包之后可以直接运行的网站啦
  * 这时我们为了方便（看自己的情况）可能需要修改一下 nginx 的配置方便以后查看
    ```conf
    # vim /etc/nginx/conf.d/lenrnTS.conf

    server {
        listen 8888;
        server_name xxx.com;
        location / {
            root /etc/nginx/html/VuePlusTS/dist/;
        }
    }
    ```
  * 记得重启 nginx 服务 `/usr/sbin/nginx -s reload`

> 此时猹猹突然发现，就算 public/index.html 的html代码里里没有添加script文件，src里的ts代码还是自动加入了打包之后的html代码里，针不戳

### 打包多个 ts 文件
如果你有多个ts文件需要运行，在 `src` 文件夹中新建一个 ts 文件 `new.ts` 之后，只需要在 `main.ts` 的最上面引入新的ts文件 `import './new'` 就可以使用了

### 开始 vue
```bash
# 安装或升级
npm install -g @vue/cli
# 保证 vue-cli 版本在 4.5.0 以上
vue --version
# 创建项目
npm init vite@latest honweb -- --template vue
# 这个语句不能用，用上面那个吧
# vue create my_project
```

创建步骤:
- Please pick a preset - 选择 ***Manually select features***
- Check the features needed for your project - 选择上 ***TypeScript*** ，特别注意点空格是选择，点回车是下一步
- Choose a version of Vue.js that you want to start the project with - 选择 ***3.x (Preview)***
- Use class-style component syntax - 直接回车
- Use Babel alongside TypeScript - 直接回车
- Pick a linter / formatter config - 直接回车
- Pick additional lint features - 直接回车
- Where do you prefer placing config for Babel, ESLint, etc.? - 直接回车
- Save this as a preset for future projects? - 直接回车

- Use history mode for router? - 直接回车
- Pick a linter / formatter config - 直接回车

运行项目:
```bash
cd my_project
npm run serve
```

### 参考资料
* [B站讲的最好的Vue3+TS从入门到精通](https://www.bilibili.com/video/BV1CK411u75G?p=9&spm_id_from=pageDriver)