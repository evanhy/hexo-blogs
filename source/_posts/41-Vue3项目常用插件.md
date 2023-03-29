---
title: Vue3项目常用插件
description: Vue3项目常用插件
tags:
  - vue3
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/wallhaven-2yz3m9_1920x1080.webp
top_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/wallhaven-2yz3m9_1920x1080.webp
abbrlink: 21141
date: 2023-03-29 15:17:14
---



### Vue3项目插件

> 列举了一些 Vue项目中比较好用的插件

#### 1、unplugin-auto-import

> 支持`vue`, `vue-router`, `vue-i18n`, `@vueuse/head`, `@vueuse/core`等自动引入
>
> https://github.com/antfu/unplugin-auto-import
>
> https://juejin.cn/post/7162052830054842399

###### 一、效果

```tsx
// 引入前
import { ref, computed } from 'vue'
const count = ref(0)
const doubled = computed(() => count.value * 2)

//引入后
const count = ref(0)
const doubled = computed(() => count.value * 2)


// 引入前
import { useState } from 'react'
export function Counter() {
  const [count, setCount] = useState(0)
  return <div>{ count }</div>
}

//引入后
export function Counter() {
  const [count, setCount] = useState(0)
  return <div>{ count }</div>
}
```

###### 二、安装

```shell
npm i -D unplugin-auto-import
```

###### 三、vite.config.ts配置

```ts
// vite.config.ts
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/vite'

export default defineConfig({
  plugins: [
    AutoImport({
      imports: ['vue', 'vue-router', 'vue-i18n', '@vueuse/head', '@vueuse/core'],
      // auto-import.d.ts生成的位置(默认是在根目录) 会在
      dts: 'src/type/auto-import.d.ts',
    })
  ]
})
```

#### 2、unplugin-vue-components

> https://github.com/antfu/unplugin-vue-components

###### 一、安装

```shell
npm i unplugin-vue-components -D
```

###### 二、vite.config.ts配置

```ts
// vite.config.ts
import Components from 'unplugin-vue-components/vite'

export default defineConfig({
  plugins: [
    Components({ 
      /* options */ 
      // 自动引入文件的位置
      dts: 'src/type/components.d.ts',
    }),
  ],
})
```

###### 三、效果

It will automatically turn this

```vue
<template>
  <div>
    <HelloWorld msg="Hello Vue 3.0 + Vite" />
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>
```

into this

```vue
<template>
  <div>
    <HelloWorld msg="Hello Vue 3.0 + Vite" />
  </div>
</template>

<script>
import HelloWorld from './src/components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>
```

###### 四、TS

为了获得对自动导入组件的TypeScript支持，Vue 3有一个PR扩展了全局组件的接口。目前，Volar已经支持这种用法。如果您使用的是Volar，您可以如下更改配置以获得支持。

```tsx
Components({
  dts: true, // enabled by default if `typescript` is installed
  // dts: 'src/type/components.d.ts'
})
```

在`include`下将`components.d.ts`添加到`tsconfig.json`中

```json
{
  "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"],
}
```

###### 五、支持从UI库导入

<img src="https://raw.githubusercontent.com/pan52yu/blogImg/main/img/image-20230217150510906.png" alt="image-20230217150510906"  />

```ts
// vite.config.ts
import Components from 'unplugin-vue-components/vite'
import {
  AntDesignVueResolver,
  ElementPlusResolver,
  VantResolver,
} from 'unplugin-vue-components/resolvers'

// your plugin installation
Components({
  resolvers: [
    AntDesignVueResolver(),
    ElementPlusResolver(),
    VantResolver(),
  ],
})
```

> 可以去antfu大佬的插件链接查看更多的配置

#### 3、unplugin-vue-define-options

> Vue3 中写调试代码需要name
>
> https://vue-macros.sxzz.moe/macros/define-options.html

###### 一、安装

```shell
npm install unplugin-vue-define-options -D
```

###### 二、配置

```ts
//vite.config.ts
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import defineOptions from 'unplugin-vue-define-options/dist/vite';

export default defineConfig({
  plugins: [vue(), defineOptions()],
});
```

###### 三、TypeScript支持

```json
// tsconfig.json
{
  "compilerOptions": {
    // ...
    "types": ["unplugin-vue-define-options/macros-global" /* ... */]
  }
}
```

###### 四、使用

```vue
<script lang="ts" setup>
  defineOptions({
    name: 'YourName',
  });
</script>
```

#### 4、vite-plugin-vue-setup-extend

> vue3 中另外一种name定义方式，直接写在script标签上

https://github.com/vbenjs/vite-plugin-vue-setup-extend

一、安装

```shell
npm i vite-plugin-vue-setup-extend -D
```

二、配置 vite.config.ts中

```ts
import vueSetupExtend from 'vite-plugin-vue-setup-extend'

export default defineConfig({
  plugins: [vue(), vueSetupExtend()],
})
```

三、使用

```vue
<script lang="ts" setup name="App">
  // script 里面必须有东西 哪怕是一个注释都行
</script>
```

#### 5、pinia-plugin-persist

> Pinia持久化插件
>
> 详情看: [https://github.com/pan52yu/MyNote/blob/main/article/Vue3%E5%AD%A6%E4%B9%A0/Pinia.md](
