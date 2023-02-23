---
title: template报错undefine
description: 处理模板错误
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/wallhaven-3zj776_1920x1080.webp?x-oss-process=style/huyu
banner_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/wallhaven-3zj776_1920x1080.webp?x-oss-process=style/huyu
index_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/wallhaven-3zj776_1920x1080.webp?x-oss-process=style/huyu
top_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/wallhaven-3zj776_1920x1080.webp?x-oss-process=style/huyu
tags:
  - vue
  - 小技巧
abbrlink: 15503
date: 2023-02-23 16:21:36
---

## Vue项目 template报错undefined

> 解决vue项目中页面调用数据 在数据加载完毕之前出现undefined问题

在项目中遇到后台数据还没有加载完毕，但是页面上调用了后台数据中的字段，这样就会报undefined

例如：一进入页面直接回显数据

```html
<template>
  <div>
    {{ data[0].name }}
  </div>
</template>
<script setup>
	const data = []
</script>
```

此时因为 `data`是空数组，所以会报错

<font color='red'>TypeError: Cannot read property 'name' of  undefined</font>



## 如何解决

1、可以通过给个默认值的方式

```html
<template>
  <div>
    {{ data[0].name }}
  </div>
</template>
<script setup>
	const data = [{name:'我是name'}]
</script>
```



2、通过`v-if`判断一下

```html
<template>
  <div v-if='data && data.length'>
    {{ data[0].name }}
  </div>
</template>
<script setup>
	const data = []
</script>
```

