---
title: Vue动态加载的侧边栏，刷新页面出现错误解决方式
date: 2020-03-31 15:48:25
tags:
- Vue
categories:
- Coding
- Vue.js
---

# 问题描述：
开发使用 [vue-admin-template](https://github.com/PanJiaChen/vue-admin-template)二次开发。   
使用从后端获取路由数据，然后前端渲染的动态加载方式。   
刷新动态加载进来的路由页面，会直接跳转到404页面（前提：路由里有配置404页面，否则是一片空白）

解决方式：在vue-router的`全局前置守卫`router.beforeEach里`next()`改为`next({...to,replace:true})`

<!-- more -->

详细代码：
``` js
router.beforeEach(async(to, from, next) => {
    // start progress bar
  NProgress.start()

  // set page title
  document.title = getPageTitle(to.meta.title)

  // determine whether the user has logged in
  const hasToken = getToken()
  if (hasToken) {
    if (to.path === '/login') {
      // if is logged in, redirect to the home page
      next({ path: '/' })
      NProgress.done()
    } else {
      const hasGetUserInfo = store.getters.phone
      if (hasGetUserInfo) {
        next()
      } else {
        try {
          // get user info
          await store.dispatch('user/getInfo')
          let routes=await getAsyncRoutes()
          // 获取动态路由
          await store.dispatch('permission/generateRoutes', routes)
          router.addRoutes([{ path: '*', redirect: '/404', hidden: true }])
          next({ ...to, replace: true })
          // next()
        } catch (error) {
          // remove token and go to login page to re-login
          await store.dispatch('user/resetToken')
          Message.error(error || 'Has Error')
          next(`/login?redirect=${to.path}`)
          NProgress.done()
        }
      }
    }
  } else {
    /* has no token*/

    if (whiteList.indexOf(to.path) !== -1) {
      // in the free login whitelist, go directly
      next()
    } else {
      // other pages that do not have permission to access are redirected to the login page.
      next(`/login?redirect=${to.path}`)
      NProgress.done()
    }
  }
})
```
