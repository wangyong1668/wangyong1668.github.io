---
title: 解决 git 中【user.name has multiple value】的错误
date: 2017-06-20 21:04:03
categories:
- 运维
tags:
- Git
- 版本控制工具
- 报错
---

安装完 git 之后，需要在命令行中输入名称和邮箱来登录自己的 git 。

# 网上教程是这么教的


```
$ git config --global user.name "你的 git 的名称"
$ git config --global user.email "你的 git 的邮箱"
```

然而现实并没有想象中顺利，报了下面的错误：
![git](https://static.oschina.net/uploads/img/201706/20144437_GZNN.jpg "git")

# 原因


```
$ git config --list        // 这条指令可以查看到 git 相关配置信息,可以看到已经无意间添加了多个 name 值
```
![查看 git 配置信息](https://static.oschina.net/uploads/img/201706/20144718_Dqmb.jpg "查看 git 配置信息")

# 解决方法

```
$ git config --global --replace-all user.name "你的 git 的名称"
$ git config --global --replace-all uesr.email "你的 git 的邮箱"
```

做完这一步，再键入  $ git config --list  会发现 name 和 email 只有一个值了，这时候就不会报错了。