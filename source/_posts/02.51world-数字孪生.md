---
title: 51world(数字孪生)
banner_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028203122.png?x-oss-process=style/huyu
index_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028203122.png?x-oss-process=style/huyu
top_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028203122.png?x-oss-process=style/huyu
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028203122.png?x-oss-process=style/huyu
categories:
  - 数字孪生
abbrlink: 3655
date: 2022-06-18 15:13:58
tags:
---

💡 概念解释：数字孪生是充分利用物理模型、传感器更新、运行历史等数据，集成多学科、多物理量、多尺度、多概率的仿真过程，在虚拟空间中完成映射，从而反映相对应的实体装备的全生命周期过程。数字孪生是一种超越现实的概念，可以被视为一个或多个重要的、彼此依赖的装备系统的数字映射系统。

# 51world数字孪生平台

💡 平台简介：51world数字孪生城市平台采用“多元数据处理，城市底座生成，应用界面开发，数据集成开放”的模块化设计，满足平台持续运营，应用灵活适配，数据管理保密的需求，支持CIM、IOC，城市大脑业务，助理城市数字化转型

✍️平台地址：https://www.51aes.com/

## 产品生态

| [**PaaS平台**](#DAN0W)   | 通过开放产品能力、让开发者共享各行业AES场景底板，数据面板，超级接口等帮助开发者快速构建全行业应用（城市、园区、交通、地产、水务等）0代码开发，一站式创作，多端跨平台的数字孪生交互系统 |
| ------------------------ | ------------------------------------------------------------ |
| [**SuperAPI**](#zqHnG)   | 是一套负责交涉Web页面和云渲染服务的编程接口（for JavaScript）, 需要基于51平台使用；用户可在WEB页面上创建任意HTML5 UI元素, 利用SuperAPI与渲染场景进行双向交互。同时可以兼容当前主流的前端框架（Vue，React，Angular等） |
| [**SuperGUI**](#mTQHM)   | SuperGUI（图形用户界面），是围绕着51WORLD核心产品全要素场景（AES），帮助生态合作伙伴进行细分行业的解决方案的搭建和输出，产品形态为B/S架构的开发者生态组合工具，旨在将基于AES的数字孪生基础能力快速赋能全行业，帮助生态伙伴高效的、低成本的构建数字孪生控制管理平台。 |
| [**蓝图编辑器**](#ZOKQn) | SuperGUI中编辑事件配置功能，可以操作页面中的元素节点，交互节点，逻辑节点，场景节点，可以接入 SuperAPI 实现场景渲染双向交互功能 |

## PaaS平台

下载地址：https://wdp.51aes.com/product-download

产品文档：https://djp2gcp2um.feishu.cn/docs/doccncXWIo0B47godjUyVf3N9Fe#

#### 功能1：[管理中心](https://djp2gcp2um.feishu.cn/docs/doccncXWIo0B47godjUyVf3N9Fe#pvOmJa)

在51WDP管理中心内，用户可以对案例进行管理及发布，也可以管理资产和场景（待开放），查看自己的权益，设置，个人信息，接收通知等。

知识点：

1.当场景需要与superAPI中链接时需要使用到管理中心中的渲染口令（具体实现：[superAPI-场景渲染](#IVMEf)）

2.当需要本地场景多个人使用时可以在 管理中心 - 系统设置 中修改 渲染路数 ，即可实现多人操作（多人操作对电脑性能要求较高）

#### 功能2：[案例编辑器](https://djp2gcp2um.feishu.cn/docs/doccncXWIo0B47godjUyVf3N9Fe#U4chVE)

在案例编辑器中，用户可以对自己的案例进行创作编辑，目前有【模板】【场景】【模型】【面板】【交互（蓝图）】【导入】等功能模块，对应了数字孪生项目的各项基本要素。另外，还有众多的全局功能，包括偏好设置、模式切换、预览、发布等。

## SuperAPI

产品文档：http://superapi.51aes.com/guide

#### **技术架构**：

三维场景通过51WORLD云端渲染，将实时视频流推送到网页端，并且能够实现前端到后端的交互同步。

SuperAPI提供可在WEB端进行调用的方法，以便用户从前端页面直接向51WORLD云渲染平台程序发送指令。同时通过注册函数监听51WORLD云渲染程序发送的事件，用户再跟据事件类型，在WEB端中对此类事件做出响应。

#### 云渲染技术描述：

在拥有GPU的服务器部署使用图形API（D3D\OpenGL）进行实时渲染的3D可视化应用软件（下称“渲染服务软件”），通过Nvidia Enc实时编码渲染的图像到h264格式（低延迟），并通过[WEB-RTC](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling)协议（流控）发送到客户端。客户端是一个能够运行在手机、平板电脑、普通PC主机的应用程序（或者网页），用作3D可视化的展示，并且收集用户的操作通过[WEB-RTC](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling)回传给服务器。最终，这套解决方案用来实现复杂3D应用（照片级真实的渲染效果）能够在手机、平板电脑、普通PC主机、甚至网页上进行远程的展示与实时的互动。

#### API安装引用

文档地址：http://superapi.51aes.com/study

① 直接用 <script 引入SuperAPI:

引入指定版本

	<script src="http://superapi.51aes.com/dist/superAPI-6.2.0.min.js"></script>

② 通过npm, cnpm, yarn安装:

✍️npm i 51superapi	✍️cnpm install 51superapi		✍️yarn add 51superapi

✍️import cloudRenderer from "51superapi"

#### superAPI 与 WDP 平台场景链接

渲染口令：在 WDP 管理中心中获取案例渲染口令

渲染地址：

云渲染则直接填写云渲染地址即可

本地渲染：本机ip+8889	("http://127.0.0.1:8889")

具体文档：http://superapi.51aes.com/study

启动渲染代码块：

```javascript
var cloudRender = new cloudRenderer("player", 0);
var cloudurl = "http://127.0.0.1:8889" //本地渲染服务地址, 8889:固定端口
var orderID = 'a5EEE4Fe' //渲染口令
//启动渲染
function start51() {
  fetch(`${cloudurl}/Renderers/Any/order`, {
    method: "POST",
    headers: {
      "Content-type": "application/json"
    },
    body: JSON.stringify({
      order: orderID,
      width: window.innerWidth,
      height: window.innerHeight
    })
    //① order 渲染口令必填; ② width, height: 设置云渲染输出分辨率(此设置为固定分辨率,可选; 默认以云渲染客户端设置的分辨率输出)
  }).then(res => {
    if (!res.ok) {
      throw Error(res.statusText)
    }
    return res.json()
  }).then(json => {
    if (json.url) {
      console.log(json.url, '云渲染url');
      //启动云渲染
      cloudRender.SuperAPI("StartRenderCloud", json.url);
      //事件注册
      cloudRender.SuperAPI("RegisterCloudResponse", myHandleResponseFunction);
    } else {
      console.log(json.message);
    }
  }).catch((error) => {
    console.error('Error: ', error);
  });
}
```

#### 可实现功能：

相关文档：http://superapi.51aes.com/apifunc

1. 镜头控制
2. 场景编辑
3. 覆盖物编辑
4. POI点
5. 点聚合
6. 楼体拆解
7. 路径
8. 场景热力图、迁徙图、战略图、区域轮廓图、区域高亮
9. 场景控件
10. 坐标计算

## SuperGUI

具体详情：SuperGUI主要用于在PaaS平台中对场景进行操作

学习地址：https://bbs.51aes.com/study/list/1414471703908311041.html?page=2	（课程 19 开始）

#### 可用功能：

模板，场景，模型，交互，面板（包含蓝图），模型导入

模板，场景，模型，交互，模型导入均为拖拽式操作，不予过多讲述

- 面板：

- - 主要用于对场景进行便捷式交互操作，可实现数据请求、图表展示、组件化信息展示、基础交互、图标展示、素材上传等操作
  - 面板中各种展示内容均可进行尺寸、样式、数据（可以通过请求实现动态数据）、交互等操作
  - 面板的初衷即为 PPT 式开发独立展示页面，可以实现 0 代码开发、操作式开发，可以快速生成可用单页面

- 面板联调：

- - 可对蓝图中使用到的逻辑操作、superApi等功能进行在线调试

## 蓝图编辑器

相关文档：https://bbs.51aes.com/blog/1425349665726164994.html

基础视频教程：https://ossstore.51hitech.com/BBSOperation/WDP4.0/Mid_Training/WDP4-MT24.mp4

- 主要用于在 SuperGUI 编辑面板时实现更多更自由的交互逻辑操作
- 主要内容：元素节点，交互节点，逻辑节点，场景节点
- 实现流程：

1. 1. 通过元素节点相关事件作为初始化触发条件
   2. 可以 触发逻辑节点相关操作、触发场景节点中 SuperAPI 事件、触发接口操作等
   3. 其中各个节点也可互相触发
   4. 达到无代码处理复杂操作的功能

#### 具体面板：

![image-20221020152930420](https://raw.githubusercontent.com/pan52yu/blogImg/main/image-20221020152930420.png)



## 相关资料

51world平台：https://www.51aes.com/

PaaS平台使用说明：https://djp2gcp2um.feishu.cn/docs/doccncXWIo0B47godjUyVf3N9Fe#

SuperAPI开发手册：http://superapi.51aes.com/guide

开发者社区：https://bbs.51aes.com/

官方qq群：（注册开发者后发送注册账号即可通过验证）

![image-20221020152955883](https://raw.githubusercontent.com/pan52yu/blogImg/main/image-20221020152955883.png)
