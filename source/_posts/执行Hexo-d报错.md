---
title: 执行Hexo d报错
hide: true
tags:
  - hexo
abbrlink: 9836
date: 2021-08-11 21:46:31
---



> 执行`hexo g -d`构建时报错

```shell
$ hexo d
INFO  Deploying: git
INFO  Clearing .deploy_git folder...
INFO  Copying files from public folder...
INFO  Copying files from extend dirs...
warning: LF will be replaced by CRLF in 2021/03/18/index/index.html.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in CNAME.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in archives/2021/03/index.html.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in archives/2021/index.html.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in archives/index.html.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in css/index.css.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in css/user.css.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in gallery/index.html.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in home/index.html.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in index.html.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in js/main.js.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in research/index.html.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in software/index.html.
The file will have its original line endings in your working directory
On branch master
nothing to commit, working tree clean
fatal: unable to access  'https://github.com/Candlelight-XYJ/Candlelight-XYJ.github.io.git/': OpenSSL  SSL_read: Connection was reset, errno 10054
FATAL Something's wrong. Maybe you can find the solution here:  https://hexo.io/docs/troubleshooting.html
Error: Spawn failed
    at ChildProcess.<anonymous>  (D:\StudyMaterials\8_FileManagement\testBlog\blog\node_modules\hexo-util\lib\spawn.js:51:21)
    at ChildProcess.emit (events.js:314:20)
    at ChildProcess.cp.emit  (D:\StudyMaterials\8_FileManagement\testBlog\blog\node_modules\cross-spawn\lib\enoent.js:34:29)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:275:12)

```

解决此问题的步骤如下：

（1）首先，我重新生成了本地电脑和Github链接的SSH的公钥KEY， 将本地电脑和GitHub重新连接

```shell
git config --global user.name "your github user name"
git config --global user.email "your github 注册邮箱"
```

上述代码执行后，生成公钥

```shell
ssh-keygen -t rsa -C "your github 注册邮箱"
```

此时公钥保存在 C:\Users\ .ssh 的文件夹下，打开id_rsa.pub文件，将里面的内容全部复制； 然后去GitHub的Settings, 找到SSH and GPG keys的页面， 新建一个SSH Key, 将刚才复制的内容粘贴到新建的SSH Key中并点击保存。

保存后输入以下代码检测SSH Key生效

```shell
ssh git@github.com
```

如果出现下面的文字，则本地和Github远程连接上了

```shell
PTY allocation request failed on channel 0
Hi zhangsan! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.
```

（2）按照链接： https://github.com/hexojs/hexo/issues/2778 中的方法，我执行了以下代码

```shell
hexo config deploy.repository git@github.com:pan52yu/pan52yu.github.io.git
```

（3）再次执行 `hexo d` 就没有报错啦 ~
文件成功上传 😊

报错443
https://blog.csdn.net/qq_37555071/article/details/114260533

## 鉴权错误
> https://blog.csdn.net/weixin_41010198/article/details/119698015