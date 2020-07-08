---
title: scss局部和全局样式
date: 2020-07-08 17:24:27
tags:
- scss
- vue
categories:
- Coding
- Vue.js
---

# 场景
需求：vue框架，使用 `element-ui` ，编辑和查看使用同一个组件展示，在查看时给 `el-form` 添加一个     `disabled` 。会带上 disabled 的样式。
如果写在 scope 的样式里，不会生效。如果写在全局的样式里，会影响其他不需要重写 disabled 样式的组件。

# 解决方案
在外层元素添加一个id   
再写样式即可
``` html
<template>
<div id="home">
<el-form :disabled="true">
<!-- 表单内容 -->
</el-form>
</div>
</template>
<style lang="scss">
#home{
  /*此处写样式*/
}
</style>
```

