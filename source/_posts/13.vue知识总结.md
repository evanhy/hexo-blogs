---
title: vue知识总结
tags:
  - vue
categories:
  - 面试
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/wallhaven-rd2x5w_1920x1080.webp?x-oss-process=style/huyu
abbrlink: 58641
date: 2022-02-11 21:02:13
---

#### 1、vue 修改数据页面不重新渲染

> `vue2`是用过`Object.defineProperty`实现数据响应式, 组件初始化时，对 data 中的 item 进行递归遍历，对 item 的每一个属性进行劫持，添加 set , get 方法。我们后来 新加的属性 ，并没有通过`Object.defineProperty`设置成响应式数据，修改后不会视图更新

**通过数组索引号修改了数组，界面会不会相应更新？为什么？**

答：不会。vue 监听不到

vue 为什么没有提供 `arr[下标] = val` 变成响应式？

尤大："因为性能问题，性能代价和获得的用户体验收益不成正比"

###### **数组/对象的响应式 ，vue 里面是怎么处理的？**

**对象**：使用了`Object.defineProperty`中的 get 和 set

> 如何监测对象中的数据？
> 通过 setter 实现监视，且要在 new Vue 时就传⼊要监测的数据
>
> 1. 对象中后追加的属性，Vue 默认不做响应式处理
> 2. 如需给后添加的属性做响应式，请使⽤如下 API：
>    `Vue.set(target,propertyName/index,value)`  `vm.$set(target,propertyName/index,value)`

