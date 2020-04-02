---
title: vue axios nginx node expresss跨域
date: 2020-04-02 10:35:26
tags:
- vue
- cors
- node
- express
categories:
- Coding
- express
---

# 环境
1. vue-cli4
2. axios
3. 部署nginx
4. 后端express 跨域

<!-- more -->

# express 跨域设置
1. 手动：
``` js
//设置跨域访问
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*')
  res.header('Access-Control-Allow-Headers', 'Authorization,X-API-KEY, Origin, X-Requested-With, Content-Type, Accept, Access-Control-Request-Method' )
  res.header('Access-Control-Allow-Methods', 'GET, POST, OPTIONS, PATCH, PUT, DELETE')
  res.header('Allow', 'GET, POST, PATCH, OPTIONS, PUT, DELETE')
  next();
});
```

2. 安装`cors`
``` js
npm install cors --save-dev

const cors = require('cors');
app.use(cors());
```
