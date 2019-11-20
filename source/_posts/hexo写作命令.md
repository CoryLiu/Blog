---
title: hexo写作命令
date: 2019-11-20 12:43:01
tags:
- hexo
categories:
- 关于博客
- hexo命令
---

# **新建文章**
``` hexo new [layout] <title> ```  
layout : 默认为`post`，可以修改`_config.yml`中的`default_layout`参数来指定默认布局  
# **布局（Layout）**
Hexo 有三种默认布局：`post`、`page` 和 `draft`。在创建这三种不同类型的文件时，它们将会被保存到不同的路径；而您自定义的其他布局和 `post` 相同，都将存储到 `source/_posts` 文件夹中。  

**布局**|**路径**
--------|--------
`post`|`source/_posts`
`page`|`source`
`draft`|`source/_drafts`

>如果不希望文章被处理，可以将 Front-Matter 中的 `layout:` 设为 `false`。

