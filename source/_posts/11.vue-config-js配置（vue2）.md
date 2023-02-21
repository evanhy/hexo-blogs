---
title: vue.config.js配置（vue2）
abbrlink: 58727
cover:
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/%E5%90%BE%E7%8E%8B%E7%BE%8E%E5%A6%82%E7%94%BB.webp?x-oss-process=style/huyu
description: config.js
date: 2022-11-23 23:06:08
tags:
---
# vue.config.js文件

这个文件导出了一个包含了选项的对象：

```js
module.exports = {
    // 选项...
}
```

接下来，详细介绍一些选项及配置：

### 基本配置

```js
module.exports = {
    productionSourceMap: false,
    publicPath: './',
    outputDir: 'dist',
    assetsDir: 'assets',
    devServer: {
        port: 8090,
        host: '0.0.0.0',
        https: false,
        open: true
    },
    // 其他配置
    ...
}
```

`productionSourceMap`：生产环境是否要生成 `sourceMap`

`publicPath`：部署应用包时的基本 URL,用法和 `webpack` 本身的 `output.publicPath` 一致

可以通过三元运算去配置`dev`和`prod`环境,

> `publicPath: process.env.NODE_ENV === 'production' ? '/prod/' : './'`

`outputDir`: `build` 时输出的文件目录

`assetsDir`: 放置静态文件夹目录

`devServer`: dev环境下，`webpack-dev-server` 相关配置

- port: 开发运行时的端口
- host: 开发运行时域名，设置成`'0.0.0.0'`,在同一个局域网下，如果你的项目在运行，同时可以通过你的http://ip:port/...访问你的项目
- https: 是否启用 `https`
- open: `npm run serve` 时是否直接打开浏览器

### 插件及规则的配置

在`vue.config.js`如果要新增/修改 `webpack` 的 `plugins` 或者 `rules` , 有2种方式。

`configureWebpack` 方式

`configureWebpack` 是相对比较简单的一种方式

- 它可以是一个对象：和 `webpack` 本身配置方式是一致，该对象将会被 `webpack-merge` 合并入最终的 `webpack` 配置
- 它也可以是一个函数：直接在函数内部进行修改配置

```js
configureWebpack: {
    rules:[],
    plugins: []
}
configureWebpack: (config) => {
    // 例如，通过判断运行环境，设置mode
    config.mode = 'production'
} 
```

`chainWebpack` 方式

`chainWebpack` 链式操作 (高级)，接下来所有的配置我都会在该选项中进行配置

### 规则rules的配置

关于`rules` 的配置，我会分别从新增/修改进行介绍。

#### rules的新增

在`webpack`中 `rules` 是 `module` 的配置项，而所有的配置的都是挂载到 `config` 下的，所以新增一个rule方式：

```js
config.module
  .rule(name)
    .use(name)
      .loader(loader)
      .options(options)
```

案例：`style-resources-loader` 来添加`less`全局变量

案例：`svg-sprite-loader` 将svg图片以雪碧图的方式在项目中加载

```js
module.exports = {
    chainWebpack: (config) => {
        // 通过 style-resources-loader 来添加less全局变量
        const types = ['vue-modules', 'vue', 'normal-modules', 'normal'];
        types.forEach(type => {
            let rule = config.module.rule('less').oneOf(type)
            rule.use('style-resource')
                .loader('style-resources-loader')
                .options({
                    patterns: [path.resolve(__dirname, './lessVariates.less')]
                });
        });
        // `svg-sprite-loader`: 将svg图片以雪碧图的方式在项目中加载
        config.module
            .rule('svg')
            .test(/.svg$/) // 匹配svg文件
            .include.add(resolve('src/svg')) // 主要匹配src/svg
            .end() 
            .use('svg-sprite-loader')
            .loader('svg-sprite-loader') // 使用的loader，主要要npm该插件
            .options({symbolId: 'svg-[name]'}) // 参数配置
    }
}
```



####  rules的修改

针对已经存在的 `rule` , 如果需要修改它的参数, 可以使用 `tap` 方法：

```js
config.module
  .rule(name)
    .use(name)
      .tap(options => newOptions)
```

案例：修改`url-loader`的参数

```js
module.exports = {
    chainWebpack: (config) => {
        // `url-loader`是webpack默认已经配置的，现在我们来修改它的参数
        config.module.rule('images')
            .use('url-loader')
            .tap(options => ({
                name: './assets/images/[name].[ext]',
                quality: 85,
                limit: 0,
                esModule: false,
            }))
    }
}
```

### 插件plugins 的配置

关于 `plugins` 的配置，我会分别从`新增/修改/删除`进行介绍。

