---
title: glob文件匹配规则
description: glob文件匹配规则
cover: 'https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20231102175408.png'
top_img: 'https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20231102175408.png'
abbrlink: 9496
date: 2023-11-02 17:48:03
---





> Glob是一种通配符语法，用于在文件系统中匹配文件名或路径



## `*`

> 匹配文件中0个或者多个字符，但是不会匹配路径中的分隔符，除非路径分隔符出现在末尾

```txt
|-style
	|-file1.txt
	|-file2.css
	|-subdir
		|-file3
		|-...
```

`./style/*`

> 匹配`./style/file1.txt`、`./style/file2.css` 和 `./style/subdir/`，但不会匹配`./style/subdir` 目录下的任何内容。
>
> 要匹配`./style/` 目录下的所有文件和子目录，包括子目录中的内容，可以使用递归通配符`**`，如 `./style/**` 或 `./style/**/*`。

`./style/*.ts`

> 匹配`./style`目录下所有的 ts 文件

`/style/*.*`

> 匹配`/style/file1.txt`、`/style/file2.css` ，因为这些文件都有文件扩展名。模式不会匹配`/style/subdir/` 或 `/style/subdir/file3`，因为它们是目录而不是直接的文件，或者没有明确的文件扩展名。

 `./style/*/*.js`

```txt
./style/subdir1/file1.js
./style/subdir1/file2.js
./style/subdir2/file3.js
./style/demo/subdir1/file1.js
```

> 匹配`./style/subdir1/file1.js`、`./style/subdir1/file2.js` 和 `./style/subdir2/file3.js`，因为它们都是位于直接子目录中的`.js`文件。它不会匹配`./style/demo/subdir1/file1.js`，**只能匹配一级子目录**

## `**`

> 匹配路径中的0个或多个目录及其子目录,需要单独出现，即它左右不能有其他东西了。如果出现在末尾，也能匹配文件

`./style/**`

> 匹配`style`目录下的所有内容，无论有多少层次深度。
>
> 这个模式通常用于递归地查找目录中的所有文件或进行批量操作，因为它能够匹配目录树中的所有文件和目录。

` ./style/**/* `

> 匹配`./style/` 目录下的所有文件和子目录，包括子目录中的所有内容，以及更深层次的子目录中的内容。这是一个递归通配符，可以匹配整个目录树下的所有文件和目录。

`./style/**/*.js`

```txt
./style/file1.css
./style/file2.js
./style/subdir1/file3.css
./style/subdir1/file4.js
./style/subdir2/
```

> 匹配`./style/file1.js`、`./style/file2.js`、`./style/subdir1/file3.js`、`./style/subdir1/file4.js`，以及`./style/subdir2/` 目录下的任何`.js`文件，**无论有多少层次深度**。
>
> 这个模式通常用于查找项目中的所有JavaScript文件，包括嵌套在子目录中的文件。



`a/**b/z`

> 匹配满足以下条件的路径：
>
> 1. 以 `a/` 开头：路径必须以 `a/` 字符串开始。
> 2. 包含任意数量的子目录（`**`）：`**` 表示匹配零个或多个目录（包括子目录）。
> 3. 后面紧跟 `b`：路径中必须包含一个 `b` 字符。
> 4. 最后以 `/z` 结尾：路径必须以 `/z` 结尾。
>
> 因此，这个表达式可以匹配像 `a/b/z`、`a/x/y/b/z`、`a/b/c/d/e/b/z` 这样的路径，只要它们满足上述条件。这个通配符表达式的主要特点是匹配以 `a/` 开头，包含一个 `b`，然后以 `/z` 结尾的路径，中间可以包含任意数量的子目录。
>
> 不能匹配以下路径：
>
> 1. 路径不以 `a/` 开头：如果路径不以 `a/` 开头，那么就不满足匹配条件，不会匹配成功。
> 2. 路径不以 `/z` 结尾：如果路径不以 `/z` 结尾，也不满足匹配条件，不会匹配成功。
>
> 以下是一些不匹配的示例：
>
> - `b/z`：路径没有以 `a/` 开头。
> - `a/x/y/c/z`：虽然路径包含 `a/`，但中间的子目录中有一个 `c`，而不是一个 `b`。
> - `a/b/z/c`：虽然路径以 `a/` 开头且以 `/z` 结尾，但 `b` 不是路径中的直接字符，它必须紧跟在 `**` 之后。
>
> 总之，`a/**b/z` 只会匹配以 `a/` 开头，包含一个 `b`，然后以 `/z` 结尾的路径，中间可以包含任意数量的子目录，但要求 `b` 紧跟在 `**` 之后。如果不满足这些条件，表达式就不会匹配成功。

## `?`

>  匹配一个字符，不会匹配路径分隔符

`?.js`

> 匹配文件名或路径中以 `.js` 结尾的文件名，并且文件名的第一个字符是任意字符（单个字符）
>
> 例如： `a.js`、`1.js`、`x.js` 这样的文件名，但是，它不会匹配像 `abc.js`、`myjs.js` 这样的文件名



## `[...]`

