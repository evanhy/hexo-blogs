---
title: pdf预览
banner_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028202859.png?x-oss-process=style/huyu
index_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028202859.png?x-oss-process=style/huyu
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028202859.png?x-oss-process=style/huyu
top_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028202859.png?x-oss-process=style/huyu
swiper_index: 2
description: 直接食用的PDF预览
tags:
  - js
abbrlink: 44377
date: 2022-10-03 14:51:21
---
### 参考文献
> https://www.cnblogs.com/songyaru/p/15078091.html

### 实现方式
> 不使用插件直接进行pdf预览时，对于小文件没有任何问题，但在预览一个305M，近400页的pdf文件，打开pdf直接拉到最后几页，会造成浏览器崩溃，于是尝试使用pdf.js的插件方式进行pdf预览，解决大文件浏览器崩溃的问题。

### 使用方法
###### 1.下载pdfjs

> 官网：http://mozilla.github.io/pdf.js/getting_started/#download

![image-20221020153026258](https://raw.githubusercontent.com/pan52yu/blogImg/main/image-20221020153026258.png)

###### 2.拖入项目中

将安装包放入中public文件夹下

![image-20221020153036289](https://raw.githubusercontent.com/pan52yu/blogImg/main/image-20221020153036289.png)



###### 3.页面中使用

（1）直接使用

```javascript
window.open('/pdf/web/viewer.html?file=' + path);//path是文件的全路径地址
```



（2）通过iframe新页面使用

```html
<iframe
  :src="`pdf/web/viewer.html?file=${pdfUrl}`"
  class="pdf-window"
  width="100%"
  height="100%"
  frameborder="0"
  ></iframe>
```

### 问题总结

###### 1.解决跨域问题

报错："file origin does not match viewer's"

解决：直接注释掉web/viewer.js中的这几行，不去判断跨域即可

```javascript
// const fileOrigin = new URL(file, window.location.href).origin;
// if (fileOrigin !== viewerOrigin) {
//   throw new Error("file origin does not match viewer's");
// }
```

###### 2.pdfjs身份认证

通过这种方式访问'/pdf/web/viewer.html?file='+path，如何在请求后端pdf文档时，在请求头中加入token验证？

解决：找到build下的pdf.js文件，加上请求头token的设置代码即可



```javascript
var params = Object.create(null);
var rangeTransport = null,
  worker = null;

// 添加设置token代码
params['httpHeaders'] = {
  "Authentication": window.localStorage.getItem('TOKEN')
}
```

###### 3.去掉pdf预览顶部的下载等

解决：找到web下viewer.html文件，找到相对应的控制设置样式visibility:hidden即可

![image-20221020153049688](https://raw.githubusercontent.com/pan52yu/blogImg/main/image-20221020153049688.png)

###### 4.分段加载

对于一些比较大的pdf文件，我们需要做到分段加载，否则会使界面卡死

解决：

（1）找到web下viewer.js，将disableAutoFetch、disableStream均改为true

![image-20221020153059956](https://raw.githubusercontent.com/pan52yu/blogImg/main/image-20221020153059956.png)

（2）找到build/pdf.js文件，寻找DEFAULT_RANGE_CHUNK_SIZE配置项，并修改为65536*16

![image-20221020153109185](https://raw.githubusercontent.com/pan52yu/blogImg/main/image-20221020153109185.png)