#### plugins的新增

- 注意：这里WebpackPlugin不需要通过`new WebpackPlugin()`使用。

```js
config
  .plugin(name)
  .use(WebpackPlugin, args)
```

案例：新增`hot-hash-webpack-plugin`

```js
const HotHashWebpackPlugin = require('hot-hash-webpack-plugin');
module.exports = {
    chainWebpack: (config) => {
        // 新增一个`hot-hash-webpack-plugin`
        // 注意：这里use的时候不需要使用`new HotHashWebpackPlugin()`
        config.plugin('hotHash')
              .use(HotHashWebpackPlugin, [{ version: '1.0.0' }]);
    }
}
```



#### plugins的修改

同理, `plugin` 参数的修改也是通过 `tap` 去修改。

```js
config
    .plugin(name)
    .tap(args => newArgs)
```

案例：修改打包后`css`抽离后的`filename`及抽离所属目录

案例：删除`console`和`debugger`

```js
const HotHashWebpackPlugin = require('hot-hash-webpack-plugin');
module.exports = {
    chainWebpack: (config) => {
        // 修改打包时css抽离后的filename及抽离所属目录
        config.plugin('extract-css')
                .tap(args => [{
                    filename: 'css/[name].[contenthash:8].css',
                    chunkFilename: 'css/[name].[contenthash:8].css'
                }]);
        // 正式环境下，删除console和debugger
        config.optimization
                .minimize(true)
                .minimizer('terser')
                .tap(args => {
                    let { terserOptions } = args[0];
                    terserOptions.compress.drop_console = true;
                    terserOptions.compress.drop_debugger = true;
                    return args
                });
    }
}
```



#### plugins的删除

对于一些`webpack`默认的 `plugin` ，如果不需要可以进行删除

```js
config.plugins.delete(name)
```

案例：删除 `vue-cli3.X` 模块的自动分割抽离

```js
module.exports = {
    chainWebpack: (config) => {
        // vue-cli3.X 会自动进行模块分割抽离，如果不需要进行分割,可以手动删除
        config.optimization.delete('splitChunks'); 
    }
}
```



### 一些常见的配置



#### 修改enter文件

```js
config.entryPoints.clear(); // 清空默认入口
config.entry('test').add(getPath('./test/main.ts')); // 重新设置
```



#### DefinePlugin

定义全局全局变量，`DefinePlugin` 是 `webpack` 已经默认配置的，我们可以对参数进行修改

```js
config.plugin('define').tap(args => [{ 
    ...args, 
    "window.isDefine": JSON.stringify(true),
    }]);
```



#### 自定义filename 及 chunkFilename

自定义打包后js文件的路径及文件名字

```js
config.output.filename('./js/[name].[chunkhash:8].js');
config.output.chunkFilename('./js/[name].[chunkhash:8].js');
```



#### 修改html-webpack-plugin参数

`html-webpack-plugin` 是 `webpack` 已经默认配置的，默认的源模版文件是 `public/index.html` ;我们可以对其参数进行修改

```js
 config.plugin('html')
        .tap(options => [{
            template: '../../index.html' // 修改源模版文件
            title: 'test',
        }]);
```



#### 设置别名alias

`webpack`默认是将`src`的别名设置为`@`, 此外，我们可以进行添加

```js
config.resolve.alias
        .set('@', resolve('src')) 
        .set('api', resolve('src/apis'))
        .set('common', resolve('src/common'))
```



### 附上一份 vue.config.js 配置

