---
title: 配置ESLint和Prettier
sticky: 999
banner_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028202634.png?x-oss-process=style/huyu
index_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028202634.png?x-oss-process=style/huyu
top_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028202634.png?x-oss-process=style/huyu
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20221028202634.png?x-oss-process=style/huyu
description: ESLint 和 Prettier
tags:
  - js
categories:
  - 提升
abbrlink: 8990
date: 2022-09-16 14:38:37
---
#### 1、为什么要使用 ESLint 和 Prettier

> 代码规范是软件开发领域经久不衰的话题，随着前端应用的大型化和复杂化，越来越多的前端团队开始重视 `JavaScript` 代码规范。不以规矩，不能成方圆



#### 2、ESlint

> 如果项目目录下有多个配置文件，ESLint 只会使用一个，优先级为：`.eslintrc.js` > `.eslintrc` > `package.json`

###### > 安装 ESlint

```shell
# 删除原来的依赖
npm rm @vue/cli-plugin-eslint @vue/eslint-config-prettier babel-eslint eslint eslint-plugin-prettier eslint-plugin-vue prettier

# 安装 依赖
npm install @vue/cli-plugin-eslint@4.4.6 @vue/eslint-config-prettier@7.0.0 babel-eslint eslint@7.15.0 eslint-plugin-prettier@3.4.1 eslint-plugin-vue@7.2.0 prettier@2.5.1 --save-dev

# 如果安装报错
# --legacy-peer-deps：安装时忽略所有peerDependencies，忽视依赖冲突，采用npm版本4到版本6的样式去安装依赖，已有的依赖不会覆盖。建议用--legacy-peer-deps 比较保险一点
# --force 会无视冲突，并强制获取远端npm库资源，当有资源冲突时覆盖掉原先的版本。
npm install @vue/cli-plugin-eslint @vue/eslint-config-prettier babel-eslint eslint eslint-plugin-prettier eslint-plugin-vue prettier --save-dev --legacy-peer-deps
```



在 package.json 文件中添加或替换 lint 命令

```json
  "scripts": {
    "lint": "eslint --fix --ext .js,.vue src/"
  },
```

```shell
npm run lint # 需要进行一键修复的时候就执行命令
```



###### > .eslintrc.js配置

```js
module.exports = {
    root: true, // 当前项目使用这个配置文件, 不会往父级目录找.eslintrc.js文件
    env: {
        // 指定eslint启动环境(vuecli底层是node支持), browser: true也可以在浏览器设置
        node: true,
    },
    extends: [
        // 扩展配置
        "eslint:recommended", // 这个扩展包帮我们启用了一系列核心规则，这些规则是在 rules（https://cn.eslint.org/docs/rules/）页面 中被标记为 ✅ 的常见问题
        "plugin:vue/recommended", // vue模板格式化更严格
        "plugin:prettier/recommended", // 添加 prettier 插件 解决 eslint 和 prettier 的冲突
        "plugin:vue/essential", // vue里必须的规则
        "@vue/prettier",
    ],
    parserOptions: {
        // 扩展配置
        // 对新语法使用eslint
        parser: "babel-eslint",
    },
    //   "off" or 0 - 关闭规则
    //   "warn" or 1 - 将规则视为一个警告（不会影响退出码）,只警告，不会退出程序
    //   "error" or 2 - 将规则视为一个错误 (退出码为1)，报错并退出程序
    rules: {
        "no-console": process.env.NODE_ENV === "production" ? "warn" : "off", // 上线环境用打印就报警告, 开发环境关闭此规则
        "no-debugger": process.env.NODE_ENV === "production" ? "warn" : "off", // debugger可以终止代码执行
        "prettier/prettier": ["error", { endOfLine: "auto" }], // 解决 Delete `␍`eslint(prettier/prettier) 错误
    },
};

```



#### 3、Prettier

###### > 根目录创建并配置 `.prettierrc` 文件

```json
{
    "tabWidth": 4,
    "printWidth": 120,
    "useTabs": false,
    "semi": true,
    "singleQuote": false,
    "TrailingComma": "all",
    "bracketSpacing": true,
    "jsxBracketSameLine": false,
    "arrowParens": "avoid"
}

```

###### > 命令行格式化所有文件

```shell
npx prettier --write . #格式化所有文件
```



> **！！！ 配置完记得重启vscode**



#### 4、插件安装

###### Prettier 插件安装

> 安装插件 `Prettier - Code formatter`
>
> ![Prettier](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/1.webp?x-oss-process=style/huyu)

###### ESLint 插件安装

> 安装插件 `ESLint`
>
> ![ESLint](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/2.webp?x-oss-process=style/huyu)



+ 添加配置项

![vscode配置项](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/3.webp?x-oss-process=style/huyu)

```json
{
  // 将prettier设置为默认格式化程序(在编辑器中有可能被其他Formatter占用，所以将prettier设置为默认Formatter)
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  // 保存时自动格式化 (根据根目录下‘.prettierrc文件配置项’)
  "editor.formatOnSave": true,
  // Enable per-language
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.formatOnSave": true
  },
  // 为ESLint启用“保存时自动修复”，并且仍然具有格式和快速修复功能
  "editor.codeActionsOnSave": {
    "source.fixAll": true,
    "source.fixAll.eslint": true
  }
}
```





#### 5、配置项含义

###### .prettierrc文件常见配置

```json
{
    // tab缩进大小,默认为2
    "tabWidth": 4,
    // 行长规则 默认为120
    "printWidth": 120,
    // 使用tab缩进，默认false true 使用tab缩进 false 使用空格缩进
    "useTabs": false,
    // 使用分号, 默认true true 有分号 false 没有分号
    "semi": false,
    // 使用单引号, 默认false false 双引号 true 单引号 (在jsx中配置无效, 默认都是双引号)
    "singleQuote": false,
    // 行尾逗号,默认 none,可选 none|es5|all
    // es5 包括es5中的数组、对象
    // all 包括函数对象等所有可选
    "TrailingComma": "all",
    // 对象中的空格 默认true
    // true: { foo: bar } false: {foo: bar}
    "bracketSpacing": true,
    // JSX标签闭合位置 默认false
    // false: <div
    //          className=""
    //          style={{}}
    //       >
    // true: <div
    //          className=""
    //          style={{}} >
    "jsxBracketSameLine": false,
    // 箭头函数参数括号 默认avoid 可选 avoid| always
    // avoid 能省略括号的时候就省略 例如x => x
    // always 总是有括号
    "arrowParens": "avoid"
}
```









