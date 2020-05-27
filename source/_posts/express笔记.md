---
title: express笔记
date: 2020-03-31 10:22:12
tags:
- JavaScript
- Node.js
- Express
categories:
- Coding
- Express
---

# 安装
``` bash
npm install express --save
```
# Express应用程序生成器
express-generator 快速创建一个应用的骨架   
``` bash
npm install -g express-generator
```
## express命令行参数
``` bash
express -h

  Usage: express [options] [dir]

  Options:

    -h, --help          输出使用方法
        --version       输出版本号
    -e, --ejs           添加对 ejs 模板引擎的支持
        --hbs           添加对 handlebars 模板引擎的支持
        --pug           添加对 pug 模板引擎的支持
    -H, --hogan         添加对 hogan.js 模板引擎的支持
        --no-view       创建不带视图引擎的项目
    -v, --view <engine> 添加对视图引擎（view） <engine> 的支持 (ejs|hbs|hjs|jade|pug|twig|vash) （默认是 jade 模板引擎）
    -c, --css <engine>  添加样式表引擎 <engine> 的支持 (less|stylus|compass|sass) （默认是普通的 css 文件）
        --git           添加 .gitignore
    -f, --force         强制在非空目录下创建
```

例：创建了一个名称为 myapp 的 Express 应用
``` bash
express --view=pug myapp
```
``` bash
cd myapp
```
``` bash
npm install
```
## 启动app
MacOS or Linux：   
``` bash
DEBUG=myapp:* npm start
```
Windows
``` bash
set DEBUG=myapp:*
npm start
```
# 生成示例的文件结构
``` bash
.
├── app.js
├── bin
│   └── www
├── package.json
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
│       └── style.css
├── routes
│   ├── index.js
│   └── users.js
└── views
    ├── error.pug
    ├── index.pug
    └── layout.pug

7 directories, 9 files
```

# express + MongoDB RestfulApi
- express
- mongoose
- MongoDB
