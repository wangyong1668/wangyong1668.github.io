---
title: dva 中使用自定义 Iconfont 需要注意的问题
date: 2017-10-23 20:40:11
categories:
- 前端
tags:
- js框架
- Dva
- Iconfont
---

# 背景

ant-design 官网提供了一部分图标，但种类并不能满足实际开发，需要去阿里图标库挑选些的图标来使用。

# 问题
![Antd 表单](http://upload-images.jianshu.io/upload_images/6693922-4fab544bb0d3829b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用过 Antd 的应该知道上面反馈成功的图标显示有误，应该是一个蓝色的√。

# 思路

起初以为是 antd 的样式没有引进来，可是想想又不对，反馈失败图标 —— 红色×是可以显示正常的，后来点了下整个网站发现只有反馈成功图标显示有问题，F12 打开控制台查源码如下：

![反馈成功图标](http://upload-images.jianshu.io/upload_images/6693922-60a0c99362a42c9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，这里用到一个伪类选择器。内容为：'\E630'。

在此之前，我刚好去阿里图标库 Iconfont 自己挑选了一批图标，下载到本地，也就是这几个文件：

![iconfont 图标](http://upload-images.jianshu.io/upload_images/6693922-31ccf32102331e28.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开 iconfont.css 文件，发现果然与其中一个图标的 unicode 冲突了。而冲突的那个图标的样子就是最上面那张图的图形。

iconfont.css
```
...
.anticon-stop:before { content: "\E630"; }
...
```

 # 解决

直接删除本地 iconfont.css 文件中冲突的那个图标是没用的，需要重新去阿里图标库，删除冲突的那个图标，重新下载。