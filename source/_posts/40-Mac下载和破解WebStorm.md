---
title: Mac下载和破解WebStorm
tags:
  - 工具
cover: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20230319212226.png
top_img: >-
  https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/20230319211955.png
abbrlink: 6134
date: 2023-03-19 21:12:47
---



# 下载和破解 WebStorm

WebStorm 是一款非常优秀的前端开发工具，但它是收费软件。在本文中，我们将介绍如何下载 WebStorm 并破解它，以便您可以免费使用它。

## 手动激活

> https://blog.idejihuo.com/jetbrains/idea-2023-1-permanent-activation-tutorial-manual-activation.html

### 下载文件

> 地址：https://fileio.lanzoue.com/iRPnH0rf3htc
>
> 下载完记得解压

### 修改参数文件

> 文件 在WebStorm > 帮助（help） > 编辑自定义虚拟机选项（Edit Custom VM Options…）
>
> <img src="https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/image-20230725171605064.png" alt="image-20230725171605064" style="zoom: 33%;" />
>
> 修改为
>
> ```
> -Xms128m
> -Xmx1024m
> -XX:ReservedCodeCacheSize=512m
> -XX:+IgnoreUnrecognizedVMOptions
> -XX:+UseG1GC
> -XX:SoftRefLRUPolicyMSPerMB=50
> -XX:CICompilerCount=2
> -XX:+HeapDumpOnOutOfMemoryError
> -XX:-OmitStackTraceInFastThrow
> -ea
> -Dsun.io.useCanonCaches=false
> -Djdk.http.auth.tunneling.disabledSchemes=""
> -Djdk.attach.allowAttachSelf=true
> -Djdk.module.illegalAccess.silent=true
> -Dkotlinx.coroutines.debug=off
> -XX:ErrorFile=$USER_HOME/java_error_in_idea_%p.log
> -XX:HeapDumpPath=$USER_HOME/java_error_in_idea.hprof
> 
> --add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED
> --add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED
> 
> #下面javaagent路径 换成你的
> -javaagent:/Users/luxi/Documents/jihuo-tool-2022.2.3/jetbra/ja-netfilter.jar=jetbrains
> ```
>
> **提示：**javaagent的路径一定要正确，否则找不到补丁文件，IDE 可能无法正常启动

### 重启WebStorm

### 输入激活码

> + 从这个网站找一个最快的网站进去https://3.jetbra.in/
>
> + 复制WebStorm的激活码
>
> + 粘贴激活码（位置在顶部导航 > 帮助 > 注册…）
>
> <img src="https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/image-20230725172049052.png" alt="image-20230725172049052" style="zoom:50%;" />
>
> + 激活成功
>
> <img src="https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/image-20230725172037696.png" style="zoom:50%;" />



## 自动激活

> 注意：2023-07-25 运行 `sudo bash install.sh`回报错，此方法可能已经失效

### 下载 WebStorm

首先，您需要从 JetBrains 官网上下载 WebStorm。请访问以下链接以获取下载页面：https://www.jetbrains.com/zh-cn/webstorm/

下载完毕后，您可以安装 WebStorm。在安装完成后，您可以忽略它，因为我们需要下载补丁并执行破解。

### 下载补丁并破解 WebStorm

我们可以通过以下步骤来下载补丁并破解 WebStorm：

1. 下载补丁：请访问以下链接以下载补丁：https://www.123pan.com/s/EHHDVv-mImMv
2. 解压补丁：下载完毕后，您需要解压补丁文件。
3. 执行破解脚本：接下来，您需要打开终端并进入 `/jetbra/scripts` 文件夹。在该文件夹中，您需要执行 `install.sh` 脚本。您可以使用以下命令执行脚本：

```shell
sudo bash install.sh
```

执行脚本时，可能会出现一个错误，错误信息可能如下所示：

```shell
sed: RE error: illegal byte sequence
```

如果出现此错误，请在执行上面的命令之前执行以下命令：

```shell
export LC_COLLATE='C'
export LC_CTYPE='C'
```

执行成功后，您将看到 "done" 提示。此时，您需要重新启动您的计算机。

1. 激活 WebStorm：当您的计算机重新启动后，打开 WebStorm。接下来，您需要从以下链接中获取激活码：https://ipfs.io/ipfs/bafybeiatyghkzrrtodzt3stm652rkrjxndg4hq2ublfdmifk7plg5k5brq/

在该页面上，找到 WebStorm 并点击它。这将自动复制激活码。接下来，您需要将激活码粘贴到 WebStorm 中的 "Activation code" 栏中。最后，点击 "Activate" 按钮以激活 WebStorm。



## 注意事项

请注意，您破解后的 WebStorm 是不受支持的软件版本。如果您有任何问题，JetBrains 不会为您提供支持。此外，如果您的 WebStorm 重复启动以获取激活状态，它可能会停止工作。如果您遇到此问题，请卸载并重新安装 WebStorm。

此外，如果您删除了解压后的补丁包，您的 WebStorm 破解将无效。因此，请不要删除补丁文件夹！！！
