---
title: Javascript 注释规范
banner_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028195802.png?x-oss-process=style/huyu
index_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028195802.png?x-oss-process=style/huyu
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028195802.png?x-oss-process=style/huyu
top_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028195802.png?x-oss-process=style/huyu
description: 注释也很重要哦
tags:
  - js
category:
  - 提升
abbrlink: 36878
date: 2022-10-23 22:10:07
---
### Javascript 代码注释规范

#### 一、语法
1. 注释的说明
语法：写在注释块第一行
```js
/**
 * events-function(这是注释的说明)
 * @description 切换音频播放状态（播放/停止）
 */
togglePlay: function() {
    // 省略其它代码...
}
```
2. 标签
语法：@tagName

```js
/** @function */
function fn() {

}
```

3. 标签的说明
语法：- 说明文字

```js
/**
 * @constructor Student - 学生
 * @param {string} name - 学生的名字
 */
 function Student(name) {

 }
 ```

4. 类型
语法：{typeName} (可与标签结合使用，如@param)

```js
/**
 * @param {string} a 必传参数
 */
function fn(a, b, c) {

}
```

5. 可选参数
语法：[paramName] (可与标签结合使用，如@param)

```js
/**
 *  @param {string} a 必传参数
 *  @param {number} [b] 可选参数
 */
function fn(a, b) {

}
```

6. 参数有默认值
语法：[paramName=value] (可与标签结合使用，如@param)

```js
/**
 *  @param {string} a 必传参数
 *  @param {number} [c=666] 参数有默认值
 */
function fn(a, c) {

}
```

7. 链接
语法：[link text]{@link namepathOrURL}

```js
/**
 * See {@link MyClass} and [MyClass's foo property]{@link MyClass#foo}.
 * Also, check out {@link http://www.google.com|Google} and
 * {@link https://github.com GitHub}.
 */
```

#### 二、示例

1. 函数

```js
/**
 * 转换时间字符串为时间对象
 * @function _str2time
 * @param strTime {String} - e.g "2017-02-13 10:02:58" or "2017-02-13" or "9:10"
 * @param type {String} - e.g date, dateTime, time
 */
function _str2time(strTime, type) {
    // 省略其它代码
}
```

2. 类/构造函数
```js
/**
 * 定时器
 * @class Timer
 */

function Timer() {
    this._timeId = 0;
    this._eventId = -1;

    this.eventHandler = {
        'stop': {}
    };

    /**
     * 定时器是否处于停止状态
     * @memberof Timer
     * @member stopped
     * @instance
     */
    this.stopped = true;

    /**
     * 启动定时器
     * @memberof Timer
     * @instance
     * @method start
     * @param {function} handler - 定时器每次执行时调用的函数
     * @param {number} interval - 定时器执行的时间间隔
     */
    this.start = function (handler, interval) {
        this.stopped = false;
        let _recursion = function() {
            this._timeId = setTimeout(() => {
                handler()
                    .then(() => {
                        if (this.stopped) {
                            clearTimeout(this._timeId);
                            this._trigger('stop');
                            return;
                        }
                        _recursion();
                    })
                    .catch(err => {
                        clearTimeout(this._timeId);
                        this.stopped = true;
                        this._trigger('stop');
                        if (err) throw new Error(err);
                    });
            }, interval)
        }.bind(this);
        _recursion();
    }

    /**
     * 停止定时器
     * @memberof Timer
     * @instance
     * @method stop
     */
    this.stop = function () {
        this.stopped = true;
    }
}

/**
 * 监听事件
 * @memberof Timer
 * @instance
 * @method on
 * @param {string} type - 事件类型  e.g 'stop'
 * @param {function} fn - 事件处理函数
 * @return {number} eventId - 事件处理函数Id，用于取消监听
 */
Timer.prototype.on = function (type, fn) {
    let _eventId = fn.name || ++this._eventId;
    this.eventHandler[type][_eventId] = fn;
    return _eventId;
}

/**
 * 触发事件
 * @private
 */
Timer.prototype._trigger = function (type) {
    let handlerMap = this.eventHandler[type];
    for (let key in handlerMap) {
        handlerMap[key]();
    }
}

/**
 * 取消监听事件
 * @memberof Timer
 * @instance
 * @method off
 * @param {string} type - 事件类型  e.g 'stop'
 * @param {function} target - 事件处理函数Id或者函数名字
 */
Timer.prototype.off = function (type, target) {
    let _target = (typeof target === 'function') ? target.name : target;
    delete this.eventHandler[type][_target];
}
```
