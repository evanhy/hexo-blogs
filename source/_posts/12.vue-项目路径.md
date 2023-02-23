---
title: vue 项目路径
abbrlink: 45836
date: 2022-11-23 23:21:13
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/wallhaven-576rd1_1920x1080.webp?x-oss-process=style/huyu
tags:
  - vue
---
## assets和static文件夹的区别

两个都是放置静态资源的地方，分别是`src/assets`文件夹和`static`文件夹

**assets目录中的文件会被webpack处理解析为模块依赖**，只支持相对路径形式。

**static/ 目录下的文件并不会被Webpack处理：它们会直接被复制到最终的打包目录（默认是dist/static）下。**必须使用绝对路径引用这些文件，这是通过在 config.js 文件中的 build.assetsPublicPath 和 build.assetsSubDirectory 连接来确定的。

任何放在 static/ 中文件需要以绝对路径的形式引用：/static/[filename]。

在我们实际的开发中，总的来说：**static放不会变动的文件 assets放可能会变动的文件。**

> 1、assets与static 引入不同
>
> 1. assets文件是src下的，所以最后会被打包，
> 2. static文件与src 同级，不会被打包
> 3. assets中的文件在vue下用../这种相对路径的形式进行引用，在script下必须用@import的方式引入
> 4. static下的文件在.vue中的任何地方都只要使用../这种相对路径的方式引入，
>
> 2、文件夹的区别
>
> （1）assets用来放置样式、静态图片，只要src下面的组件中用到的资源就放在assets中。
>
> （2）static用来放没有npm包的第三方插件，字体文件。
>
> （3）assets与components同级 components下的.vue引用静态文件时，相对路径为 ../assets/xxxx
>
> 3、vue如何引入其它静态文件：
>
> （1）src目录下的资源只能import或require。
>
> （2）想静态引入的话，建立一个与src同级的目录static，然后把静态资源放入该文件夹下，html的引入路径如下：./static/...（注：试过一定要放在static文件夹下，否则报错）

### 在js数据中如何引用图片

因为webpack会将图片当做模块来引用，所以在js中需要使用require将图片引用进来，不能直接以字符串的形式。

```html
// js部分
data() {
	return {
		imgUrl: '图片地址', // 错误写法
		imgUrl: 'require('图片地址')' // 正确的写法
	}
}

// template部分
img标签形式：
<img :src:"img" />
// 或者div背景图形式
<div :style="{ backgroud: 'url(' + img + ')' }"></div>
```



## Vue中如何配置自定义路径别名

在我们日常开发中，常常会导入一些模块或者组件，如果采用相对路径的方式：

```bash
import uEditor from "../../../../../components/tools";
```

会很臃肿

#### 创建vue.config.js,写入配置代码

```js
module.exports = {
    configureWebpack: {
        resolve: {
            alias: { //配置别名,修改后需要重新编译才能生效
                'assets': '@/assets',
                'common': '@/common',
                'components': '@/components',
                'network': '@/network',
                'views': '@/views',
                'static': path.resolve(__dirname, '../static'), // 也可以使用这种方法
            }
        }
    }
}
```

> 注意：如果在HTML路径中使用别名，需要在引用前面加上“~”前缀
>
> `<img src="~assets/img/..." />`
>
> 其他文件中使用
>
> ```html
> <style>
> 	@import "assets/css/..."
> </style>
> ```