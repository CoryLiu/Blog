---
title: 避免git pull时产生多余的merge记录
date: 2020-10-19 08:53:43
tags:
- git
categories:
- Codding
---

# 产生的原因

多人开发一个项目，本地仓库落后于远程仓库

``` bash
A-B-C(dev)
\
D(origin/dev)
```

具体情境如下：

我当前拉取的远端版本为 B，此时修改了代码，并在本地仓库 commit 一次，但并未 push 到远端仓库。

另一位开发者在 B 的基础上，同样 commit 了一次并 push 到远端仓库。那么这个时候，我再 push 自己的代码就会发生错误

这个时候我们会选择，先 pull，再 push。Ok，push 成功，但是此时我们查看 log 就会发现除了我们自己提交的那条日志之外，会多出一条 “Merge branch ‘dev’ of …”。

pull操作 = fetch + merge，从 remote 分支拉取新的更新，然后合并到本地分支。

如果 remote 分支超前于本地分支，并且本地分支没有任何 commit，直接从 remote 进行 pull 操作，默认会采用 `fast-forward` 模式，这种模式下，并不会产生合并节点，也就是说不会产生多余的那条 log 信息。

如果本地先 commit 后再去 pull，那么此时，remote 分支和本地分支会出现分叉，这个时候使用 pull 操作拉取更新时，就会进行分支合并，产生合并节点和 log 信息。这两种情况分别如下：

``` bash
# fast-forword 
A-B-D(origin/master)
     \
      C(master)
```
 
``` bash
# merge
A-B-C-E(master)
   \ /
    D(origin/master)
```

# 如何解决

## 1.
在执行 git pull 的时候加上 `--rebase` 参数，成功后再进行真正的 merge 操作。（如果有冲突需要手动解决）

## 2.
这个方法操作以后可以一劳永逸。在你的 git bash 里执行 `git config --global pull.rebase true`，这个配置就是告诉 git 在每次 pull 前先进行 rebase 操作。这种方法和方法1原理一样，只不过方法1是每次pull前都要手动操作

## 3.
不建议直接 pull 操作，建议在 commit 之后，先 fetch 一下，然后 merge 合并，最后执行 push 命令

# 转载信息
>版权声明：本站未注明转载的文章均为原创，版权归 如默 所有   
>本文链接：https://www.rumosky.com/archives/241.html   
>所有转载文章须注明出处，保留原作者链接，并说明文章非原创，且不可用于商业目的