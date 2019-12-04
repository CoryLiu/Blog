---
title: 从npm迁移yarn
date: 2019-12-04 10:22:04
tags:

- yarn

categories:

- Coding
- Vue.js

---

Yarn和npm使用相同的 `package.json` 格式，而且从 npm 迁移 Yarn 可以从npm安装依赖包。

只需执行：

``` BASH
yarn
```

Yarn 将通过自己的解析算法来重新组织 `node_modules` 目录，这个算法和[node.js 模块解析算法](https://nodejs.org/api/modules.html#modules_all_together)是兼容的。

<!-- more -->

## CLI命令比较

| npm(v5)                               | Yarn                          |
|---------------------------------------|-------------------------------|
| npm install                           | yarn install                  |
| 不适用                                 | yarn install --flat           |
| 不适用                                 | yarn install --har            |
| npm install --no-package-lock         | yarn install --no-lockfile    |
| (不适用)                               | yarn install --pure-lockfile  |
| npm install [package]                 | yarn add [package]            |
| npm install [package] --save-dev      | yarn add [package] --dev      |
| (不适用)                               | yarn add [package] --peer     |
| npm install [package] --save-optional | yarn add [package] --optional |
| npm install [package] --save-exact    | yarn add [package] --exact    |
| (不适用)                               | yarn add [package] --tilde    |
| npm install [package] --global        | yarn global add [package]     |
| npm update --global                   | yarn global upgrade           |
| npm rebuild                           | yarn install --force          |
| npm uninstall [package]               | yarn remove [package]         |
| npm cache clean                       | yarn cache clean [package]    |
| rm -rf node_modules && npm install    | yarn upgrade                  |

