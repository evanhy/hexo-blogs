---
title: js知识点总结
banner_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028195627.png?x-oss-process=style/huyu
index_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028195627.png?x-oss-process=style/huyu
top_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028195627.png?x-oss-process=style/huyu
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028195627.png?x-oss-process=style/huyu
tags:
  - js
categories:
  - 面试
abbrlink: 2198
date: 2022-02-09 17:09:35
---

#### 1、手动实现`防抖`和`节流`

> **防抖**：事件被触发 n 秒后再执行回调，如果在这 n 秒内事件又被触发，则重新计时,，防止在短时间内过于频繁的执行相同的任务。当短时间内的频繁是不必要的时候，就可以考虑去抖动，避免资源浪费，或造成不友好的体验。
>
> **节流**：规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，如果在同一个单位时间内某事件被触发多次，只有一次能生效
>
> **防抖函数的应用场景：**
>
> - 按钮提交场景：防⽌多次提交按钮，只执⾏最后提交的⼀次
> - 服务端验证场景：表单验证需要服务端配合，只执⾏⼀段连续的输⼊事件的最后⼀次，还有搜索联想词功能类似⽣存环境请⽤lodash.debounce
>
> **节流函数的适⽤场景：**
>
> - 拖拽场景：固定时间内只执⾏⼀次，防⽌超⾼频次触发位置变动
> - 缩放场景：监控浏览器resize
> - 动画场景：避免短时间内多次触发动画引起性能问题

```js
/**
 * debounce 防抖
 * @param fn [function] 需要防抖的函数
 * @param delay [number] 毫秒，防抖期限值
 */
function debounce(fn, delay) {
  let timer = null; //借助闭包
  return function (...arg) {
    if (timer) {
      //进入该分支语句，说明当前正在一个计时过程中，并且又触发了相同事件。
      // 所以要取消当前的计时，重新开始计时
      clearTimeout(timer);
    }
    timer = setTimeout(() => {
      fn.apply(this, arg); // 使用apply将fn函数的this指向修改为return后的function
    }, delay); // 进入该分支说明当前并没有在计时，那么就开始一个计时
  };
}
/**
 * debounce 节流
 * @param fn [function] 需要节流的函数
 * @param delay [number] 毫秒
 */
function throttle(fn, delay) {
  let valid = false; // 节流阀
  return function (...arg) {
    if (valid) {
      //休息时间 暂不接客
      return;
    }
    // 工作时间，执行函数并且在间隔期内把状态位设为无效
    valid = true;
    setTimeout(() => {
      fn.apply(this, arg);
      valid = false;
    }, delay);
  };
}
```



#### 2、let、const、var 的区别