```js
const path = require('path')
const HotHashWebpackPlugin = require('hot-hash-webpack-plugin')
const WebpackBar = require('webpackbar')
const resolve = dir => path.join(__dirname, '.', dir)
module.exports = {
    productionSourceMap: false,
    publicPath: './',
    outputDir: 'dist',
    assetsDir: 'assets',
    devServer: {
        port: 9999,
        host: '0.0.0.0',
        https: false,
        open: true,
    },
    chainWebpack: config => {
        const types = ['vue-modules', 'vue', 'normal-modules', 'normal']
        types.forEach(type => {
            let rule = config.module.rule('less').oneOf(type)
            rule.use('style-resource')
                .loader('style-resources-loader')
                .options({
                    patterns: [path.resolve(__dirname, './lessVariates.less')],
                })
        })
        config.resolve.alias
            .set('@', resolve('src'))
            .set('api', resolve('src/apis'))
            .set('common', resolve('src/common'))
        config.module
            .rule('images')
            .use('url-loader')
            .tap(options => ({
                name: './assets/images/[name].[ext]',
                quality: 85,
                limit: 0,
                esModule: false,
            }))
        config.module
            .rule('svg')
            .test(/.svg$/)
            .include.add(resolve('src/svg'))
            .end()
            .use('svg-sprite-loader')
            .loader('svg-sprite-loader')
        config.plugin('define').tap(args => [
            {
                ...args,
                'window.isDefine': JSON.stringify(true),
            },
        ])
        // 生产环境配置
        if (process.env.NODE_ENV === 'production') {
            config.output.filename('./js/[name].[chunkhash:8].js')
            config.output.chunkFilename('./js/[name].[chunkhash:8].js')
            config.plugin('extract-css').tap(args => [
                {
                    filename: 'css/[name].[contenthash:8].css',
                    chunkFilename: 'css/[name].[contenthash:8].css',
                },
            ])
            config.plugin('hotHash').use(HotHashWebpackPlugin, [{ version: '1.0.0' }])
            config.plugin('webpackBar').use(WebpackBar)
            config.optimization
                .minimize(true)
                .minimizer('terser')
                .tap(args => {
                    let { terserOptions } = args[0]
                    terserOptions.compress.drop_console = true
                    terserOptions.compress.drop_debugger = true
                    return args
                })
            config.optimization.splitChunks({
                cacheGroups: {
                    common: {
                        name: 'common',
                        chunks: 'all',
                        minSize: 1,
                        minChunks: 2,
                        priority: 1,
                    },
                    vendor: {
                        name: 'chunk-libs',
                        chunks: 'all',
                        test: /[\/]node_modules[\/]/,
                        priority: 10,
                    },
                },
            })
        }
    },
}
```



### 详细的配置

