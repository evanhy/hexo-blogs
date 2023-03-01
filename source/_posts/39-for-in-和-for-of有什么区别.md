---
title: for...in 和 for...of有什么区别
abbrlink: 22447
date: 2023-03-01 11:20:08
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/1150213.webp?x-oss-process=style/huyu
top_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/1150213.webp?x-oss-process=style/huyu
tags:
  - js
---

# for...in 和 for...of有什么区别

## 最明显的区别：

- for...in 遍历得到 key
- for...of 遍历得到 value

> 代码演示：

```javascript
const arr = [10, 20, 30]
for (let val in arr) {
		console.log('for...in', val)
}
console.log('====我是分隔符====')
for (let val of arr) {
		console.log('for...of', val)
}

```

> 演示结果：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4e9eb288960643709a740f331d543cc0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

## 二者适用于不同的数据类型

- 遍历对象：for...in可以，for...of 不可以
- 遍历Map Set: for..of 可以，for..in 不可以
- 遍历 generator：for...of 可以，for...in 不可以

真的是这样吗？我们就来亲身测试一下！

顺便带着思考一下这个问题：什么时候用 for...of 什么时候用 for...in呢？

### 遍历对象时：

> 演示：

```javascript
// 遍历对象
const obj = {
		name: '张三',
		city: 'beijing'
}
for (let val in obj) {
		console.log('for...in', val)
}
for (let val of obj) {
		console.log('for...of', val)
}

```

> 演示结果：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3b99a3b79bbd4186b5e66077f707014d~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

我们可以看见for..in 正常进行了遍历。

但是 for...of 出现了报错，提示我们 obj 不是一个可以遍历的数据

### 遍历 set 时

> 演示：

```vbnet
// 遍历 Set 
const set = new Set([10, 20, 30])
for(let n in set) {
  console.log('for...in', n)
}
for(let n of set) {
  console.log('for...of', n)
}
 
```

> 演示结果：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b4a0764752db416c89fc998fcb26b012~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

我们可以看见for..in 值是 undefined。

但是 for...of 正常进行了遍历。

### 遍历 map 时

> 演示：

```arduino
// 遍历 Map 
const map = new Map([
  ['x', 10],
  ['x1', 110],
  ['x2', 210],
])
for(let m in map) {
  console.log('for...in', m)
}
for(let m of map) {
  console.log('for...of', m)
}
```

> 演示结果：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/727e0bcf548c4576bfc113d59dc99cdd~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

我们可以看见for..in 值是 undefined。

但是 for...of 正常进行了遍历。

### 遍历 generator

#> 代码演示：

```javascript
// 遍历 generator
function* foo() {
		yield 10
		yield 20
		yield 30
}

for (let g in foo) {
		console.log('for...in', g)
}
for (let g of foo) {
		console.log('for...of', g)
}

```

> 演示结果：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aa203c1577c94e2085ab73631ea7bc84~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

我们可以看见for..in 值是 undefined。

但是 for...of 正常进行了遍历。

### 二者在什么情况下可以使用呢？

到底那些数据可以用 for...in 那些可以用 for...of ?

在提出这个问题的时候，我们需要先知道：**for...in** 和 **for...of** 可以用在哪？

#### **for...in**

可以用在**可枚举**的数据，如：

- 对象
- 数组
- 字符串

##### 什么是可枚举数据呢？

举个例子来说

```ini
const obj = { x: 100 }
 
```

我们使用 `Object.getOwnPropertyDescriptors` 方法获取指定对象所有的自有属性的属性描述符。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bab94bd2e4484f90b4eddc1047ab8aa5~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

这时候发现他的每一个属性中 enumerable 都是 true，这时候他就是可枚举的。

> 如果一个属性的**enumerable为false**，下面三个操作不会取到该属性。
>
> for..in循环
>
> Object.keys方法
>
> JSON.stringify方法

所以一般情况下 对象、数组、字符串都是可枚举的

#### **for...of**

**for...of**用于**可迭代**的数据，如：

- 数组
- 字符串
- Map
- Set

##### 什么是**可迭代**数据呢？

举个例子来说

```ini
const arr = [10, 20, 30]
 
```

数组中有个 `Symbol.iterator`的属性：

- 1.只要一个数据已经实现了Iterator接口, 那么这个数据就有一个叫做`[Symbol.iterator]`的属性
- 2.`[Symbol.iterator]`的属性会返回一个函数
- 3.`[Symbol.iterator]`返回的函数执行之后会返回一个对象
- 4.`[Symbol.iterator]`函数返回的对象中又一个名称叫做next的方法
- 5.next方法每次执行都会返回一个对象`{value: 10, done: false}`
- 6.这个对象中存储了当前取出的数据和是否取完了的标记

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/68120cc13a194f3bb18219322e56d7bb~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

就类似设计模式中的迭代器模式：

不断有next、next去调用：

- A next() => B
- B next() => C
- 如此 如此

了解了上面的情况之后，我们也可以理解什么时候用 for...of 什么时候用 for...in？

- **for...in**可以用在**可枚举**的数据
- **for...of**用于**可迭代**的数据

{% referfrom [1],'for...in 和 for...of有什么区别','https://juejin.cn/post/7104444342684614664' %}