> **1.是否存在变量提升？**
>
> - `var`声明的变量存在变量提升（将变量提升到当前[作用域](https://developer.mozilla.org/zh-CN/docs/Glossary/Scope)的顶部）。即变量可以在声明之前调用，值为`undefined`
>
> - `let`和`const`不存在变量提升。即它们所声明的变量一定要在声明后使用，否则报`ReferenceError`错
>
> **2.是否存在暂时性死区？**
>
> `let和const存在暂时性死区`。即只要块级作用域内存在 let 命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响
>
> **3.是否允许重复声明变量？**
>
> - `var`允许重复声明变量。
>
> - `let`和`const`在同一作用域不允许重复声明变量。
>
> **4.是否存在块级作用域？**
>
> - var 不存在块级作用域。
> - let 和 const 存在块级作用域
>   - 块作用域由`{ }`包括，`if`语句和`for`语句里面的`{ }`也属于块作用域
>
> **5. 是否能修改声明的变量？**
>
> - `var`和`let`可以。
> - `const`声明一个只读的**常量**。一旦声明，常量的值就不能改变。`const`声明的变量不得改变值，这意味着，`const`一旦声明变量，就必须立即初始化，不能留到以后赋值。



#### 3、箭头函数与普通函数区别

> 1.箭头函数是匿名函数，不能作为构造函数，不能使用 new
>
> 2.箭头函数内没有`arguments`，可以用展开运算符`...`解决
>
> 3.箭头函数的 this，始终指向父级上下文（箭头函数的`this`取决于定义位置`父级的上下文`，跟使用位置没关系，普通函数`this`指向调用的那个对象）
>
> 4.箭头函数不能通过`call() 、 apply() 、bind()`方法直接修改它的 this 指向。(`call、apply、bind`会默认忽略第一个参数，但是可以正常传参)
>
> 5.箭头函数没有原型属性



#### 4、Promise

- 状态

  - **进行中**：`pending`

  - **已成功**：`resolved`

  - **已失败**：`rejected`

- 特点

  - 对象的状态不受外界影响

  - 一旦状态改变就不会再变，任何时候都可得到这个结果

- 声明：`new Promise((resolve, reject) => {})`
- 出参

  - **resolve**：将状态从`未完成`变为`成功`，在异步操作成功时调用，并将异步操作的结果作为参数传递出去

  - **reject**：将状态从`未完成`变为`失败`，在异步操作失败时调用，并将异步操作的错误作为参数传递出去

**① 什么是 Promise？**

- Promise，简单说就是一个`容器`，包含异步操作结果的对象
- 从语法上说，**promise** 是一个`对象`，从它可以获取异步操作的的最终状态（成功或失败）。
- Promise 是一个`构造函数`，对外提供统一的 API，自己身上有 all、reject、resolve 等方法，原型上有 then、catch 等方法。

**② Promise 有什么用？**

解决回调地狱

ps:什么是回调地狱？回调地狱，其实简单来说就是异步回调函数的嵌套

**③Promise 有哪些方法**

- **then()：**分别指定`resolved状态`和`rejected状态`的回调函数

  - **第一参数**：状态变为`resolved`时调用

  - **第二参数**：状态变为`rejected`时调用(可选)

  - **链式调用 promise.then()**：**`then` 方法返回一个 Promise 对象，其允许方法链，从而创建一个 promise 链**

* **catch()**：指定发生错误时的回调函数

* **Promise.resolve()**：将对象转为 Promise 对象 等价于 `new Promise(resolve => resolve())`

  - **Promise 实例**：原封不动地返回入参

  - **thenable 对象**：

    `thenable`对象指的是具有`then`方法的对象

    `Promise.resolve`方法会将这个对象转为 Promise 对象，然后就立即执行`thenable`对象的`then`方法

  - **不具有 then()的对象**：将此对象转为 Promise 对象并返回，状态为`resolved`

  - **不带参数**：返回 Promise 对象，状态为`resolved`

* **Promise.reject()**：将对象转为状态为`rejected`的 Promise 对象(等价于`new Promise((resolve, reject) => reject())`)

* **Promise.all()：**

  并发，**发起多个并发请求**，将多个实例包装成一个新实例（数组形式），然后在所有 promise 都被解决后执行一些操作(齐变更再返回)

  - 成功：只有全部实例状态变成`fulfilled`（ 成功 ），最终状态才会变成`fulfilled`
  - 失败：其中一个实例状态变成`rejected`，最终状态就会变成`rejected`
  - 每一个 promise 成功的值，会按照传入的顺序返回数组内
  - 用 all 方法进行接口请求，就算其中有失败的情况，别的请求也会进行，但最后的状态还是`rejected`

* **Promise.race()：**

  赛跑机制

  将多个实例包装成一个新实例，返回全部实例状态优先变更后的结果(先变更先返回)

  - 成功失败：哪个实例率先改变状态就返回哪个实例的状态

- **Promise.finally()**：指定不管最后状态如何都会执行的回调函数
- **Promise.allSettled()**⭐：将多个实例包装成一个新实例，返回全部实例状态变更后的状态数组(齐变更再返回)
  - 成功：成员包含`status`和`value`，`status`为`fulfilled`，`value`为返回值
  - 失败：成员包含`status`和`value`，`status`为`rejected`，`value`为错误原因
- **Promise.any()**：将多个实例包装成一个新实例，返回全部实例状态变更后的结果数组(齐变更再返回)

* 成功：其中一个实例状态变成`fulfilled`，最终状态就会变成`fulfilled`

* 失败：只有全部实例状态变成`rejected`，最终状态才会变成`rejected`

- **Promise.try()**：不想区分是否同步异步函数，包装函数为实例，使用`then()`指定下一步流程，使用`catch()`捕获错误

**常见的错误**

- `Uncaught TypeError: undefined is not a promise`

  如果在控制台中收到 `Uncaught TypeError: undefined is not a promise` 错误，则请确保使用 `new Promise()` 而不是 `Promise()`

- **`UnhandledPromiseRejectionWarning`**

  这意味着调用的 promise 被拒绝，但是没有用于处理错误的 catch。 在 then 之后添加 catch 则可以正确地处理

> 扩展：[手写 Promise](https://juejin.cn/post/7043758954496655397#heading-2)



#### 5、数据类型

原始数据类型（**基本类型**）：按值访问，可以操作保存在变量中实际的值。

- **空值**（`null`）：用于未知的值 —— 只有一个 `null` 值的独立类型。
- **未定义**（`undefined`）: 用于未定义的值 —— 只有一个 `undefined` 值的独立类型。
- **布尔值**（`boolean`）：用于 `true` 和 `false`。
- **数字**（`number`）：用于任何类型的数字：整数或浮点数，在 `±(253-1)` 范围内的整数。
- **字符串**（`string`）：用于字符串：一个字符串可以包含 0 个或多个字符，所以没有单独的单字符类型。
- **符号**（`symbol`）:用于唯一的标识符。
- BigInt:一种数字类型,可以表示任意精度格式的整数

引用类型（**复杂数据类型**）：引用类型的值是保存在内存中的对象。

- 对象（**Object**）
  - 数组对象（Array）
  - 函数对象（Function）
  - 布尔对象（Boolean）
  - 数字对象（Number）
  - 字符串对象（String）
  - 日期对象（Date）
  - 正则对象（RegExp）
  - 错误对象（Error）

> ⚠️ **注意**： 与其他语言不同的是，JavaScript 不允许直接访问内存中的位置，也就是说不能直接操作对象的内存空间。在操作对象时，实际上是在操作对象的引用而不是实际的对象。所以引用类型的值是按引用访问的。



#### 6、检测数据类型的常用方法

> **typeof、instanceof、constructor、Object.prototype.toString.call()**

###### 1 .typeof

```js
console.log(
  typeof 100, //"number"
  typeof undefined, //"undefined"
  typeof null, //"object"
  typeof function () {
    console.log("aaa");
  }, //"function"
  typeof new Number(100), //'object'
  typeof new String("abc"), // 'string'
  typeof new Boolean(true) //'boolean'
);
```

> typeof 可以正常检测出：number、boolean、string、object、function、undefined、symbol、bigint
>
> - 检测基本数据类型，null 会检测 object，因为 null 是一个空的引用对象
> - 检测复杂数据类型，除 function 外，均为 object

###### **2 . instanceof**

`instanceof` 运算符需要指定一个构造函数，或者说指定一个特定的类型，用来判断这个构造函数的原型是否在给定对象的原型链上

> 基本数据类型中：Number，String，Boolean。字面量值不可以用 instanceof 检测，但是构造函数创建的值可以
>
> 注意：null 和 undefined 都返回了 false，这是因为它们的类型就是自己本身，并不是 Object 创建出来它们，所以返回了 false。

```js
console.log(
  100 instanceof Number, //false
  undefined instanceof Object, //false
  [1, 2, 3] instanceof Array, //true
  new Error() instanceof Error //true
);
```

###### **3 .constructor**

constructor 是 prototype 对象上的属性，指向构造函数。根据实例对象寻找属性的顺序，若实例对象上没有实例属性或方法时，就去原型链上寻找，因此，实例对象也是能使用 constructor 属性的。可以检测出字面量方式创建的对象类型

如果输出一个类型的实例的 constructor，就如下所示：

```js
console.log(new Number(123).constructor);
//ƒ Number() { [native code] }
```

可以看到它指向了 Number 的构造函数，因此，可以使用`num.constructor === Number`来判断一个变量是不是 Number 类型的

除了 undefined 和 null 之外，其他类型都可以通过 constructor 属性来判断类型。

```js
var num = 123;
var str = "abcdef";
var bool = true;
var arr = [1, 2, 3, 4];

// undefined和null没有constructor属性
console.log(
  num.constructor === Number,
  str.constructor === String,
  bool.constructor === Boolean,
  arr.constructor === Array
);
//所有结果均为true
```

###### **4 . 使用 Object.prototype.toString.call()检测对象类型**⭐

```js
const toString = Object.prototype.toString;

toString.call(123); //"[object Number]"
toString.call(undefined); //"[object Undefined]"
toString.call(null); //"[object Null]"
toString.call(/^[a-zA-Z]{5,20}$/); //"[object RegExp]"
toString.call(new Error()); //"[object Error]"
```

可以使用`Object.prototype.toString.call(obj).slice(8，-1)`来判断并截取

使用`Object.prototype.toString.call()`的方式来判断一个变量的类型是最准确的方法

###### **5 . 自己封装函数**

```js
function getType(obj) {
  const type = typeof obj;
  if (type !== "object") {
    return type;
  }
  //如果不是object类型的数据，直接用typeof就能判断出来
  //如果是object类型数据，准确判断类型必须使用Object.prototype.toString.call(obj)的方式才能判断
  return Object.prototype.toString.call(obj).replace(/^\[object (\S+)]$/, "$1");
}
```

###### **6、isArray**

isArray 可以检测出是否为数组

```js
const arr = [];
Array.isArray(arr); // true
```



#### 7、数组的常用方法有哪些

##### 一、操作方法

###### 增

下面前三种是对原数组产生影响的增添方法，第四种则不会对原数组产生影响

> push( ) unshift( ) splice( ) concat( )

**push()**

`push()`方法接收任意数量的参数，并将它们添加到数组末尾，返回数组的最新长度

**unshift()**

unshift()在数组开头添加任意多个值，然后返回新的数组长度

**splice**

传入三个参数，分别是开始位置、0（要删除的元素数量）、插入的元素，返回的是空数组

**concat()**

合并

首先会创建一个当前数组的副本，然后再把它的参数添加到副本末尾，最后返回这个新构建的数组，不会影响原始数组

`let colors = ["red"].concat("yellow", ["black"]);// ["red", "yellow", "black"]`

###### 删

下面三种都会影响原数组，最后一项不影响原数组：

> pop() shift() splice() slice()

**pop()**

`pop()` 方法用于删除数组的最后一项，同时减少数组的`length` 值，返回被删除的项

**shift()**

`shift()`方法用于删除数组的第一项，同时减少数组的`length` 值，返回被删除的项

**splice()**

传入两个参数，分别是开始位置，删除元素的数量，返回包含删除元素的数组

**slice()**

slice(开始索引, 结束索引) ，返回一个新数组，不会影响原始数组

###### 改

即修改原来数组的内容，常用`splice`

**splice()**

传入三个参数，分别是（开始位置，要删除元素的数量，要插入的任意多个元素)，返回删除元素的数组

会改变原数组

###### 查

查找元素，返回元素坐标或者元素值

> indexOf( )  includes( )  find( )

**indexOf()**

返回要查找的元素在数组中的位置，如果没找到则返回 -1

**includes()**

返回要查找的元素在数组中的位置，找到返回`true`，否则`false`

**find()**

返回第一个匹配的元素

##### 二、排序方法

> reverse()sort()

###### reverse()

翻转

###### sort()

排序

```js
function sortArr(a, b) {
  return a - b; // 升序
  return b - a; // 降序
}
```

##### 三、转换方法

###### join()

join() 方法接收一个参数，即字符串分隔符，返回包含所有项的字符串，转为字符串

##### 四、迭代方法

常用来迭代数组的方法（除 forEach 外其他都不会对空数组进⾏检测、不会改变原始数组）有如下：

> some() every() forEach() filter() map()

###### some()

对数组每一项都运行传入的测试函数，如果至少有 1 个元素返回 true ，则这个方法返回 true

###### every()

对数组每一项都运行传入的测试函数，如果所有元素都返回 true ，则这个方法返回 true

###### forEach()

对数组每一项都运行传入的函数，没有返回值

###### filter()

对数组每一项都运行传入的函数，函数返回 `true` 的项会组成数组之后返回

###### map()

映射 对数组每一项都运行传入的函数，返回由每次函数调用的结果构成的数组



#### 8、深浅拷贝

> **基本类型**数据保存在在**栈**内存中
>
> **引用类型**数据保存在**堆**内存中，引用数据类型的变量是一个指向堆内存中实际对象的引用，存在栈中

![](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/eq4kTpIjEyhCWwu.png?x-oss-process=style/huyu)

##### 深拷贝和浅拷贝的区别

**1.浅拷贝**： 将原对象或原数组的引用直接赋给新对象，新数组，新对象/数组只是原对象的一个引用。

如果属性是基本类型，拷贝的就是基本类型的值。如果属性是引用类型，拷贝的就是内存地址，即浅拷贝是拷贝一层，深层次的引用类型则共享内存地址

**2.深拷贝**： 创建一个新的对象和数组，将原对象的各项属性的“值”（数组的所有元素）拷贝过来，是“值”而不是“引用”

##### 浅拷贝

**数组浅拷贝:**

```js
// 直接遍历
function shallowCopy(arr) {
  const newArr = [];
  arr.forEach((item) => newArr.push(item));
  return newArr;
}
// slice
const arr2 = [1, 2, 3, 4, 5];
const newArr2 = arr2.slice();
//    concat() 合并空数组实现
const arr3 = [11, 22, 33, 44, 55];
const newArr3 = arr3.concat([]);
```

**对象浅拷贝:**

```js
// 直接遍历
function shallowCopy(obj) {
  const newObj = {};
  for (let item in obj) {
    newObj[item] = obj[item];
  }
  return newObj;
}

// 使用拓展运算符
const obj2 = { name: "Bob", age: 17 };
const newObj2 = { ...obj2 };
```

##### 深拷贝

> 用深拷贝最后要递归到全部是基本值，不然可能会陷入死循环/循环引用，导致栈溢出
>
> TODO(待理解)⭐： 处理过的数据使用 map 结构缓存起来 >> 递归的时候碰到相同的数据 >> 直接使用缓存里面的

**1. 先转换成字符串，在转换成(数组/对象) JSON.parse(JSON.stringify(XXXX))**

有一个缺点 里面的函数不能拷贝

```js
const array = [{ number: 1 }, { number: 2 }, { number: 3 }];
const str = JSON.stringify(array);
const copyArray = JSON.parse(str);
```

**2、递归实现简单的深拷贝：**

```js
function deepClone(obj = {}) {
  if (typeof obj !== "object" || obj == null) {
    // obj 是 null ，或者不是对象和数组，直接返回
    return obj;
  }
  // 初始化返回结果
  let result;
  obj instanceof Array ? (result = []) : (result = {});
  for (let key in obj) {
    // 保证 key 不是原型的属性
    if (obj.hasOwnProperty(key)) {
      // 递归调用！！！
      result[key] = deepClone(obj[key]);
    }
  }
  // 返回结果
  return result;
}
```

**3、解构赋值法实现一层拷贝**

**4、Object.assign() 实现一层深拷贝**

**5、 lodash第三方库**

**小结**

前提为拷贝类型为引用类型的情况下：

- 浅拷贝是拷贝一层，属性为对象时，浅拷贝是复制，两个对象指向同一个地址
- 深拷贝是递归拷贝深层次，属性为对象时，深拷贝是新开栈，两个对象指向不同的地址



#### 9、闭包

**什么是闭包**

> 通俗地讲闭包就是在一个函数里边再定义一个函数，这个内部函数一直保持有对外部函数中作用域的访问权限(小房间一直可以有大房子的访问权限)

**闭包的作用**

> 1. 访问其他函数内部变量
> 2. 保护变量不被 JS 的垃圾回收机制回收
> 3. 避免全局变量被污染 方便调用上下文的局部变量 加强封装性

**闭包的优点**

> （一）变量长期驻扎在内存中
> （二）另一个就是可以重复使用变量，并且不会造成变量污染
> ① 全局变量可以重复使用，但是容易造成变量污染。不同的地方定义了相同的全局变量,这样就会产生混乱。
> ② 局部变量仅在局部作用域内有效，不可以重复使用，不会造成变量污染。
> ③ 闭包结合了全局变量和局部变量的优点。可以重复使用变量，并且不会造成变量污染

**闭包的缺点**

> 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在 IE 中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。



#### 10、数组去重

https://segmentfault.com/a/1190000016418021

###### Set 去重

```js
const arr = [1, 2, 3, 3, 3, 2, 3, 4, 5, 4, 4];
const newArr = [...new Set(arr)]; // [ 1, 2, 3, 4, 5 ]
```

###### indexOf 去重

```js
function unique(arr) {
  let newArr = [];
  for (let i = 0; i < arr.length; i++) {
    if (newArr.indexOf(arr[i]) === -1) newArr.push(arr[i]);
  }
  return newArr;
}
```

###### splice 去重

```js
function unique(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] === arr[j]) {
        arr.splice(j, 1);
        j--;
      }
    }
  }
  return arr;
}
```

###### includes 去重

```js
function unique(arr) {
  const newArr = [];
  for (let i = 0; i < arr.length; i++) {
    if (!newArr.includes(arr[i])) {
      newArr.push(arr[i]);
    }
  }
  return newArr;
}
```

###### filter 去重

```js
function unique(arr) {
  return arr.filter(function (item, index, arr) {
    //当前元素，在原始数组中的第一个索引===当前索引值，否则返回当前元素
    return arr.indexOf(item) === index;
  });
}
```

###### findIndex数组对象去重

```js
function unique(arr) {
  // todo:待总结数组去重方法
  // 方法一
  return arr.filter((item, index) => {
    return arr.findIndex((child) => child.id === item.id) === index;
  });
}
```

###### Map数组对象去重

```js
function unique(arr) {
  const res = new Map();
  return arr.filter((item) => !res.has(item.id) && res.set(item.id, 1));
}
```

###### reduce 数组对象去重

```js
function unique(arr) {
  let obj = {};
  return arr.reduce((pre, item) => {
    obj[item.id] ? "" : (obj[item.id] = true && pre.push(item));
    return pre;
  }, []);
}
```

###### lodash 库数组和数组对象去重

```js
import { isEqual, uniqWith, uniqBy } from "lodash";
let arr = [
  { id: 1, name: "sli", year: 2012 },
  { id: 2, name: "ap", year: 2015 },
  { id: 1, name: "alslion", year: 2012 },
  { id: 3, name: "pose", year: 2012 },
  { id: 3, name: "pose", year: 2012 },
];
// 根据id去掉相同的元素:
console.log(uniqBy(arr, "id"));
// 深检查数组每一项进行去重:
console.log(uniqWith(arr, isEqual));
```



#### 11、逻辑运算符 && 和 ||

**||运算符: 条件 1 || 条件 2**

- 若条件 1 为 true、返回条件 1
- 若条件 1 为 false、不管 || 后面是 true 还是 false、都是返回||后面的值、即则返回条件 2;

```javascript
console.log(0 || ""); // ''
console.log("" || 0); // 0
```

**&&运算符: 条件 1 && 条件 2**

- 若条件 1 为 false、就返回返回条件 1 的值;
- 若条件 1 为 true, 无论条件 2 为 true 或者 false, 都将返回条件 2 的值;

```javascript
console.log(0 && ""); // 0
console.log("" && 0); // ''
```



#### 12、new 的过程

分析一下 new 的整个过程：

- 1、创建一个空对象
- 2、this 指向 obj 空对象，并调用构造函数
- 3、继承构造函数的原型
- 4、返回对象

简单实现一下 new：

```js
function myNew(fn, ...args) {
  // 第一步：创建一个空对象
  const obj = {};
  // 第二步：this指向obj 空对象，并调用构造函数
  fn.apply(obj, args);
  // 第三步：继承构造函数的原型
  obj.__proto__ = fn.prototype;
  // 第四步：返回对象
  return obj;
}
```



#### 13、事件循环 Event Loop  （结合着14条讲）

**是什么？**

> 首先，`JavaScript`是一门单线程的语言，意味着同一时间内只能做一件事，如果前面一个任务耗时太长，后续的任务不得不等待，可能会导致程序假死的问题，但是这并不意味着单线程就是阻塞，而实现单线程非阻塞的方法就是事件循环

在`JavaScript`中，所有的任务都可以分为

- 同步任务：立即执行的任务，同步任务一般会直接进入到主线程中执行
- 异步任务：异步执行的任务，比如`ajax`网络请求，`setTimeout`定时函数等

同步任务进入主线程，即主执行栈，异步任务进入任务队列，主线程内的任务执行完毕为空，会去任务队列读取对应的任务，推入主线程执行。过程不断重复就是**事件循环**



#### 14、async 和 await 宏任务 和 微任务

https://www.mianshiya.com/qd/bf4a0bf261c7e2500090d9482499675f

#####  宏任务 和 微任务

下面代码执行顺序是什么

```js
console.log(1)  // 同步

setTimeout(()=>{  // 宏任务
    console.log(2)
}, 0)

new Promise((resolve, reject)=>{
    console.log('new Promise') // 同步
    resolve()
}).then(()=>{
    console.log('then')  // 微任务
})

console.log(3) // 同步
```

`1`=>`'new Promise'`=> `3` => `'then'` => `2`

###### 1、什么是宏任务和微任务

Javascript 把异步任务又做了进一步的划分，异步任务又分为两类，分别是：

**微任务：**

一个需要异步执行的函数，执行时机是在主函数执行结束之后、当前宏任务结束之前

常见的微任务有：

- Promise.then
- MutaionObserver
- Object.observe（已废弃；Proxy 对象替代）
- process.nextTick（Node.js）

**宏任务：**

宏任务的时间粒度比较大，执行的时间间隔是不能精确控制的，对一些高实时性的需求就不太符合

常见的宏任务有：

- script (可以理解为外层同步代码)
- setTimeout/setInterval
- UI rendering/UI事件
- postMessage、MessageChannel
- setImmediate、I/O（Node.js）

> **promise里面的代码是同步任务 promise的方法.then()等是异步任务 微任务**

###### 2、宏任务和微任务的执行顺序

<img src="https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/YvSB58sUMZg7l31.png?x-oss-process=style/huyu" >

每一个宏任务执行完之后，都会检查是否存在待执行的微任务，如果有，则执行完所有微任务之后，再继续执行下一个宏任务。

> **1、微任务比宏任务的执行时间要早**
>
> **2、微任务在DOM渲染之前执行，宏任务在DOM渲染之后执行**

##### async与await

> `async` 是异步的意思，`await`是等待。所以可以理解`async`就是用来声明一个异步方法，而 `await`是用来等待异步方法执行

###### async

`async`函数返回一个`promise`对象

###### await

正常情况下，`await`命令后面是一个 `Promise`对象，返回该对象的结果。如果不是 `Promise`对象，就直接返回对应的值

不管`await`后面跟着的是什么，`await`都会阻塞后面的代码（加入微任务列表）

下面代码执行顺序是什么：

```js
async function async1() {
    console.log('async1 start')
    await async2()
    console.log('async1 end')
}
async function async2() {
    console.log('async2')
}
console.log('script start')
setTimeout(function () {
    console.log('settimeout')
})
async1()
new Promise(function (resolve) {
    console.log('promise1')
    resolve()
}).then(function () {
    console.log('promise2')
})
console.log('script end')
```

分析过程：

1. 执行整段代码，遇到 `console.log('script start')` 直接打印结果，输出 `script start`
2. 遇到定时器了，它是宏任务，先放着不执行
3. 遇到 `async1()`，执行 `async1` 函数，先打印 `async1 start`，下面遇到`await`怎么办？先执行 `async2`，打印 `async2`，然后阻塞下面代码（即加入微任务列表），跳出去执行同步代码
4. 跳到 `new Promise` 这里，直接执行，打印 `promise1`，下面遇到 `.then()`，它是微任务，放到微任务列表等待执行
5. 最后一行直接打印 `script end`，现在同步代码执行完了，开始执行微任务，即 `await`下面的代码，打印 `async1 end`
6. 继续执行下一个微任务，即执行 `then` 的回调，打印 `promise2`
7. 上一个宏任务所有事都做完了，开始下一个宏任务，就是定时器，打印 `settimeout`

所以最后的结果是：`script start`、`async1 start`、`async2`、`promise1`、`script end`、`async1 end`、`promise2`、`settimeout`



#### 15、**call、apply、bind 的区别**

- 都可以改变 `this` 指向
- call 和 apply 会`立即执行`，bind 不会，而是返回一个函数
- call 和 bind 可以接收`多个参数`，`apply` 只能接受两个，第二个是`数组`
- bind 参数可以分多次传入



#### 16、继承

> **首先，继承的是 属性 和 原型方法**

##### Class 继承

ES6：https://es6.ruanyifeng.com/#docs/class-extends

> Class 可以通过`extends`关键字实现继承，让子类继承父类的属性和方法
>
> 子类如果写 `constructor()`就必须要写 `super()`,且要写在最前面，否则报错，只有`super()`方法才能让子类实例继承父类。
>
> :facepunch:ps: ES6 规定，子类必须在`constructor()`方法中调用`super()`，否则就会报错。这是因为子类自己的`this`对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，添加子类自己的实例属性和方法。如果不调用`super()`方法，子类就得不到自己的`this`对象。

```js
class Parent {
  constructor(x,y) {
    this.x = x
		this.y = y
		...
  }
	toString() {
    ...
  }
}

class Son extends Parent {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }
  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```

##### ES5 继承

> js 中有很多中继承的方式，不过每一种继承方式都有优缺点，重点掌握 ES5 继承，别的继承方式基本都是 ES5 继承的语法糖

> 先创造子类实例，通过`Parent.call(this, arg1, arg2...)`将父类的属性方法添加到`this`上，继承了父类的属性
>
> 再通过 `Son.prototype = Object.create( Father.prototype )`将父类的原型继承过来
>
> 最后可以通过`Son.prototype.constructor = Son` 将子类的原型指到子类身上

```js
function Father(name) {
  this.name = name;
}

Father.prototype.get = function () {
  return "黑马";
};

// 继承的是 属性 和 原型方法
function Son(name) {
  Father.call(this, name);
}

// Son.prototype = new Father() // 相互影响 会存在一个 {name:undefined}

// Object.create 创造出一个空对象
// 让当前对象的__proto__ 指向传入的对象
Son.prototype = Object.create(Father.prototype);
Son.prototype.constructor = Son;

const son = new Son("程序员");
console.log(son);
console.log(son.get() + son.name); // 黑马程序员
```



#### 17、原型链

**什么是原型原型链？**

> 每个函数都有一个 prototype 原型(原型就是对象)，原型对象有一个 constructor 属性，指向的是构造函数
>
> 访问对象的某一个属性或者方法时，会从对象自身查找，如果查找不到，就会去原型链上去找，原型的最终目的就是让所有的实例能够共享其属性和方法
>
> 查找顺序:
>
> 自身 >`__proto__`> 构造函数的原型对象 >`__proto__`> Object 的原型对象 >`__proto__`> null

![](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/zlOJPgIK3y7YkWh.png?x-oss-process=style/huyu)



#### 18、堆和栈 TODO ⭐⭐

https://juejin.cn/post/6844903618999500808



#### 19、ES6 Set 和 Map TODO ⭐⭐



#### 20、**includes**

> `includes()` 方法用来判断一个数组是否包含一个指定的值，根据情况，如果包含则返回 true，否则返回 false

语法

```js
arr.includes(valueToFind[, fromIndex])
```

- `valueToFind`

  需要查找的元素值。**Note:** 使用 `includes()`比较字符串和字符时是区分大小写。

- `fromIndex` 可选

  从`fromIndex` 索引处开始查找 `valueToFind`。如果为负值，则按升序从 `array.length + fromIndex` 的索引开始搜 （即使从末尾开始往前跳 `fromIndex` 的绝对值个索引，然后往后搜寻）。默认为 0。

```js
[1, 2, 3].includes(2); // true
[1, 2, 3].includes(4); // false
[1, 2, 3].includes(3, 3); // false
[1, 2, 3].includes(3, -1); // true
[1, 2, NaN].includes(NaN); // true
```



#### 21、**find**

> `find() `方法返回数组中满足提供的测试函数的第一个元素的值。否则返回 `undefined`

```js
arr.find(callback[, thisArg])
```

- `callback`

  在数组每一项上执行的函数，接收 3 个参数：`element`当前遍历到的元素。`index`可选当前遍历到的索引。`array`可选数组本身

- `thisArg`可选

  执行回调时用作 `this` 的对象

#### find findindex indexOf  includes filter

#### 22、hasOwnProperty

https://juejin.cn/post/6966053301615853582

> `hasOwnProperty`是Object.prototype的一个方法
> **他能判断一个对象是否包含自定义属性而不是原型链上的属性**
> `hasOwnProperty` 是 JavaScript 中唯一一个处理属性但是不查找原型链的函数

<img src="https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/image-20220805205922244.png?x-oss-process=style/huyu" alt="image-20220805205922244" style="zoom:150%;" />



#### 23、JS如何判断两个对象是否相等

```js
const a = {
	name: 'zs',
	age: 18,
	job: {
		sex: '男'
	}
}
const b = {
	name: 'zs',
	age: 18,
	job: {
		sex: '男'
	}
}
```

###### JSON.stringify（）转字符串

```js
console.log(JSON.stringify(a) === JSON.stringify(b))  // true
```

**缺陷**：哪怕两个对象的内部数据相等，但只要每个数据对应的位置不同，其结果也为false

###### 函数检测

```js
const isEqual = (a, b) => {
 const aKeysList = Object.keys(a)
 const bKeysList = Object.keys(b)
 if (aKeysList.length !== bKeysList.length) {
  return false
 }
 for (const aKey in a) {
  if (a[aKey] !== b[aKey]) {
   return false
  }
 }
 return true
}
console.log(isEqual(a, b))   // true
```

**缺陷**：若对象当中嵌有引用类型数据，则此方法则不适用，需要进行改进

###### 函数检测的基础上递归

```js
const isEqual = (a, b) => {
	const aKeysList = Object.keys(a)
	const bKeysList = Object.keys(b)
	if (aKeysList.length !== bKeysList.length) {
		return false
	}
	for (const aKey in a) {
		if (typeof a[aKey] === 'object' || typeof b[aKey] === 'object') {
			if (!isEqual(a[aKey], b[aKey])) {
				return false
			}
		} else if (a[aKey] !== b[aKey]) {
			return false
		}
	}
	return true
}
console.log(isEqual(a, b))  // true
```



#### 24、for···in和for···of的区别

https://blog.csdn.net/weixin_43638968/article/details/109291957

**首先一句话：(for···in取key，for··of取value)**
①从遍历[数组](https://so.csdn.net/so/search?q=数组&spm=1001.2101.3001.7020)角度来说，for···in遍历出来的是key(即下标)，for···of遍历出来的是value(即数组的值)；

②从遍历[字符串](https://so.csdn.net/so/search?q=字符串&spm=1001.2101.3001.7020)的角度来说，同数组一样。

③从遍历对象的角度来说，for···in会遍历出来的为对象的key，但for···of会直接报错。

④如果要使用for…of遍历普通对象，需要配合Object.keys()一起使用。



#### 25、创建对象的几种方式

https://juejin.cn/post/6844904126233444360

###### 字面量

```js
const obj = {
	name: '张三',
}
```

###### new Object

```js
const obj2 = new Object()
```

###### 构造函数

```js
function Person(name, age) {
	this.name = name
	this.age = age
	sang() {
		console.log('唱歌')
	}
}
const p = new Person('张三', 22)
```

###### ES6 class

```js
class Person {
	constructor(name, age) { // constructor构造函数
		this.name = name
		this.age = age
	}
	sayname() { //原型上的
		console.log(this.name)
	}
}
const per = new Person('dz', 23)
```



#### 26、变量提升与函数提升

###### 变量提升

将变量声明提升到它所在作用域的最开始的部分。

- **通过var定义（声明）的变量，在定义语句之前就可以访问到**
- **值：undefined；**

```javascript
	console.log(a); //undefined
	var a = 1;
```

因为有变量提升的缘故，上面代码实际的执行顺序为：

```javascript
	var a;
	console.log(a);
	a = 1;
```

###### 函数提升

> js中创建函数有两种方式：**函数声明式**和**函数表达式**

**1、函数声明提升**

**js在执行之前，会把foo函数提升到最前面**，所以我们在fun函数定义之前就可以使用fun函数。

```javascript
	fun();
	function fun(){
		console.log("aa");
	}
```

打印结果为aa；说明以函数声明来定义函数时，可以在定义函数之前访问到定义的函数。

**2、函数表达式提升**

```javascript
	var fun = function() {
	    console.log('函数表达式');
	};
```

此种声明方式我们可以理解为**一个普通变量的提升**，在js代码执行之前会把fun提升带最前面，**在函数赋值之前，fun是undefined，如果调用fun(),将会报错**。

```javascript
	fun();
	var fun = function (){
	    console.log("aa");
	}
```

此时打印的结果为报错`Uncaught TypeError: fun is not a function`，因为在js代码执行之前，会把fun提升到最前面，值为undefined，不是一个函数，以函数的形式来进行调用时将会报错。



#### 27、暂时性死区

> 暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，**只有等到声明变量的那一行代码出现，才可以获取和使用该变量**

```js
	function fn() {
    console.log(a);   // ReferenceError: Cannot access 'a' before
		let/const a = 78
  }
	fn()
```



#### 28、ES6新增了哪些语法

> - class
> - 箭头函数
> - 解构赋值
> - 字符串模板
> - async/await
> - 引入 module 模块
> - generators(生成器)
> - Map和Set



#### 29、假如有10万条数据需要处理，前端应该怎么处理？

https://juejin.cn/post/6844904184689475592

https://juejin.cn/post/7101206944534233125#heading-29

思路：

1. **采用懒加载+分页(前端维护懒加载的数据分发和分页)**
2. **使用虚拟滚动技术(目前react的antd4.0已支持虚拟滚动的select长列表)**



