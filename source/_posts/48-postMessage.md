---
title: postMessage
tags:
  - js
  - postMessage
cover: 'https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/202307171539537.png'
top_img: 'https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/202307171539537.png'
abbrlink: 61913
date: 2023-07-17 15:36:24
description: 跨域的一种解决方法
---

# postMessage

> https://developer.mozilla.org/zh-CN/docs/web/api/window/postmessage

> postMessage是HTML5引入的一种跨文档通信的机制，可以在不同的窗口或框架之间传递数据，即使这些窗口或框架不属于同一个源。



## postMessage的使用

### 发送消息

> 以跨域下载文件为例

```vue
// vue 文件
<script lang="ts" setup>
import { ref } from "vue";
const iframeDom = ref<HTMLIFrameElement>(null);
const downVideo = (url: string, fileName: string) => {
  const iframeWin = iframeDom.value.contentWindow;
  iframeWin.postMessage({ apiUrl: url, fileName: fileName},"*");
};
defineExpose({
  downVideo
});
</script>
<template>
  <iframe ref="iframeDom" frameborder="0" scrolling="auto" src="/xg/xgvideo.html" />
</template>

```

以下是postMessage函数的语法：

```ts
otherWindow.postMessage(message, targetOrigin, [transfer]);
```

参数说明：

- `otherWindow`：目标窗口的引用，可以是iframe或window对象。
- `message`：要发送的数据。可以是任何可以序列化的JavaScript对象。
- `targetOrigin`：消息的目标源。只有目标窗口与指定的源相同才会接收到消息。可以是字符串“*”，表示接收任何源的消息。
- `transfer`：可选，要转移的对象，如Blob和ArrayBuffer。



### 接收消息

```html
<!-- html文件 -->
<!DOCTYPE html>
<html>
<body>
<div id="mse"></div>
<script>
  window.addEventListener("message", e => {
    if (!e.data.apiUrl) return;
    let data = e.data;
    // console.log(data.apiUrl, data.fileName);
    const link = document.createElement("a");
    document.body.appendChild(link);
    link.style.display = "none";
    link.download = data.fileName;
    link.href = data.apiUrl;
    link.target = "_blank";
    link.click();
    document.body.removeChild(link);
    window.URL.revokeObjectURL(data.apiUrl);
  });
</script>
</body>
</html>

```

以下是添加事件侦听器的语法：

```js
window.addEventListener('message', handleMessage, [useCapture]);
```

参数说明：

- `handleMessage`：当接收到消息时要调用的函数。
- `useCapture`：指定事件是否在捕获或冒泡阶段处理。