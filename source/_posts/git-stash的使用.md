---
title: git stash的使用
date: 2020-07-10 10:49:30
tags:
- git
categories:
- Coding
- git
---

## 暂时保存现状的操作
``` bash
$ git stash save "write some save message"
```
<!-- more -->
## 显示暂存列表
``` bash
$ git stash list
```

## 恢复暂存的操作
``` bash
$ git stash pop
```
如果不指定参数，在对比操作中可以复原到最新的操作。指定类似stash@{1}的参数，可以复原特定的操作。

## 删除暂存的操作
``` bash
$ git stash drop
```
如果不指定参数，在对比操作中会删除最新的操作。指定类似stash@{1}的参数，可以删除特定的操作。

## 删除所有暂存的操作
``` bash
$ git stash clear
```