**[数组](https://cn.vuejs.org/v2/guide/list.html#%E6%95%B0%E7%BB%84%E6%9B%B4%E6%96%B0%E6%A3%80%E6%B5%8B)**： `Vue`重写了数组的原型，更准确的表达是**拦截**了数组的原型

> 如何监测数组中的数据？
> 通过包裹数组更新元素的⽅法实现，本质就是做了两件事：
>
> 1. 调⽤原⽣对应的⽅法对数组进⾏更新
> 2. 重新解析模板，进⽽更新⻚⾯
> 3. 在 Vue 修改数组中的某个元素⼀定要⽤如下⽅法：
>
>    1. 使 ⽤ 这 些 API ：
>       `push() pop() shift() unshift() splice() sort() reverse()`
>    2. `Vue.set()` 或 `vm.$set()`
>    3. 覆盖整个数组

**为什么对象和数组要分开处理?**

> `对象`的属性通常比较少，对每一个属性都劫持`set和get`，并不会消耗很多性能
>
> `数组`有可能有成千上万个元素，如果每一个元素都劫持`set和get`，无疑消耗太多性能了
>
> 所以`对象`通过`defineProperty`进行正常的劫持`set和get`
>
> `数组`则通过`修改数组原型上的部分方法`，来实现`修改数组触发响应式`



#### 2、生命周期

##### Vue 生命周期都有哪些？

|    生命周期     | 执行时机                                                     |
| :-------------: | :----------------------------------------------------------- |
| `beforeCreate`  | 在组件实例被创建之初、组件的属性⽣效之前被调用               |
|    `created`    | 在组件实例已创建完毕。此时属性也已绑定，但真实 DOM 还未⽣成，$el 还不可⽤ |
|  `beforeMount`  | 在组件挂载开始之前被调⽤。相关的 render 函数⾸次被调⽤       |
|    `mounted`    | 在 el 被新建的 vm.$el 替换并挂载到实例上之后被调用           |
| `beforeUpdate`  | 在组件数据更新之前调⽤。发⽣在虚拟 DOM 打补丁之前            |
|    `update`     | 在组件数据更新之后被调用                                     |
|   `activited`   | 在组件被激活时调⽤（使用了 `<keep-alive>` 的情况下）         |
|  `deactivated`  | 在组件被销毁时调⽤（使用了 `<keep-alive>` 的情况下）         |
| `beforeDestory` | 在组件销毁前调⽤                                             |
|   `destoryed`   | 在组件销毁后调⽤                                             |

##### 父子组件生命周期执行顺序

**加载渲染过程**

```js
	->父beforeCreate
  ->父created
  ->父beforeMount
  ->子beforeCreate
  ->子created
  ->子beforeMount
  ->子mounted
  ->父mounted
```

**更新过程**

```js
父beforeUpdate->子beforeUpdate->子updated->父updated
```

**销毁过程**

```js
父beforeDestroy->子beforeDestroy->子destroyed->父destroyed
```

`keep-alive`可以实现组件缓存，当组件切换时不会对当前组件进行卸载

https://juejin.cn/post/6844903641866829838#heading-11

https://juejin.cn/post/7114252241166401573



#### 3、watch 和 computed 的区别

官方文档：对于任何复杂逻辑，你都应当使用计算属性

扩展：[vue 中 Computed、Methods、Watch 区别](https://blog.csdn.net/weixin_43638968/article/details/103593954)

|                     computed（计算属性）                     |                   watch（监视属性/侦听器）                   |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| 根据你所依赖的数据动态显示新的计算结果<br />不用再 data 中声明，否则报错 | data 的数据监听回调,依赖 data 的数据变化<br />直接使用 data 声明的数据 |
|                           支持缓存                           |                          不支持缓存                          |
|                          不支持异步                          |                           支持异步                           |
|       有 get 和 set 方法，当数据变化时，调用 set 方法        | 可以深度监视 deep,加载就调用 immediate<br />监听的函数接收两个函数，newVal 和 oldVla |
|    当需要进行数值计算，并且依赖于其它数据时，用 computed     |     在某个数据变化时做一些事情或需要异步操作时，用 watch     |
|                       computed 能做的                        |                        watch 都能做到                        |



#### 4、组件通信（组件间传值）

> - `props` `$emit`
> - `.sync` `v-model`
> - `$parent / $children` $parent 获取父组件的实例，任意调用父组件的方法，修改父组件的数据
> - `ref` 父组件获取 子组件 实例，任意调用子组件的方法获取子组件的属性
> - `provide / inject`prpvide 父组件内部提供数据 inject 嵌套的子组件可以注入数据
> - `$attrs / $listeners` $attrs(没有被 props 接收的所有自定义属性) $listeners(可以获取所有的父组件传递过来的自定义事件)
> - `eventBus` 定义一个事件总线 使用`$on` 绑定 `$emit` 触发
> - `vuex`
> - 路由传参

https://www.wpgdadatong.com/cn/blog/detail?BID=B3650

https://juejin.cn/post/7110223595359436813



#### 5、$nextTick

**NextTick 是什么**

> 在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM

```js
// 修改数据
this.message = "修改后的值";
// 此时DOM还没有更新
console.log(this.$el.textContent); // => '原始的值'
this.$nextTick(function () {
  // DOM 更新了
  console.log(this.$el.textContent); // => '修改后的值'
});
```

> 应用场景：因为 vue 是异步执行 dom 更新的，所以当你希望在更新数据之后，操作新的视图， 那么你的操作逻辑应写在 Vue.nextTick(callback) 的回调中，而这个回调会在dom 更新循环结束之后执行。 否则，因为异步更新 dom 的原因，如果你不是在 Vue.nextTick(callback) 的回调中执行操作新视图， 那么可能会发生意外。例如你在 created()钩子 是不能操作 dom 的，但你可以在此调接口更新数据， 如果你此时希望接口更新完毕数据后，接着调用操作 dom 的逻辑，那么最后将这部分操作 dom 的逻辑， 放置在Vue.nextTick(callback) 的回调函数中。

#### 6、修饰符

引用：https://juejin.cn/post/7026867875990208543#heading-22

##### 事件修饰符

`stop`

- `stop`修饰符的作用是阻止冒泡

`prevent⭐`

- `prevent`修饰符的作用是**阻止默认事件**（例如 a 标签的跳转）

`capture`

- 事件流默认冒泡，使用 capture 进行捕获**捕获**

`self`

- `self`修饰符作用是，只有点击**事件绑定的本身**才会触发事件

`once`

- `once`修饰符的作用是，事件**只执行一次**

`native⭐`

- `native`修饰符是加在**自定义组件**的事件上，保证事件能执行
- `native`是用来是在父组件中给子组件绑定一个**原生的事件**，就将子组件变成了普通的 HTML 标签看待

`passive`

- 当我们在监听元素滚动事件的时候，会一直触发 onscroll 事件，在 pc 端是没啥问题的，但是在移动端，会让我们的网页变卡，因此我们使用这个修饰符的时候，相当于给 onscroll 事件整了一个.lazy 修饰符

##### v-bind 修饰符

`sync⭐`

- 当`父组件`传值进`子组件`，子组件想要改变这个值时，可以这么做

- ```js
  // 父组件里
  <children :foo.sync="bar"></children>
  // 子组件里
  this.$emit('update:foo', newValue)
  ```

`camel`

`.camel` 修饰符允许在使用 DOM 模板时将 `v-bind` property 名称驼峰化，例如 SVG 的 `viewBox` property：

```js
<svg :view-box.camel="viewBox"></svg>
```

##### 鼠标修饰符

`left` `right` `middle`

- 这三个修饰符是鼠标的左中右按键触发的事件

##### 表单相关修饰符

`trim⭐`

- `trim`修饰符的作用类似于 JavaScript 中的`trim()`方法，作用是把`v-model`绑定的值的首尾空格给过滤掉。

`lazy`

- `lazy`修饰符作用是，改变输入框的值时 value 不会改变，当光标离开输入框时，`v-model`绑定的值 value 才会改变

`number`

- `number`修饰符的作用是将值转成数字，但是先输入字符串和先输入数字，是两种情况：

> 先输入数字的话，只取前面数字部分
>
> 先输入字母的话，`number`修饰符无效

##### 系统修饰符

> `.ctrl`、`.alt`、`.shift`、`.meta`

##### 按键修饰符

> 配合键盘事件使用: `.enter` `.tab` `.delete` `.esc` `.space` `.up` `.down` `left`......



#### 7、图片上传 TODO⭐⭐

[FileReader 与 URL.createObjectURL 实现图片、视频上传预览](https://juejin.cn/post/6844903507640713223)

event.target.files 就是用户上传的图片信息

配合 cropperjs 可以实现图片裁剪功能

```js
// 如果接口要求 Content-Type 是 multipart/form-data
// 则你必须传递 FormData 对象
```



#### 8、v-for 和 v-if / v-if 和 v-show

[**为什么 v-if 和 v-for 不能同时使用？**](https://juejin.cn/post/7106437556354416677)

- v-if 不能和 v-for 一起使用的原因是 v-for 的优先级比 v-if 高，一起使用会造成性能浪费

- 解决方案有两种，把 v-if 放在 v-for 的外层或者把需要 v-for 的属性先从计算属性中过滤一次

- v-if 和 v-for 的优先级问题在 vue3 中不需要考虑，vue3 更新了 v-if 和 v-for 的优先级，使 v-if 的优先级高于 v-for

**v-if 和 v-show**

`v-show`隐藏则是为该元素添加`display:none`。`v-if`是将`dom`元素整个添加或删除

- `v-show` 由`false`变为`true`的时候不会触发组件的生命周期
- `v-if`由`false`变为`true`的时候，触发组件的`beforeCreate`、`create`、`beforeMount`、`mounted`钩子，由`true`变为`false`的时候触发组件的`beforeDestory`、`destoryed`方法

性能消耗：`v-if`有更高的切换消耗；`v-show`有更高的初始渲染消耗；

如果需要非常频繁地切换，则使用 v-show 较好

如果在运行时条件很少改变，则使用 v-if 较好



#### 9、Vue.use()

`Vue.use`是用来安装插件的

**用法**：Vue.use(plugin)

- **如果插件是一个对象**，必须提供 `install` 方法。
- **如果插件是一个函数，它会被作为 install 方法**。install 方法调用时，`会将 Vue 作为参数传入`。
- Vue.use(plugin)调用之后，插件的 install 方法就会默认接受到一个参数，这个参数就是 Vue

**总结：Vue.use 是官方提供给开发者的一个 api，用来注册、安装类似 Vuex、vue-router、ElementUI 之类的插件的**



#### 10、 跨域

> 线上环境的跨域问题
> 1、cors >> 后端加响应头 access-control-allow-origin: '*' 允许所有的页面请求
> 2、代理(主流方案)
> 开发环境 ：webpack的devServer进行代理，本地请求转发到接口 vue.config.js > devServer > proxy {}
> 线上环境： 服务器端使用 nginx 反向代理，拦截请求转发到线上接口, 解决跨域问题

##### **什么是跨域**

> 跨域问题是浏览器的**同源策略**所导致的
>
> 其中，**域名、协议、端口号**相同，称之为同源，如果不同，称之为跨源或**跨域**

**跨域常见的解决方法：**

- **代理**，常用
- **CORS**，常用
- JSONP

##### 跨域解决方法 1-代理

> 代理适用的场景是：生产环境不发生跨域，但开发环境发生跨域
>
> 因此，只需要在开发环境使用代理解决跨域即可，这种代理又称之为开发代理

在实际开发中，只需要对开发服务器稍加配置即可完成

```js
// vue 的开发服务器代理配置
// vue.config.js
module.exports = {
  devServer: {
    // 配置开发服务器
    proxy: {
      // 配置代理
      "/api": {
        // 若请求路径以 /api 开头
        target: "http://dev.taobao.com", // 将其转发到 http://dev.taobao.com
      },
    },
  },
};
```

##### 跨域解决方法 2-CORS

阮一峰 CORS: https://www.ruanyifeng.com/blog/2016/04/cors.html

> `CORS`是基于`http1.1`的一种跨域解决方案，它的全称是**C**ross-**O**rigin **R**esource **S**haring，**跨域资源共享**。
>
> CORS 需要浏览器和后端同时支持。
>
> 整个 CORS 通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS 通信与同源的 AJAX 通信没有差别，代码完全一样。浏览器一旦发现 AJAX 请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。
>
> 因此，实现 CORS 通信的关键是服务器。只要服务器实现了 CORS 接口，就可以跨源通信。

浏览器将 CORS 请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）

只要同时满足以下两大条件，就属于简单请求。

凡是不同时满足下面两个条件，就属于非简单请求。

> （1) 请求方法是以下三种方法之一：
>
> - HEAD
> - GET
> - POST
>
> （2）HTTP 的头信息不超出以下几种字段：
>
> - Accept
> - Accept-Language
> - Content-Language
> - Last-Event-ID
> - Content-Type：只限于三个值`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`

##### 跨域解决方法 3-JSONP

> JSONP 的做法是：当需要跨域请求时，不使用 AJAX，转而生成一个 script 元素去请求服务器，由于浏览器并不阻止 script 元素的请求，这样请求可以到达服务器。服务器拿到请求后，响应一段 JS 代码，这段代码实际上是一个函数调用，调用的是客户端预先生成好的函数，并把浏览器需要的数据作为参数传递到函数中，从而间接的把数据传递给客户端
>
> JSONP 优点是简单兼容性好，可用于解决主流浏览器的跨域数据访问的问题。**缺点是仅支持 get 方法具有局限性,不安全可能会遭受 XSS 攻击。**



#### 11、cookie

什么是 cookie

> cookie 是储存在用户本地终端上的数据，是网站为了**识别用户**和**跟踪会话**而存储在用户本地终端中的文本数据

怎么操作

> 可以使用`js-cookie`插件
>
> 模块化开发时直接引入`import Cookies from 'js-cookie'`
>
> js-cookie.js 常用的 API 和方法
>
> 设置 cookie
>
> ```javascript
> Cookies.set("name", "value", { expires: 7, path: "" }); //7天过期
> Cookies.set("name", { foo: "bar" }); //设置一个json
> ```
>
> 读取 cookie
>
> ```javascript
> Cookies.get("name"); //获取cookie
> Cookies.get(); //读取所有的cookie
> ```
>
> 删除 cookie
>
> ```javascript
> Cookies.remove("name"); //删除cookie时必须是同一个路径。
> ```



#### 12、[keep-alive 原理](https://blog-1gxfs2h1401f4f58-1254415986.tcloudbaseapp.com/%E9%9D%A2%E8%AF%95%E9%A2%98%E7%B3%BB%E5%88%97/%E4%BA%8C%E3%80%81%E5%AF%B9keep-alive%E7%9A%84%E7%90%86%E8%A7%A3.html#%E5%8E%9F%E7%90%86)

##### keep-alive 是什么

> 1. vue 自带的组件 >> 主要功能是缓存组件 >> 提升性能
> 2. 使用场景：可以少网络请求，如果当前组件数据量比较大，就可以节省网络请求 >> 提升用户体验
> 3. 举例：如果详情页面之间进行切换，就可以使用`keep-alive`进行缓存组件，防止同样的数据重复请求

`keep-alive` 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们

`keep-alive`可以设置以下`props`属性：

- `include` - 字符串或正则表达式。只有名称匹配的组件会被缓存
- `exclude` - 字符串或正则表达式。任何名称匹配的组件都不会被缓存
- `max` - 数字。最多可以缓存多少组件实例

关于`keep-alive`的基本用法：

```go
<keep-alive>
  <component :is="view"></component>
</keep-alive>
```

匹配首先检查组件自身的 `name` 选项，如果 `name` 选项不可用，则匹配它的局部注册名称 (父组件 `components` 选项的键值)，匿名组件不能被匹配

设置了 keep-alive 缓存的组件，会多出两个生命周期钩子（`activated`与`deactivated`）：

- 首次进入组件时：`beforeRouteEnter` > `beforeCreate` > `created`> `mounted` > `activated` > ... ... > `beforeRouteLeave` > `deactivated`
- 再次进入组件时：`beforeRouteEnter` >`activated` > ... ... > `beforeRouteLeave` > `deactivated`

##### **使用场景**

使用原则：当我们在某些场景下不需要让页面重新加载时我们可以使用`keep-alive`

举个栗子:

当我们从`首页`–>`列表页`–>`商详页`–>`再返回`，这时候列表页应该是需要`keep-alive`

从`首页`–>`列表页`–>`商详页`–>`返回到列表页(需要缓存)`–>`返回到首页(需要缓存)`–>`再次进入列表页(不需要缓存)`，这时候可以按需来控制页面的`keep-alive`

在路由中设置`keepAlive`属性判断是否需要缓存

```js
{
  path: 'list',
  name: 'itemList', // 列表页
  component (resolve) {
    require(['@/pages/item/list'], resolve)
 },
 meta: {
  keepAlive: true,
  title: '列表页'
 }
}
```

使用`<keep-alive>`

```js
<div id="app" class='wrapper'>
    <keep-alive>
        <!-- 需要缓存的视图组件 -->
        <router-view v-if="$route.meta.keepAlive"></router-view>
     </keep-alive>
      <!-- 不需要缓存的视图组件 -->
     <router-view v-if="!$route.meta.keepAlive"></router-view>
</div>
```

##### 缓存后如何获取数据

解决方案可以有以下两种：

- beforeRouteEnter
- actived



#### 13、ref

作用：

- 放到 dom 节点上 >> 获取原生 dom
- 组件身上 >> 获取组件实例 >> 可以获取组件内部所有的方法和数据



#### 14、scoped 原理是什么？

作用：使**样式私有化（模块化）**，不对全局造成污染

原理：动态的给组件加上一个 hash 值，用属性选择器去匹配



#### 15、$router 和 $route

**$route**：当前的路由信息对象，获取到路由参数、路径

> 1. **$route.path：** 字符串，对应当前路由的路径，总是解析为绝对路径，如`/foo/bar`。
> 2. **$route.params：** 一个 key/value 对象，包含了 动态片段 和 全匹配片段，如果没有路由参数，就是一个空对象。
> 3. **$route.query：** 一个 key/value 对象，表示 URL 查询参数。例如，对于路径 /foo?user=1，则有$route.query.user == 1，如果没有查询参数，则是个空对象。
> 4. **$route.hash：** 当前路由的 hash 值 (不带#) ，如果没有 hash 值，则为空字符串。锚点
> 5. **$route.fullPath：** 完成解析后的 URL，包含查询参数和 hash 的完整路径。
> 6. **$route.matched：** 数组，包含当前匹配的路径中所包含的所有片段所对应的配置参数对象。
> 7. **$route.name：** 当前路径名字
> 8. **$route.meta：** 路由元信息

**$router**：全局路由 vueRouter 的实例，挂载到 Vue 原型上$router 属性，可以获取到全局路由配置信息，跳转方法

> $router.replace({path:'home'})，//替换路由，没有历史记录
>
> $router.push('/login') ,跳转到指定路由
>
> $router.back()
>
> $router.go()



#### 16、[发布订阅模式和观察者模式](https://juejin.cn/post/7055441354054172709#heading-3)

##### 发布订阅模式

> 发布订阅模式中有三个角色，发布者 `Publisher` ，信息中心 `Event Channel` ，订阅者 `Subscriber`
>
> 我们假定，存在一个"信号中心"，某个任务执行完成，就向信号中心"发布"（publish）一个信号，其他任务可以向信号中心"订阅"（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做"发布/订阅模式"（publish-subscribe pattern)
>
> **通常是通过 on 事件订阅消息，emit 事件发布消息，remove 事件删除订阅**

```js
class PubSub {
  constructor() {
    // 事件中心
    // 存储格式: warTask: [], routeTask: []
    // 每种事件(任务)下存放其订阅者的回调函数
    this.events = {};
  }
  // 订阅方法
  subscribe(type, cb) {
    if (!this.events[type]) {
    }
    this.events[type].push(cb);
  }
  // 发布方法
  publish(type, ...args) {
    if (this.events[type]) {
      this.events[type].forEach((cb) => cb(...args));
    }
  }
  // 取消订阅方法
  unsubscribe(type, cb) {
    if (this.events[type]) {
      const cbIndex = this.events[type].findIndex((e) => e === cb);
      if (cbIndex !== -1) {
        this.events[type].splice(cbIndex, 1);
      }
    }
    if (this.events[type].length === 0) {
      delete this.events[type];
    }
  }
  unsubscribeAll(type) {
    if (this.events[type]) {
      delete this.events[type];
    }
  }
}
```

##### 观察者模式

当对象之间存在一对多的依赖关系时，其中一个对象的状态发生改变，所有依赖它的对象都会收到通知，这就是**观察者模式**

> - 观察者(订阅者) -- Watcher
>   - update()：当事件发生时，具体要做的事情
> - 目标(发布者) --  Dep
>   - subs 数组：存储所有的观察者
>   - addSub()：添加观察者
>   - notify()：当事件发生，调用所有观察者的 update() 方法
> - 没有事件中心

##### 总结

| 设计模式     | 观察者模式                                    | 发布订阅模式                                               |
| ------------ | --------------------------------------------- | ---------------------------------------------------------- |
| 主体         | Watcher 观察者、Dep 目标对象                  | Publisher 发布者、Event Channel 信息中心、Subscribe 订阅者 |
| 主体关系<br> | Dep 中通过 subs 记录 Watcher                  | Publisher 和 Subscribe 不想不知道对方，通过中介联系        |
| 优点         | 角色明确，Watcher 和 Dep 要遵循约定的成员方法 | 松散耦合，灵活度高，通常应用在异步编程中                   |
| 缺点         | 紧耦合                                        | 当事件类型变多时，会增加维护成本                           |
| **使用案例** | 双向数据绑定                                  | 事件总线 EventBus                                          |

#### 17、[vue 响应式原理（数据劫持）](https://blog-1gxfs2h1401f4f58-1254415986.tcloudbaseapp.com/vue%E5%8E%9F%E7%90%86/vue2%E5%8E%9F%E7%90%86/1.%E5%93%8D%E5%BA%94%E5%BC%8F%E5%8E%9F%E7%90%86.html)



#### 18、eventBus



#### 19、v-model 与.sync

> **相同点**：都是语法糖，都可以实现父子组件中的数据的双向通信。
>
> ```js
> //  v-model
> <son  v-model="num"/> //父组件使用子组件
> model:{
> 	prop:'newValue', // 默认为 value 可以使用prop自定义属性名
> 	event:"updateValue", // event 修改事件名 默认为input
> },
> props: { // 子组件接收
> 	value: {  // 默认为value
> 		type: Number,
> 	}
> }
> this.$emit('input', '小红')
> // .sync
> <son :title.sync="doc.title"></son>   // 父组件
> props:{
> 	title:{
> 	  type:...
> 	}
> }
> this.$emit('update:title', newTitle)  // 子组件
> ```
>
> **区别点**：格式不同： v-model="num", :num.sync="num"
>
> `v-model`：    @input + value
> `:num.sync`:  @update:num
>
> **另外需要特别注意的是:** `v-model`只能用一次；`.sync`可以有多个



#### 20、如何做样式穿透

**背景**：修改当前组件嵌套的子组件内部的样式

**问题**：

1. 如果不添加 scoped，可能影响全局样式
2. 如果添加 scoped，嵌套的子组件内部样式不能生效

**如何解决**：添加`/deep/` / `::v-deep`

> scss: 使用`::v-deep`
>
> less: 使用 `/deep/`



#### 21、你对SPA单页面的理解，它的优缺点分别是什么？

> SPA（ single-page application ）仅在 Web 页面初始化时加载相应的 HTML、JavaScript 和 CSS。一旦页面加载完成，SPA 不会因为用户的操作而进行页面的重新加载或跳转；取而代之的是利用路由机制实现 HTML 内容的变换，UI 与用户的交互，避免页面的重新加载。

###### 单页应用优缺点

优点：

- 具有桌面应用的即时性、网站的可移植性和可访问性
- 用户体验好、快，内容的改变不需要重新加载整个页面，SPA 相对对服务器压力小；
- 良好的前后端分离，分工更明确

缺点：

- SEO（搜索引擎）难度较大
- 首次渲染速度相对较慢
- 前进后退路由管理：由于单页应用在一个页面中显示所有的内容，所以不能使用浏览器的前进后退功能，所有的页面切换需要自己建立堆栈管理；



#### 22、怎么理解 Vue 的单向数据流

> ① 单向数据流是指数据从父组件传向子组件，子组件没有权限直接修改该数据；
> ② 子组件需要在 data 或者 computed 中重新定义变量来接收父组件传来的值，以便修改；
> ③ 子组件可以通过 $emit 的方式通知父组件修改值，再重新传回给子组件；



#### 23、路由懒加载

###### 为什么要使用路由懒加载

> 为给客户更好的客户体验，首屏组件加载速度更快一些，解决白屏问题

###### 定义

> 懒加载简单来说就是延迟加载或按需加载，即在需要的时候的时候进行加载

> 路由 和 组件 的常用两种懒加载方式：
>
> 1、vue异步组件实现路由懒加载 `component：resolve=>(['需要加载的路由的地址'，resolve])`
>
> 2、es提出的import(推荐使用这种方式) `const HelloWorld = （）=> import('需要加载的模块地址')`

通过魔法注释webpackChunkName修改名称，打包时就会显示修改后的新名称

```js
const { name } = await import(/* webpackChunkName:"newName"*/'路径')
```



#### 24、如何理解vue的key属性

![image-20220811205624543](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/GMFRTZ1Ew2igUmP.png?x-oss-process=style/huyu)

> 	   	   **key 是为 Vue 中 vnode 的唯一标记，通过这个 key，我们的 diff 操作可以更准确、更快速，以便高效的更新虚拟 DOM**
>
> 	   	   1.  对⽐规则：
>
> 	   	       1. 旧虚拟DOM中找到了与新虚拟DOM相同的key：
> 	   	           1. 若虚拟DOM中内容没变, 直接使⽤之前的真实DOM
> 	   	           2. 若虚拟DOM中内容变了, 则⽣成新的真实DOM，随后替换掉⻚⾯中之前的真
> 	   	                实DOM
>
> 	   	       2. 旧虚拟DOM中未找到与新虚拟DOM相同的key：创建新的真实DOM，随后渲染到
> 	   	           到⻚⾯
>
> 	   	         2. ⽤index作为key可能会引发的问题：
> 	   	          1. 若对数据进⾏逆序添加、逆序删除等破坏顺序操作：会产⽣没有必要的真实DOM
> 	   	              更新 ==> 界⾯效果没问题, 但效率低
> 	   	          2. 若结构中还包含输⼊类的DOM：会产⽣错误DOM更新 ==> 界⾯有问题
> 	   	         3. 开发中如何选择key?
> 	   	          1. 最好使⽤每条数据的唯⼀标识作为key，⽐如id、⼿机号、⾝份证号、学号等唯⼀
> 	   	              值
> 	   	          2. 如果不存在对数据的逆序添加、逆序删除等破坏顺序的操作，仅⽤于渲染列表，
> 	   	              使⽤index作为key是没有问题的



#### 25、vue-router 有哪几种导航钩子

https://router.vuejs.org/zh/guide/advanced/navigation-guards.html

https://router.vuejs.org/zh/guide/advanced/meta.html

**全局导航钩子**

+ `router.beforeEach(to,from,next)`：添加一个导航守卫，在任何**导航前**执行
+ `router.beforeResolve(to,from)`：添加一个导航守卫，在**导航即将解析之前**执行。在这个状态下，所有的组件都已经被获取，并且其他导航守卫也已经成功
+ `router.afterEach(to,from)`：添加一个导航钩子，**在每次导航后执行**

**组件内的钩子**

+ `beforeRouterEnter(to, from, next)`：在渲染该组件的对应路由被验证前调用
+ `beforeRouterUpdate`：在当前路由改变，但是该组件被复用时调用
+ `beforeRouterLeave`：在导航离开渲染该组件的对应路由时调用

```js
const UserDetails = {
  template: `...`,
  beforeRouteEnter(to, from,next) {
    // 在渲染该组件的对应路由被验证前调用
    // 不能获取组件实例 `this` ！
    // 因为当守卫执行时，组件实例还没被创建！
  },
  beforeRouteUpdate(to, from) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 `/users/:id`，在 `/users/1` 和 `/users/2` 之间跳转的时候，
    // 由于会渲染同样的 `UserDetails` 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 因为在这种情况发生的时候，组件已经挂载好了，导航守卫可以访问组件实例 `this`
  },
  beforeRouteLeave(to, from) {
    // 在导航离开渲染该组件的对应路由时调用
    // 与 `beforeRouteUpdate` 一样，它可以访问组件实例 `this`
  },
}
```

**路由独享的守卫**

+ `beforeEnter`:**只在进入路由时触发**，不会在 `params`、`query` 或 `hash` 改变时触发

```js
const routes = [
  {
    path: '/users/:id',
    component: UserDetails,
    beforeEnter: (to, from) => {
      // reject the navigation
      return false
    },
  },
]
```

![image-20220809005712651](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/crRNltVTHoA8nXW.png?x-oss-process=style/huyu)



#### 26、什么是 MVVM？（深入待总结）

![img](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/4BpRdQsYCcEuwe6.webp?x-oss-process=style/huyu)

> - Model：模型层（数据层），主要用于保存一些数据
> - View： 视图层，主要用于将后端数据借助各种元素呈现给用户，同时也可提供用户操作的入口
> - ViewModel：视图模型层：该层也是mvvm中的核心层，主要用于作为Model个View两个层的数据连接层，负责两个层之间的数据传递。该层主要包含两大功能点：
>   - DOM监听（DOM Listener）视图变化后更新数据
>   - 数据绑定（Data bindings）数据变化后更新视图

#### 27、Vue 是如何实现数据双向绑定的？

https://juejin.cn/post/6950939562872930341

> Vue 数据双向绑定主要是指：数据变化更新视图，视图变化更新数据

Vue 主要通过以下 4 个步骤来实现数据双向绑定的：

1. 需要Observe（观察者）的数据对象进行递归遍历，包括子属性对象的属性，都加上setter和getter这样的话，给这个对象的某个值赋值，就会触发setter，那么就能监听到了数据变化
2. Compile（编译）解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图
3. Watcher订阅者是Observer和Compile之间通信的桥梁，主要做的事情是: ①在自身实例化时往属性订阅器(dep)里面添加自己 ②自身必须有一个update()方法 ③待属性变动dep.notify()通知时，能调用自身的update()方法，并触发Compile中绑定的回调，则功成身退。
4. MVVM作为数据绑定的入口，整合Observer、Compile和Watcher三者，通过Observer来监听自己的model数据变化，通过Compile来解析编译模板指令，最终利用Watcher搭起Observer和Compile之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据model变更的双向绑定效果。

以上四个步骤的流程图表示如下，如果有同学理解不大清晰的，可以查看作者专门介绍数据双向绑定的文章[《0 到 1 掌握：Vue 核心之数据双向绑定》](https://juejin.cn/post/6844903903822086151)，有进行详细的讲解、以及代码 demo 示例

![1158910-20180306234148823-430002059.png](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/ZykbSiv6VjH3Udl.webp?x-oss-process=style/huyu)



#### 28、Vue.observable你有了解过吗？说说看

 https://cowtransfer.com/s/1ef3d485042d4e 点击链接查看 [ my2.jpg ] ，或访问奶牛快传 cowtransfer.com 输入传输口令 uk9ct9 查看；



#### 29、v-model 的原理？

我们在 vue 项目中主要使用 v-model 指令在表单 input、textarea、select 等元素上创建双向数据绑定，v-model 本质上不过是语法糖，v-model 在内部为不同的输入元素使用不同的属性并抛出不同的事件：

- text 和 textarea 元素使用 value 属性和 input 事件；
- checkbox 和 radio 使用 checked 属性和 change 事件；
- select 字段将 value 作为 prop 并将 change 作为事件。

以 input 表单元素为例：

```js
<input v-model='something'>
// 相当于
<input v-bind:value="something" v-on:input="something = $event.target.value">
```

#### 30、data是个函数不是个对象

> 首先，data是个函数是确保数据不会被污染，在JS中对象是一个引用数据类型，如果是对象形式，多个vue实例会相互影响，为了保证组件的复用性，data要写成函数，函数形式每次使用时，会返回一个新的对象拷贝，这样使用组件时data中的数据就不会被相互影响
>
> new Vue的实例，比如app.js中，是不会被复用的，所以不存在数据污染的情况

#### 31、路由三种模式

**hash模式**

**history模式**

**abstract模式**

#### 32、hash模式和history模式（补充）

https://juejin.cn/post/6867875626611654663

###### 前端路由原理

前端路由的核心，就在于改变视图的同时不会向后端发出请求；而是加载路由对应的组件。vue-router就是将组件映射到路由, 然后渲染出来的。并实现了三种模式：Hash模式、History模式以及Abstract模式。默认Hash模式

**hash模式** ： #后面是路由路径，特点是前端访问，#后面的变化不会经过服务器

**history模式**：正常的/访问模式，特点是后端访问，任意地址的变化都会访问服务器

###### Hash模式

- 原理
  基于浏览器的**hashchange事件**，地址变化时，通过window.location.hash 获取地址上的hash值；并通过构造Router类，配置routes对象设置hash值与对应的组件内容。
- 优点

1. hash值会出现在URL中, 但是不会被包含在Http请求中, 因此hash值改变不会重新加载页面
2. hash改变会触发hashchange事件, 能控制浏览器的前进后退
3. 兼容性好

- 缺点

1. **地址栏中携带#，不美观**
2. 只可修改 # 后面的部分，因此只能设置与当前 URL 同文档的 URL
3. hash有体积限制，故只可添加短字符串
4. 设置的新值必须与原来不一样才会触发hashchange事件，并将记录添加到栈中
5. **每次URL的改变不属于一次http请求，所以不利于SEO优化**

###### History模式

- 原理
  基于HTML5新增的pushState()和replaceState()两个api，以及浏览器的popstate事件，地址变化时，通过window.location.pathname找到对应的组件。并通过构造Router类，配置routes对象设置pathname值与对应的组件内容。
- 优点

1. **没有#，更加美观**
2. pushState() 设置的新 URL 可以是与当前 URL 同源的任意 URL
3. pushState() 设置的新 URL 可以与当前 URL 一模一样，这样也会把记录添加到栈中
4. pushState() 通过 stateObject 参数可以添加任意类型的数据到记录中
5. pushState() 可额外设置 title 属性供后续使用
6. 浏览器的进后退能触发浏览器的popstate事件，获取window.location.pathname来控制页面的变化

- 缺点

1. URL的改变属于http请求，借助history.pushState实现页面的无刷新跳转，因此**会重新请求服务器**。所以前端的 URL 必须和实际向后端发起请求的 URL 一致。如果用户输入的URL回车或者浏览器刷新或者分享出去某个页面路径，用户点击后，URL与后端配置的页面请求URL不一致，则匹配不到任何静态资源，就会返回404页面。所以需要后台配置支持，覆盖所有情况的候选资源，如果 URL 匹配不到任何静态资源，则应该返回app 依赖的页面或者应用首页。
2. 兼容性差，特定浏览器支持

###### Abstract模式

工作时没用过，支持所有javascript运行模式。vue-router 自身会对环境做校验，如果发现没有浏览器的 API，路由会自动强制进入 abstract 模式。在移动端原生环境中也是使用 abstract 模式。

###### 总结

hash 模式和 history 模式都属于浏览器自身的特性，Vue-Router 只是利用了这两个特性（通过调用浏览器提供的接口）来实现前端路由。

#### 33、说说你对slot的理解？slot使用场景有哪些？

https://vue3js.cn/interview/vue/slot.html#%E4%B8%80%E3%80%81slot%E6%98%AF%E4%BB%80%E4%B9%88

###### **什么是slot**

> slot，是组件的一块HTML模板，这块模板显示不显示、以及怎样显示由父组件来决定

###### slot的作用

> **通过插槽可以让用户可以拓展组件，去更好地复用组件和对其做定制化处理**
>
> 如果父组件在使用到一个复用组件的时候，获取这个组件在不同的地方有少量的更改，如果去重写组件是一件不明智的事情
>
> 通过`slot`插槽向组件内部指定位置传递内容，完成这个复用组件在不同场景的应用
>
> **比如布局组件、表格列、下拉选、弹框显示内容等**

###### slot的分类

> - 默认插槽
>
>   - 子组件用`<slot>`标签来确定渲染的位置，标签里面可以放`DOM`结构，当父组件使用的时候没有往插槽传入内容，标签内`DOM`结构就会显示在页面
>
>     父组件在使用的时候，直接在子组件的标签内写入内容即可
>
> - 具名插槽
>
>   - 子组件用`name`属性来表示插槽的名字，不传为默认插槽
>
>     父组件中在使用时在默认插槽的基础上加上`slot`属性，值为子组件插槽`name`属性值
>
> - 作用域插槽
>
>   - 子组件在作用域上绑定属性来将子组件的信息传给父组件使用，这些属性会被挂在父组件`v-slot`接受的对象上
>
>     父组件中在使用时通过`v-slot:`（简写：#）获取子组件的信息，在内容中使用

#### 34、使用过 Vue SSR 吗？说说 SSR？

服务器端渲染

#### 35、说说你对vue的mixin的理解，有什么应用场景？

























