---
title: vue页面刷新
tags:
  - vue
  - 小技巧
abbrlink: 49666
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/wallhaven-57pqz9_1920x1080.webp?x-oss-process=style/huyu
description: vue页面刷新的技巧
date: 2023-02-23 14:08:51
---

# 路由页面刷新

> 当我们在一个页面上，页面上有不同的分类，点击不同的分类需要传不同的参数来请求接口，当接口返回数据后，需要将页面的数据重新渲染，而不是之前的数据。

比如：
一个地址：http://xxx/#/music/orderMusic/editMusic?id=xxx
path 是：'/music/orderMusic/editMusic'
fullPath 是：'/music/orderMusic/editMusic?id=xxx'

我们每次只改变==url==后面的==id==，同时将页面重新渲染

**解决方法**

我们可以通过 **router-view中的key=‘$route.fullPath’** 来解决这个问题。

```vue
<router-view :key='$route.fullPath'>
```

> **通过绑定一个`fullPath`，可以识别当前页面路由的完整地址，当地址发生改变(包括参数改变)则重新渲染页面(例如动态路由参数的变化)**

当然也可以通过这两种办法：

+ 1.`this.$router.go(0)`

+ 2.`location.reload()`

这两种都可以刷新当前页面的，缺点就是相当于按`ctrl + F5` 强制刷新那种，整个页面重新加载，会出现一个瞬间的空白页面，用户体验不好，所以不推荐使用。



# 局部刷新

> 比如：当我们在渲染表格请求是，可能会出现表格没变化的情况

==可以通过v-if去强制判断==

```vue
<template>
	<Table :data='tableData' v-if='loading'>
    
  </Table>
</template>
<script lang='ts' setup>
	import { ref } form 'vue'
  
  const loading = ref(true)
  
  const getTableData = async () => {
    const res = awiat ...
    // ...逻辑代码
    // 请求成功
    if(res.code === 200) {
      loading.value = false
      setTimeout(() => {
        loading.value = true
      },0)
        // this.$nextTick(() => {
        //     this.isRouterAlive = true
        // })
      // 这样table就会进行一个强制刷新
    }
  }
</script>
```

