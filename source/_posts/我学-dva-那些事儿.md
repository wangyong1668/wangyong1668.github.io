---
title: 我学 dva 那些事儿
date: 2017-07-01 20:08:41
categories:
- 前端
tags:
- js框架
- Dva
- 状态管理器
---

今天正式入驻简书，准备开一个专题总结下最近的学习成果 —— dva。

> dva 是由阿里架构师 sorrycc 带领 team 完成的一套前端框架，在作者的 github 里是这么描述它的：“dva 是 react 和 redux 的最佳实践”。

# antd-admin（推荐 x 1）

antd-admin 是基于 dva + antd 的一个后管系统，作者：[zuiidea](https://github.com/zuiidea)，项目：[antd-admin](https://github.com/zuiidea/antd-admin)，github 上也有 2000+ stats，有 dva 经验的可以看下，楼主当时也是边学习 dva 概念，边研究这个项目的。

这里面没有布局的概念，做到后面遇到一些坑。作者貌似因为 Ant Design Pro 的出现也不维护项目了。不建议以此学习了。

# Ant Design Pro（推荐 x 5）

阿里自己整的一套开箱即用的中台前端/设计解决方案 [Ant Design Pro github](https://github.com/ant-design/ant-design-pro)。初学者以这个入手，可以省很多事：框架搭建、布局、权限等等。[点我在线预览效果](https://preview.pro.ant.design/#/dashboard/analysis)、[官方文档](https://pro.ant.design/index-cn)。

# umi（中文名：五米，推荐 x 5）

同样是 dva 作者 sorrycc 最近整的新玩意，2018.2.26 发布的 1.0 版本。sorrycc 认为之前 dva 固然好，但还要用户自己引入 UI 工具 antd，打包工具 roadhog，路由 react-router，状态管理器 dva，这些很麻烦！前端发展越来越快，为什么不揉到一体呢！！

- [作者原文 Blog：Hello!umi，介绍了他为什么要整这个东东](https://umijs.org/blog/2018/01/19/hello-umi.html)
- [快速上手，开发体验爽到不行](https://umijs.org/docs/zh-Hans/getting-started.html)
- [umi github](https://github.com/umijs/umi)

# antv（图表库，推荐 x 5）

与 Antd 风格相同，阿里自己研发的图表库。值得关注。

- [Antv 官网](https://antv.alipay.com/zh-cn/index.html)
- [Antv 图表用法，告诉你你的数据适合用什么图表进行展示](https://antv.alipay.com/zh-cn/vis/chart/index.html)

# 先吐槽

学习的过程并非一帆风顺，遇到的一些问题对于非科班出身的我来说简直不可思议。
- 教程字典化

  - 现在的网络很发达，看似学习资料沧海星空，实则大多是一篇篇的字典，很难寻找到一篇用心编写的好教程。

  - 什么是字典化：复制的 API 文档。就像新华字典一样，我承认里面拥有所有的汉字，但是不知道查字典的方法抱着一本字典又有什么用呢？一个汉字一个汉字的去记忆会打击学习的兴趣。

  -  我理解的好教程 。花费80%的时间去跟你讲清楚这究竟是个什么东西——这就像查阅字典的方法。API 仅仅是“帮助文档”而已。

 

- 学会 dva 和学会在项目中使用 dva 是两码事
  - 学会使用  dva 开发一个增删改查的模块是很快的，我花费了三天。

  - 写完代码还需要提交，这就接触了此前没有使用过的 git 分布式版本控制工具。

  - 老板告诉我，你写的代码不能直接提交，要经过 ESLint 工具做代码审查之后才能放心的提交到版本库。

  - 提交到版本库只是开发环境时做的事情，考虑到将来上线还需要学习诸如 webpack 构建工具来进行项目打包。

  - .............

  - 项目中的 dva 远不止用到这些工具，还需要其它的一系列技术点才能组成一套完整的体系。

> 而我现在要做的事，就是将过程遇到的所有技术编写成一篇篇的***好教程***，便于需要它的人不会遇到我先前遇到的窘境。

# 为什么写这篇 Blog
起初，是由于项目的需要，开始正式接触 dva。

dva 的开发模式不同于传统页面的开发，学习起来有一定的技术门槛。

 在 [dva](https://github.com/dvajs/dva) 的作者[sorrycc Blog](https://github.com/sorrycc/blog/issues) 里逗留了数个日夜，对新的开发模式好像是理解了，于是就迫不及待的下载了一个 demo，准备开发 CRUD。

故事情节的发展往往不会这么顺利，开发过程中发现，学会 dva 和在项目中使用 dva 进行完整开发完全是两码事：因为项目不仅仅是代码开发，还要关注项目的整体生命周期，代码规范，打包上线等等。

![初学 dva 时心里变化](http://upload-images.jianshu.io/upload_images/6693922-82ef3a8dd35e419f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "初学 dva 时心里变化")

如果你和我一样，不能容忍在你的项目存在看不懂的东东，哪怕是一个配置文件，存在必有价值，不明白的东东就该 ~~DELETE~~ 掉，那么来到这里就对了，本文就是对 dva 做一次“大手术”，肢解它的技术组成，掌握项目中的 dva。

# 项目中的 dva
![项目中的 dva](http://upload-images.jianshu.io/upload_images/6693922-e923e0532dc83c37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "项目中的 dva")

# 目录

- 初识 dva
    - [dva 简介](https://my.oschina.net/u/3500483/blog/1058145) 
    - [创建一个 dva 脚手架工程](https://my.oschina.net/u/3500483/blog/1057996) 
    - [dva 脚手架目录分析](https://my.oschina.net/u/3500483/blog/1058203) 
    - [12 步 30 分钟，完成用户管理的 CURD 应用 ](https://github.com/sorrycc/blog/issues/18)
- 语言基础
    - [ES6](http://es6.ruanyifeng.com/)
    - [React](http://www.runoob.com/react/react-tutorial.html)
- 工具
    - [通俗易懂的 Npm 入门教程](https://my.oschina.net/u/3500483/blog/1138568) 
    - [循序渐进的 Git 入门教程](https://my.oschina.net/u/3500483/blog/1154601) 
    - [开发工具 Webstorm](http://www.jianshu.com/p/4ce97b360c13)
    - [项目启动打包工具 Roadhog](https://github.com/sorrycc/roadhog)
    - [代码格式化工具 ESLint](https://my.oschina.net/u/3500483/blog/1506899)
    - [es6 编译工具 Babel](https://my.oschina.net/u/3500483/blog/1517941)
    - [模拟后台提供接口工具 json-server](https://github.com/typicode/json-server#routes)
     - [制作假数据工具 Mock](https://github.com/nuysoft/Mock/wiki/Getting-Started)
     - [使用 Less 写样式](http://www.jianshu.com/p/48018e5da7dd)
     - [Iconfont 图标库使用](http://www.jianshu.com/p/0fc36e7f7d2e)
- 核心技术
    - [react-router 4.0](https://reacttraining.com/react-router/web/guides/philosophy)
    - [redux](https://github.com/reactjs/redux)
    - [axios](https://segmentfault.com/a/1190000008470355?utm_source=tuicool&utm_medium=referral)
    - [antd](https://ant.design/docs/react/i18n-cn)
- dva 相关 Git 社区地址
    - [dva](https://github.com/dvajs/dva)
    - [roadhog](https://github.com/sorrycc/roadhog)
    - [ant-design](https://github.com/ant-design/ant-design)
    - [antd-admin](https://github.com/zuiidea/antd-admin)
    - [umi（五米）](https://github.com/umijs/umi)
    - [ant-design-pro](https://github.com/ant-design/ant-design-pro)
- 推荐开源项目
    - [React 全家桶](http://blog.csdn.net/awaw00/article/category/6692955)
    - [12 步 30 分钟，完成用户管理的 CURD 应用 ](https://github.com/sorrycc/blog/issues/18)
    - [antd-admin 后台管理系统](https://github.com/zuiidea/antd-admin)


# dva README.md 之外的文档

dva 目前还没有自己的社区，如果学习过程中遇到问题可以去 dva 的 github issue 库去寻找答案，目前已经 close 了 1160 个问题，也就是你遇到的问题大多前人都已遇到过。当然也有人不愿意或不熟悉 github 的，这里也记录一些作者遇到的坑的解决方法。

- [dva  2.0 版本中如何使用 router 4.0](http://www.jianshu.com/p/c5ec9ffa29be)
- [dva 2.0中如何使用代码进行路由跳转](http://www.jianshu.com/p/7de59752b8a8)
- [dva 中使用 browserHistory 文档](http://www.jianshu.com/p/2e9e45e9a880)
- [dva 处理页面加载过渡组件 dva-loading 使用文档](http://www.jianshu.com/p/61fe7a57fad4)
- [dva 中使用自定义 iconfont 注意事项](http://www.jianshu.com/p/d9b89cb3afb5)
- [dva 更新2.0版本遇到问题汇总](http://www.jianshu.com/p/649e97ff4354)
- [dva 中进行页面复用实践总结](https://www.jianshu.com/p/e0a220906301)
- [解决 dva 注销退出系统后再点击登入，state 数据未清空的问题？](https://www.jianshu.com/p/a4122272d615)
 - [dva 中使用 axios 统一拦截错误示例](https://www.jianshu.com/p/d6796986e2ab)


# 备注
之前一直在开源中国上写博客，一些 dva 相关博客也在那上面，还没时间转过来，有需要的可以跳转看看。[开源中国博客地址](https://my.oschina.net/u/3500483/blog/1057798)、[github 上博文地址](https://github.com/dkvirus/dva) 
