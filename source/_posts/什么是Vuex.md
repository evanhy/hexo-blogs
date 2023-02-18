---
title: 什么是Vuex
abbrlink: 65033
date: 2022-03-01 21:08:42
tags:
---

#### 什么是vuex 

vue提供的一个状态管理模式，解决不同组件数据共享问题

#### vuex有几个属性

##### **state** 

state是放置所有公共状态的属性，

`$store.state.属性名` `...mapState(['属性名'])`

##### **mutations** 

state数据的修改只能通过mutations，并且mutations必须是同步更新，目的是形成`数据快照`

数据快照：一次mutation的执行，**立刻**得到一种视图状态，因为是立刻，所以必须是同步

`this.$store.commit('函数名', 10)` `...mapMutations(['函数名'])`

##### **actions** 

state是存放数据的，mutations是同步更新数据，actions则负责进行异步操作

`this.$store.dispatch('函数名',传参)`   `...mapActions(['函数名'])`

##### **getters** 

类似于计算属性，有时我们还需要从state中派生出一些状态，这些状态是**依赖state**的，此时会用到getters

`$store.getters.filterList` `...mapGetters(['filterList'])`

##### **modules**

> 模块化，由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿
>
> **命名空间** `namespaced`



#### 什么是mapState

+ .......