```js
/*
    根据Vue Cli配置的一些常用配置项, 具体参考(https://cli.vuejs.org/zh/config/#pages)
*/

module.exports = {
    productionSourceMap: false, // 生成环境是否开启SourceMap(source map的作用就是定位。source map定位的时浏览器控制台输出语句在项目文件的位置。)

    filenameHashing: false, // build之后生成的静态资源默认情况下加了hash值以控制静态资源的缓存，默认是true

    publicPath: './', // 部署应用包时的基本 URL(可以通过三元运算去配置dev和prod环境, publicPath: process.env.NODE_ENV === 'production' ? '/prod/' : './')

    indexPath: 'index.html', // 指定生成的 index.html 的输出路径 (相对于 outputDir)。也可以是一个绝对路径。

    outputDir: 'dist', // 打包输出文件目录

    assetsDir: 'assets', // 放置静态文件夹目录

    lintOnSave: true, // 是否在开发环境下通过 eslint-loader 在每次保存时 lint 代码。这个值会在 @vue/cli-plugin-eslint 被安装之后生效。

    runtimeCompiler: true, // 是否使用包含运行时编译器的 Vue 构建版本

    transpileDependencies: [], // Babel 显式转译列表

    crossorigin: '', // 设置生成的 HTML 中 <link rel="stylesheet"> 和 <script> 标签的 crossorigin 属性（注：仅影响构建时注入的标签）

    integrity: false, // 在生成的 HTML 中的 <link rel="stylesheet"> 和 <script> 标签上启用 Subresource Integrity (SRI)

    /*
        如果这个值是一个对象，则会通过 webpack-merge 合并到最终的配置中 
        如果你需要基于环境有条件地配置行为，或者想要直接修改配置，那就换成一个函数 (该函数会在环境变量被设置之后懒执行)。该方法的第一个参数会收到已经解析好的配置。在函数内，你可以直接修改配置，或者返回一个将会被合并的对象
    */
    configureWebpack: {},

    chainWebpack: () => {}, // 对内部的 webpack 配置（比如修改、增加Loader选项）(链式操作)

    pages: {
        // 构建多页面应用，页面的配置
        index: {
            entry: 'src/index/main.js', // page 的入口

            template: 'public/index.html', // 模板来源

            filename: 'index.html', // 在 dist/index.html 的输出

            title: 'Index Page', // 当使用 title 选项时， template 中的 title 标签需要是 <title><%= htmlWebpackPlugin.options.title %></title>

            chunks: ['chunk-vendors', 'chunk-common', 'index'], // 在这个页面中包含的块，默认情况下会包含 提取出来的通用 chunk 和 vendor chunk。
        },
        /*
            当使用只有入口的字符串格式时，
            模板会被推导为 `public/subpage.html`
            并且如果找不到的话，就回退到 `public/index.html`。
            输出文件名会被推导为 `subpage.html`。
        */
        subpage: 'src/subpage/main.js',
    },

    devServer: {
        // dev环境  NodeJs架设临时的服务器用于项目的运行与调试
        open: true, // 是否运行好直接在默认浏览器打开
        inline: true, // 用于设置代码保存时是否自动刷新页面。
        hot: true, // 用于设置代码保存时是否进行热更新（局部刷新，不刷新整个页面）。
        openPage: 'default/index', //指定deserver 编译完成后自动打开的页面 (注意：需要配合 open来使用。如果Vue router 为 hash mode。应在url前面补上/#。如：openPage: '/#/about')
        https: false, // 用于设置是否启用https
        port: 8024, // 指定要监听请求的端口号
        host: '0.0.0.0', // 用于指定devDerve使用的host
        compress: true, // 对devServer 所有服务启用 gzip 压缩
        headers: {
            // 在所有响应中添加首部内容
            'X-Custom-Foo': 'bar',
        },
        /* 当出现编译器错误或警告时，在浏览器中显示全屏覆盖层。默认禁用。两种写法*/
        // overlay: true, // 第一种
        overlay: {
            // 第二种
            warnings: true, // 警告
            errors: true, // 错误
        },
        proxy: {
            // proxy:{'/api':{}},代理器中设置/api,项目中请求路径为/api的替换为target
            '/api': {
                target: 'http://127.0.0.10:3000', // 代理的服务器，也就是api要访问的服务器
                ws: true, // 是否代理websocket,
                secure: false, // 是否使用HTTPS协议, 默认false
                changeOrigin: true, // 将主机头的来源更改为目标URL，也就是是否允许跨域
                pathRewrite: { '^/api': '' }, // 重写url的path部分
                /*
                    pathRewrite详解
                    现在这样两个 api:
                    (1): http://localhost:80/api/login
                    (2): /api/login
                    devServe.proxy 会匹配第二个，因为第二个以/api开头，而第一个以http开头。
                    再例如有这样的 api：/getUserInfo，其所在的服务器为http://192.168.0.169。那这个 api 的url 就应该是http://192.168.0.169/getUserInfo。
                    然后在 axios 配置的 route 是 /api/getUserinfo。
                    这样配置之后，devServer.proxy 就能把 url 改写成http://192.168.0.169/getUserInfo
                */
            },
        },
    },

    css: {
        extract: false, // 是否使用css分离插件 ExtractTextPlugin  开启extract后，组件样式以内部样式表的形式加载的, 打包的结果会多出一个 css 文件夹以及css 文件。
        sourceMap: true, // 开启 CSS source maps?

        // 因为配置了loaderOptions.css, 尽管requireModuleExtension的值为默认值，我们也需要指出
        requireModuleExtension: true, // 用于设定，是否只有 *.module.[ext] 结尾的文件才会被视作CSS Modules 模块。 默认情况下，只有文件名中含有.modules的CSS相关文件还能使用CSS Modules。
        loaderOptions: {
            //用于向 CSS 相关的 loader 传递选项（设置）。
            /* 
               注意：以下配置在 Vue CLI v4 与 v3 之间存在差异。
               Vue CLI v3 用户可参考 css-loader v1 文档
               https://github.com/webpack-contrib/css-loader/tree/v1.0.1 
            */
            css: {
                // 配置 CSS Modules 的class命名规则
                modules: {
                    localIdentName: '[local]_[hash:base64:8]',
                },
            },
            postcss: {
                // 这里的选项会传递给 postcss-loader
            },
            sass: {
                // @/ 是 src/ 的别名 所以这里假设你有 `src/variables.sass` 这个文件 (注意：在 sass-loader v7 中，这个选项名是 "data")
                prependData: `@import "~@/variables.sass"`,
            },
            /*
                默认情况下 `sass` 选项会同时对 `sass` 和 `scss` 语法同时生效, 因为 `scss` 语法在内部也是由 sass-loader 处理的;
                但是在配置 `data` 选项的时候, `scss` 语法会要求语句结尾必须有分号，`sass` 则要求必须没有分号 在这种情况下，我们可以使用 `scss` 选项，对 `scss` 语法进行单独配置
            */
            scss: {
                prependData: `@import "~@/variables.scss";`,
            },
            less: {
                // 给 less-loader 传递 Less.js 相关选项
                // http://lesscss.org/usage/#less-options-strict-units `Global Variables`
                // `primary` is global variables fields name
                globalVars: {
                    primary: '#fff',
                },
            },
        },
    },

    parallel: require('os').cpus().length > 1, // 是否为 Babel 或 TypeScript 使用 thread-loader

    pwa: {}, // 向 PWA 插件传递选项

    pluginOptions: {}, // 可以用来传递任何第三方插件选项
}
```

