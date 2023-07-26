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



# `for…in` 和 `for…of`有什么区别

## for…in



### 1.遍历数组

```js
const arr = ["element1", "element2", "element3"]

for (const item in arr) {
  console.log("🚀 ~ for in", arr[item])
}
// 输出 element1 element2 element3
```



### 2.遍历对象

```js
const obj = {
  name: "obj",
  age: 18,
}
for (const item in obj) {
  console.log("🚀 ~ for in", item, obj[item])
}
// 输出：
//  name obj
//  age 18
```

> 检测是否可枚举
>
> ```js
> console.log("🚀 ~ ", Object.getOwnPropertyDescriptor(obj, "name"))
> // 输出：
> //  { value: 'obj', writable: true, enumerable: true, configurable: true }
> //  enumerable: true 可枚举
> ```



### 3.遍历字符串

```js
const str = "hello!world"
for (const item in str) {
  console.log("🚀 ~ for in", str[item])
}
// 输出：
//  h e l l o ! w o r l d
```



### 4.遍历字符串，包含unicode字符

```js
const str2 = "a𠮷c"

for (const item in str2) {
  console.log("🚀 ~ for in", str2[item])
}
// 输出：
//  a � � c
```



### 5.遍历对象，父类属性也会遍历出来

```js
const fatter = {
  fatherAttr: 1
}

const instance = Object.create(fatter)

instance.a = 1;
instance.b = 2;
instance.c = 3;

for (const attr in instance) {
  console.log(attr, instance[attr])
}

// 输出：
//  a 1
//  b 2
//  c 3
//  fatherAttr 1
// 将原型链上的属性也遍历出来了

// 通过 hasOwnProperty 判断是否是自身属性
for (const attr in instance) {
  if (instance.hasOwnProperty(attr)) {
   console.log(attr, instance[attr])
  }
}
```



## for…of

**示例：**

```js
let str = 'a𠮷c';
for (const item of str) {
  console.log(item)
}
// 输出：
//  a 𠮷 c
```

**遍历Map**

```js
const map = new Map([["a", 1], ["b", 2], ["c", 3]]);
for (const mapElement of map) {
  console.log(mapElement)
}
// 输出：
//  [ 'a', 1 ] [ 'b', 2 ] [ 'c', 3 ]

// 可解构
for (const [key, value] of map) {
  console.log(key, value)
}
// 输出：
//  a 1  b 2  c 3
```



**`Set`和`Array`**

+ `entries()、keys()、values()` 都返回一个迭代器对象
+ `entries()` 返回值为集合的键值对 `[[key, value], [key, value], [key, value]]`（对于数组，key 就是索引值；对于 Set，key 就是 value）
+ `keys()` 返回值为集合的键名 `[key, key, key]`（对于数组，key 就是索引值；对于 Set，key 就是 value）
+ `values()` 返回值为集合的值 `[value, value, value]`

```js
const arr2 = ["a", "b", "c"];
let set = new Set(arr2);

// 1.entries
for (const entry of set.entries()) {
  console.log(entry, "entries")
}
// 输出：
//  [ 'a', 'a' ] [ 'b', 'b' ] [ 'c', 'c' ]

for (const arElement of arr2.entries()) {
  console.log(arElement, "arElement")
}
// 输出：
//  [ 0, 'a' ] [ 1, 'b' ] [ 2, 'c' ]

// 2.keys
for (const key of set.keys()) {
  console.log(key, "keys")
}
// 输出：
//  a b c
for (const key of arr2.keys()) {
  console.log(key, "keys")
}
// 输出：
//  0 1 2

// 3.values
for (const value of set.values()) {
  console.log(value, "values")
}
// 输出：
//  a b c
for (const value of arr2.values()) {
  console.log(value, "values")
}
// 输出：
//  a b c
```

## for...in 和 for...of 的区别

|   for…in   |   for…of   |      |
| :--------: | :--------: | :--: |
|    key     |   value    | 结果 |
| 可枚举属性 | 可迭代对象 | 遍历 |
|  遍历对象  |  遍历数组  | 常用 |

## 拓展操作

```js
const father = {
  fatherAttr: 1
}

const instance = Object.create(father)

instance.a = 1;
instance.b = 2;
instance.c = 3;

Object.defineProperty(instance, "d", {
  value: 4,
  enumerable: false,  // 不可枚举
})
```

###  1.Object.Keys 获取键名数组