> 由多个规则组成的数组，可以匹配数组中符合任意一个子项的文件，当子项中第一个字符为`!`或者`^`时，表示不匹配该规则

`./style/a[0-3].js`

> 匹配`style`目录下的`a0.js`, `a1.js`, `a2.js`, `a3.js`

`[xyz].js`

> 只能匹配 `x.js`,`y.js`,`z.js`,不会匹配`xy.js`,`xyz.js`等,整个中括号只代表一个字符

`[^xyz].js `

> 能匹配 a.js,b.js,c.js等,不能匹配x.js,y.js,z.js

<span style="color: red">注：使用数组的方式还有一个好处就是可以很方便的使用排除模式，在数组中的单个匹配模式前加上`!`即是排除模式，它会在匹配的结果中排除这个匹配，要注意一点的是不能在数组中的第一个元素中使用排除模式</span>

`[*.js,!b*.js]`

> 匹配所有js文件，但排除掉以b开头的js文件

`['!b*.js',*.js]`

> 不会排除任何文件，因为排除模式不能出现在数组的第一个元素中



## `{...}`

> 展开模式，根据里面的内容展开为多个规则，能匹配所有展开之后的规则将上面的例子扩展一下

`['./**/*.{html, php}', '!{build, simple, images, node_modules}/**']`

> 选择所有以 `.html` 或 `.php` 结尾的文件，但排除了名为 `build`、`simple`、`images` 和 `node_modules` 的目录及其内容。这通常用于选择要处理的文件集合，以便在构建流程中执行相应的操作。

## `!(pattern|pattern|pattern)`

> 这里指排除符合这几个模式的所有文件

`./style/!(test|login).js`

> 匹配排除文件名为`login.js`和`test.js`之后的所有js文件

`['./**/!(_)*.{html, php}', '!{build, node_modules}/**']`

> 选择所有以 `.html` 或 `.php` 结尾的文件，但排除了文件名以 `_` 开头的html与php文件，并且排除了名为 `build` 和 `node_modules` 的目录及其内容。这通常用于选择要处理的文件集合，以便在构建流程中执行相应的操作



## ?(pattern|pattern|pattern)

> 匹配括号中给定的任一模式0次或者1次，类似于js正则中的(pattern|pattern|pattern)?

 `./style/?(a|a2|b).js`

> 匹配以 `./style/` 开头，后跟 `a`、`a2` 或 `b`，最后以 `.js` 结尾的文件路径，例如：
>
> - `./style/a.js`
> - `./style/a2.js`
> - `./style/b.js`
>
> 其他不满足这个模式的路径将不会被匹配

## @(pattern|pattern|pattern)

> 匹配多个模式中的任一个，类似于js正则中的(pattern|pattern|pattern)

`./style/@(a|b|c).js`

> - `./style/`：路径必须以 `./style/` 开头。
> - `@(a|b|c)`：这个部分表示选择其中一个字符串，也就是匹配 `a`、`b` 或 `c` 中的任意一个。
> - `.js`：路径必须以 `.js` 结尾。
>
> 匹配`a`、`b` 或 `c` 中的任意一个字符，最后以 `.js` 结尾的文件路径，例如：
>
> - `./style/a.js`
> - `./style/b.js`
> - `./style/c.js`
>
> 这些路径会匹配成功。但是，如果路径中的字符不是 `a`、`b` 或 `c` 中的任意一个，或者不以 `.js` 结尾，那么就不会匹配成功。例如，`./style/d.js` 或 `./style/x.js` 将不会匹配成功。
>
> **不能组合，匹配一次，不能为空，注意与`?`的区别，如果是`?`则是零个或一个字符**



## +(pattern|pattern|pattern)

> 匹配括号中给定任一模式1次或者多次，这几个模式可以组合在一起匹配，类似于js正则中的(pattern|pattern|pattern)+

`./style/+(a|a2|b).js`

> 匹配以 `./style/` 开头，后跟一个或多个字符，并且可以是 `a`、`a2` 或 `b` 中的任意一个字符，最后以 `.js` 结尾的文件路径。示例：
>
> - `./style/ab.js`
> - `./style/aa2.js`
> - `./style/bbb.js`
>
> 这些路径都可以匹配成功，因为它们满足了一个或多个字符，且可以是 `a`、`a2` 或 `b` 中的任意一个字符，以及以 `.js` 结尾的条件。



## *(pattern|pattern|pattern)

> 匹配括号中给定任一模式0次或者多次，这几个模式可以组合在一起匹配，类似于js正则中的(pattern|pattern|pattern)*

`./style/*(a|b|c).js`

> 匹配以 `./style/` 开头，后跟零个或多个字符，并且可以是 `a`、`b` 或 `c` 中的任意一个字符，最后以 `.js` 结尾的文件路径。以下是一些匹配成功的示例：
>
> - `./style/a.js`
> - `./style/abc.js`
> - `./style/bbc.js`
> - `./style/ccc.js`
>
> 这些路径都可以匹配成功，因为它们满足了零个或多个字符，且可以是 `a`、`b` 或 `c` 中的任意一个字符，以及以 `.js` 结尾的条件。
