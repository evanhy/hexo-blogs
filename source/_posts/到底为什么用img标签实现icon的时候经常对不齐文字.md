---
title: 用img标签实现icon的时候对不齐文字
tags: 小技巧
description: 对齐img做图标
abbrlink: 48966
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221227155033.png?x-oss-process=style/huyu
date: 2022-12-27 15:38:20
---

# 到底为什么用img标签实现icon的时候经常对不齐文字

+ 用img标签直接插入图片会出现对不齐的情况

![baseline](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/image-20221227153953411.png?x-oss-process=style/huyu)

> 因为图片的`vertical-align`默认是`baseline`的

+ 如果设为middle，会偏下

<img src="https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/image-20221227154246039.png?x-oss-process=style/huyu" alt="middle" style="zoom:50%;" />

> 但是对于英文字母看着是居中的，对于汉字会偏下

+ 怎么解决呢

```css
img {
  	height: 1em,
    vertical-align: -0.15em
}
```

![解决](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/image-20221227154445688.png?x-oss-process=style/huyu)

> 这样就对齐了，而且字体大小改变，图片也会居中显示