```js
for (const key of Object.keys(instance)) {
  console.log(key, "Object.keys")
}
// 输出：
//  a b c
```

> `Object.keys` 不会遍历原型链上的属性、以及自身不可枚举的属性
>
> `Object.keys` 返回的是一个数组，所以可以使用数组的方法

+ ps: `Object.keys` 与 `for in` 的区别

  ```js
  for (const instanceKey in instance) {
    console.log(instanceKey, "for in")
  }
  // 输出：
  //  a b c fatherAttr
  ```

  > `for in` 会遍历原型链上的属性，但是同样不会遍历自身不可枚举的属性

### 2.Object.getOwnPropertyNames() 获取键名数组

> Object.getOwnPropertyNames() 与 Object.keys() 的区别在于，Object.getOwnPropertyNames() 会遍历自身不可枚举的属性

```js
for (const instanceKey of Object.getOwnPropertyNames(instance)) {
  console.log(instanceKey, "Object.getOwnPropertyNames")
}
// 输出：
//  a b c d
```

###  3.Object.entries() 获取键值对数组

> `Object.entries()` 不会遍历原型链上的属性和自身不可枚举的属性

```js
for (const instanceKey of Object.entries(instance)) {
  console.log(instanceKey, "Object.entries")
}
// 输出：
//  [ 'a', 1 ] [ 'b', 2 ] [ 'c', 3 ]
```

+  ps: 当使用对象初始化一个Map实例，可以使用`Object.entries()`来初始化

   ```js
   const obj = {a: 1, b: 2, c: 3};
   const map = new Map(Object.entries(obj));
   console.log(map.get("a")) // 1
   ```

### 4.Object.values() 获取对象的属性值数组

> `Object.values()` 不会遍历原型链上的属性和自身不可枚举的属性

```js
for (const instanceKey of Object.values(instance)) {
  console.log(instanceKey, "Object.values")
}
// 输出：
//  1 2 3
```

### 5.Object.getOwnPropertySymbols()获取Symbol属性名

> 上面的方法都无法获取Symbol属性名，只能通过`Object.getOwnPropertySymbols()`来获取

```js
instance[Symbol("e")] = 5;
for (const instanceKey of Object.getOwnPropertySymbols(instance)) {
  console.log(instanceKey, "Object.getOwnPropertySymbols")
}
// 输出：
//  Symbol(e)
```

{% referfrom [1],'JavaScript骚操作之遍历、枚举与迭代（上篇）','https://juejin.cn/post/6844903724897271816' %}



## 什么是可枚举数据呢？

举个例子来说

```js
const obj = { x: 100 }
```

我们使用 `Object.getOwnPropertyDescriptors` 方法获取指定对象所有的自有属性的属性描述符。

<img src="https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/image-20230726133156693.png" alt="枚举" style="zoom:50%;" />

这时候发现他的每一个属性中 enumerable 都是 true，这时候他就是可枚举的。

> 如果一个属性的**enumerable为false**，下面三个操作不会取到该属性。
>
> for..in循环
>
> Object.keys方法
>
> JSON.stringify方法

所以一般情况下 对象、数组、字符串都是可枚举的

## 什么是**可迭代**数据呢？

**for...of**用于**可迭代**的数据，如：

- 数组
- 字符串
- Map
- Set

```js
const arr = [1, 2, 3]
```

数组中有个 `Symbol.iterator`的属性：

- 1.只要一个数据已经实现了Iterator接口, 那么这个数据就有一个叫做`[Symbol.iterator]`的属性
- 2.`[Symbol.iterator]`的属性会返回一个函数
- 3.`[Symbol.iterator]`返回的函数执行之后会返回一个对象
- 4.`[Symbol.iterator]`函数返回的对象中又一个名称叫做next的方法
- 5.next方法每次执行都会返回一个对象`{value: 1, done: false}`
- 6.这个对象中存储了当前取出的数据和是否取完了的标记

<img src="https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/image-20230726133554323.png" alt="迭代" style="zoom:50%;" />

就类似设计模式中的迭代器模式：

不断有next、next去调用：

- A next() => B
- B next() => C
- 如此 如此

了解了上面的情况之后，我们也可以理解什么时候用 for...of 什么时候用 for...in？

- **for...in**可以用在**可枚举**的数据
- **for...of**用于**可迭代**的数据



{% referfrom [2],'for...in 和 for...of有什么区别','https://juejin.cn/post/7104444342684614664' %}