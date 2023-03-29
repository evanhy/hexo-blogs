---
title: Pinia持久化插件
description: Pinia持久化
abbrlink: 50437
date: 2023-03-29 15:28:52
tags:
  - vue3
  - pinia
cover: >-
    https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20230329152949.png
top_img: >-
    https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20230329152949.png
---



## Pinia持久化

> `pinia-plugin-persist`插件实现`Pinia`的数据持久化

#### 食用方法

##### **安装**

```shell
# npm
npm install pinia-plugin-persist

# yarn
yarn add pinia-plugin-persist

# pnpm
pnpm add pinia-plugin-persist
```

**`store/index.ts`配置**

```ts
import { createPinia } from 'pinia'
import piniaPersist from 'pinia-plugin-persist'

const pinia = createPinia()
pinia.use(piniaPersist)
```

**`tsconfig.ts`添加配置**

```json
// tsconfig.ts
{
  "compilerOptions": {
    "types": [
      "pinia-plugin-persist"
    ]
  },
}
```

##### **Usage**

源码即文档

```typescript
export interface PersistStrategy {
    key?: string;
    storage?: Storage;
    paths?: string[];
}
export interface PersistOptions {
    enabled: true;
    strategies?: PersistStrategy[];
}
declare type Store = PiniaPluginContext['store'];
declare module 'pinia' {
    interface DefineStoreOptionsBase<S, Store> {
        persist?: PersistOptions;
    }
}
```

**1. 基本使用**

```javascript
import { defineStore } from "pinia"

export const useStore = defineStore("StoreId", {
    state: () => ({
        foo: "foo",
        bar: "bar"
    }),
    // 开启数据缓存
    persist: {
      	enabled: true,
        strategies: [
            {
                key: 'user',  //自定义 Key值
                storage: localStorage,  // 选择存储方式
                path: ['foo'], // 默认所有 state 都会进行缓存，通过 paths 指定要持久化的字段，其余的则不会进行持久化
            },
        ]
    }
})
```

开启 `enabled` 之后，默认会对整个 `Store` 的 `state` 内容进行 `sessionStorage` 储存。

**2. 进阶用法**

`strategies` 字段说明：

| 属性    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| key     | 自定义存储的 key，默认是 store.$id                           |
| storage | 可以指定任何 extends Storage 的实例，默认是 sessionStorage   |
| paths   | state 中的字段名，按组打包储存(默认所有 state 都会进行缓存，通过 paths 指定要持久化的字段，其余的则不会进行持久化) |



```javascript
import { defineStore } from "pinia"

export const useStore = defineStore("YourStore", () => {
    const foo = ref("foo")
    const bar = ref("bar")
    return { foo, bar }
}, {
    enabled: true,
    strategies: [{
        // 自定义存储的 key，默认是 store.$id
        key: "custom storageKey",
        // 可以指定任何 extends Storage 的实例，默认是 sessionStorage
        storage: localStorage,
        // state 中的字段名，按组打包储存
        paths: ["foo", "bar"]
    }]
})
```

`storage` 属性可以使用任何继承自 `Storage` 协议的对象，自定义存储对象也可以，如下 `cookiesStorage` 为例

```tsx
import Cookies from 'js-cookie'

const cookiesStorage: Storage = {
  setItem (key, state) {
    return Cookies.set('accessToken', state.accessToken, { expires: 3 })
  },
  getItem (key) {
    return JSON.stringify({
      accessToken: Cookies.getJSON('accessToken'),
    })
  },
}

export const useStore = defineStore("YourStore", () => {
    const foo = ref("foo")
    const bar = ref("bar")
    const accessToken = ref("xxx")
    return { foo, bar, accessToken }
}, {
    enabled: true,
    strategies: [{
        key: "token",
        storage: cookiesStorage,
        paths: ["accessToken"]
    }]
})
```

#### 源码解读

```ts
type Store = PiniaPluginContext['store'];
type PartialState = Partial<Store['$state']>;

export const updateStorage = (strategy: PersistStrategy, store: Store) => {

  // 默认使用 sessionStorage
  const storage = strategy.storage || sessionStorage
  
  // 默认存储 key 为 store.$id
  const storeKey = strategy.key || store.$id

  if (strategy.paths) {
  
    // 遍历 paths 将对应的属性收集到 finalObj 中
    const partialState = strategy.paths.reduce((finalObj, key) => {
      finalObj[key] = store.$state[key]
      return finalObj
    }, {} as PartialState)
    
    // 执行存储
    storage.setItem(storeKey, JSON.stringify(partialState))
  } else {
  
    // 如果没有 paths，则按整个 store.$state 存储
    storage.setItem(storeKey, JSON.stringify(store.$state))
  }
}


export default ({ options, store }: PiniaPluginContext): void => {

  // 判断插件功能是否开启
  if (options.persist?.enabled) {
  
    // 默认策略实例
    const defaultStrat: PersistStrategy[] = [{
      key: store.$id,
      storage: sessionStorage,
    }]

    const strategies = options.persist?.strategies?.length ? options.persist?.strategies : defaultStrat

    strategies.forEach((strategy) => {
      const storage = strategy.storage || sessionStorage
      const storeKey = strategy.key || store.$id
      const storageResult = storage.getItem(storeKey)

      if (storageResult) {
      
        // 如果 storage 中存在同步数据
        store.$patch(JSON.parse(storageResult))
        updateStorage(strategy, store)
      }
    })

    store.$subscribe(() => {
    
      // 监听 state 变化，同步更新 storage
      strategies.forEach((strategy) => {
        updateStorage(strategy, store)
      })
    })
  }
}
```
