---
title: 通俗易懂的 Npm 入门教程
date: 2017-07-03 22:03:29
categories:
- 后端
tags:
- Npm
- 教程
---

> 时常有这样一种感觉，好像这也会那也会，看到啥都感觉知道，但一到应用总是会滞留片刻。原因是不熟，没有形成一套完整的知识体系。

> npm 是一款伴随着 node 出现的包管理工具，本文面向 npm 初学者，是对项目中的 npm 知识体系做的一个总结，当然 npm 的知识不仅如此，后面还会陆续补充更深入的知识点。

# Npm 简介


## Node —— 行走在服务端的 js 

    从接触 js 以来对它的印象就是客户端的脚本语言，用于给页面添加动画效果看起来更加酷炫。

    有一天吃午饭的时候和同事在一起闲聊拉呱，无意间听他说起了 Node.js：“有位大牛用 NodeJS 
    开发了个操作系统 NodeOS”。当时就感觉世界观被改变了，想这么一种弱类型脚本语言也能去做 
    C 语言这种高级语言做的事情，感觉很是不可思议。于是暗下决心，找个时间要学习一下 Node.js。

> NodeJS 2009年5月发布，是一款用 js 开发的服务端语言，编译引擎是谷歌的 v8 引擎。

## Npm —— 减少重复造轮子

### 包的概念

Node 中引入了包的概念，也叫做模块，使用完全开源的思想减少重复造轮子。

打个比方：

    dk 想做一个视频网站，需要使用上传下载技术。

    tiger 君在不久前参与的项目也需要使用上传下载技术，tiger 君是个丰富的老程序员，
    对流的读取读出很是熟悉，于是封装了两个方法，屏蔽了内部复杂的实现机理，
    只留出两个方法供外界调用，只需传入规定的参数便可实现上传下载功能。

    dk 找到 tiger 君要了这个封装的文件（是个 js 文件），于是花了10分钟阅读了该文件的使用说明，
    又花了5分钟实现了上传下载功能。

> 上面所说的 tiger 君封装的 js 文件就是“包”的概念。

### 为什么使用 npm

前面已经说到包对于 Node 来说很重要，可以减少造很多重复的轮子。

但是一个项目中使用到的包可能有几十甚至几百个，完全靠手动管理是很麻烦的。

    yang 君是互联网某个 IP 下的一名程序员，他也想做一个上传下载的功能，
    无奈他并不认识 tiger 君，站在上帝视角的我们看到这一幕不禁就会感到有点尴尬。

> Npm 的推出就是为了解决这个问题。

Npm 提供一个公共的平台，当你写出一个自认为不错的包时，可以发布到这个平台，如果别人有类似的功能需求就可以从这个平台直接下载包。

### npm 命令行工具

> npm 是一款命令行工具。

上面已经说到 npm 提供了这样一个公共平台，但是每一次要使用包都去 Npm 官网下载然后再导入项目中，这看起来并不是很聪明的做法。

npm 开发了一款命令行工具，安装之后，你可以打开的你的黑框口（DOS 窗口）,在里面输入简单的指令就可以下载包，发布包等等与包有关的任何操作。

## Node 与 Npm 的关系

> Npm 的诞生是由于 Node 的需要而发明的。

在之前的版本里，安装 Node 与安装 Npm 你需要下载两个安装包进行安装，这无疑是一件很蠢的事情。

后来官方人员意识到这一点，将 Npm 安装集成到了 Node，也就是说现在你只需要安装一个 Node，就同时安装了 Npm。


## Nvm —— 另一款命令行工具
> Nvm 是一款命令行工具，用于安装 Node。

要安装 Node 可以下载安装包进行安装。（不推荐）

但是 Node 是有版本属性的，就像 JAVA 里有 jdk1.6、jdk1.8 的区别一样。

那么如果你的项目需要使用不同版本的 Node，你总不能下载多个安装包，然后在使用哪个版本的 Node 时将之前版本的 Node 卸载掉。

nvm 和 npm 同样是命令行工具，有这类似的概念：

npm —— 管理包（包括增删改查包）

nvm —— 管理 Node 版本（包括下载 Node,切换 Node 版本）

# Npm 安装

## 使用 nvm 安装 npm 

### 下载 nvm

[nvm 百度云盘下载地址](http://pan.baidu.com/s/1qY2Tqxe)

### 安装图解
（第一次安装默认到底，减少后面环境出错的几率，等玩熟了在自定义位置）

**第一步**

![nvm 安装路径](http://img.blog.csdn.net/20170209085726130?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmFpZHVfMzIyNjIzNzM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**第二步**

![nvm 管理的 node 安装路径](http://img.blog.csdn.net/20170209085757334?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmFpZHVfMzIyNjIzNzM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


安装完成后这两个路径要记下来（后面介绍具体用途）

    nvm安装路径默认为: C://Users/Administrator/AppData/Roming/nvm

    nvm安装的node路径默认为: C://ProgramFile/nodejs （其实是个快捷方式）

### 安装 node 

![安装 node](http://img.blog.csdn.net/20170209091019839?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmFpZHVfMzIyNjIzNzM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

> 安装 node 就等于安装了 npm。

```
$ npm -v    // 查看对应 npm 版本
```

### 切换 node 版本

![切换 node 版本](http://img.blog.csdn.net/20170209091043183?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmFpZHVfMzIyNjIzNzM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

> 每个 node 版本对应一个 npm 版本，切换 node 版本也就意味着切换 npm 版本。

### nvm 常用指令

```
$ nvm version         // 查看nvm版本
$ nvm install 4.6.2   // 安装node4.6.2版本（附带安装npm）
$ nvm uninstall 4.6.2 // 卸载node4.6.2版本
$ nvm list            // 查看node版本
$ nvm use 4.6.2       // 将node版本切换到4.6.2版本
$ nvm root　　　　     // 查看nvm安装路径 
$ nvm install latest  //下载最新的node版本和与之对应的npm版本
```

> nvm 仅仅用来安装 node 和切换 node 版本，使用频率不高，记住这几个指令就够用了。

### nvm 如何实现 node 版本切换

在安装 nvm 时候让记下两个安装路径：（下面称 nvm 路径和 node 路径）
    
    nvm安装路径默认为: C://Users/Administrator/AppData/Roming/nvm
    
    nvm安装的node路径默认为: C://ProgramFile/nodejs （其实是个快捷方式）
    

- 在计算机切换到 nvm 路径：
![nvm 安装目录](https://static.oschina.net/uploads/img/201706/29112602_Wf2j.png "nvm 安装目录")

    这是 dk 计算机上的 nvm 目录，当前安装了两个版本的 node，想使用哪个版本的 node，本质上是创建要使用那个 node 版本的快捷方式，替换 **node 路径**。

- 在计算机切换到 node 路径：
![node 目录](https://static.oschina.net/uploads/img/201706/29113644_umRs.png "node 目录")

    可以看到，这个目录有个 node_modules 文件夹和许多 cmd 命令，未来安装的全局包都放在这里。 

## 配置淘宝镜像

### 淘宝镜像

> Npm 提供一个公共的平台，当你写出一个自认为不错的包时，可以发布到这个平台，如果别人有类似的功能需求就可以从这个平台直接下载包。

这个平台是在国外的服务器上，我们在国内使用 nvm 下载 node 或者 npm 下载包都需要到国外网站上去下载，有时下载速度是很慢很慢的。

淘宝镜像是阿里的淘宝团队整出来的一个平台，相当于将国外 Npm 上的包全部 copy 一份到淘宝镜像这个国内服务器上（你无须担心 Npm 上的包与淘宝镜像上的包是否会有差异，答案是完全一模一样）。

现在我们只需简单的配置一下 nvm 和 npm，让它们在下载的时候优先从国内的淘宝镜像上下载资源，这在项目中会为我们节约很多时间。

### 配置 nvm 淘宝镜像

找到 nvm 安装目录，默认目录是：C://Users/Administrator/AppData/Roming/nvm。

找到里面的 settings.txt 文件，选择记事本打开。

用下面代码替换内容：

```
root: C:\Users\Administrator\AppData\Roaming\nvm
path: C:\Program Files\nodejs
arch: 64
proxy: none
node_mirror: https://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```

> 注意：root 为 nvm 安装路径，path 为 node 安装路径，如果这两个路径不是默认安装，使用下面指令查找并替换即可。

查看 nvm 安装路径：
![查看 nvm 安装路径](https://static.oschina.net/uploads/img/201706/29142133_VLY4.png "查看 nvm 安装路径")

查看 node 安装路径：
![查看 node 安装路径](https://static.oschina.net/uploads/img/201706/29142229_0hKa.png "查看 node 安装路径")


### 配置 npm 淘宝镜像

使用 npm config 指令进行配置（下面代码可直接复制）。

```
$ npm config set registry https://registry.npm.taobao.org
```

使用 npm config list 指令检查 npm 淘宝镜像是否配置成功。

![检查 npm 淘宝镜像是否配置成功](https://static.oschina.net/uploads/space/2017/0601/233833_6cqK_3500483.png)

补充，这里其实也可以和 nvm 配置淘宝镜像一样找到对应的文件，然后填写。（不建议这样做）

上图中 HOME 地址为：C:\Users\Administrator，找到这个目录，会看到 .npmrc 文件，就是 npm 的配置信息文件。以记事本方式打开就可以看到庐山真面目啦。

# npm 创建项目

## npm init 创建 package.json

### 背景故事 

    在很久很久以前，dk 要开发一个前端项目，在计算机的某个旮沓地方建立了一个文件夹叫 dk_project，就称为这是一个“项目”了。

    又过了很久，dk 离开了公司，来了位新同事，在接手 dk 工作的时候发现计算机上面的 dk_project 文件夹，因为没有任何明显的
    标识，就被当成普通文件夹给 DELETE 掉了。

    回到现代，随着 npm 的诞生，人们意识到建立一个项目目录不应该这么草率，于是乎规定，
    如果某个文件夹被创建作为一个项目目录，那么它就应该包含一个 package.json 的文件。

    package.json 文件里记录项目的描述信息：项目作者、项目描述、项目依赖哪些包、插件配置信息等等数不清的好处。
    
### 创建项目描述文件 package.json

> 注意：下面的这些操作都是在黑窗口（DOS窗口）进行的。

**第一步**

在 d 盘下新建一个目录 demo 作为项目目录并在 dos 窗口切换到该目录下。

```
$ d:
$ cd D:\demo
```

**第二步**

使用 npm init 指令创建项目描述文件 package.json。

命令行里会以交互的形式让你填一些项目的介绍信息，依次介绍如下：（不知道怎么填的直接回车、回车...）

- name 项目名称
- version 项目的版本号
- description 项目的描述信息
- entry point 项目的入口文件
- test command 项目启动时脚本命令
- git repository 如果你有 Git 地址，可以将这个项目放到你的 Git 仓库里
- keywords 关键词
- author 作者叫啥
- license 项目要发行的时候需要的证书，平时玩玩忽略它

![npm init](https://static.oschina.net/uploads/img/201706/29144942_SgJp.png "npm init")

**第三步**

在项目目录下会自动生成 package.json 文件，打开可以看到刚才配置的项目信息。

### 来点看得见的东东

在项目根目录下新建文件 index.js，并在其输入以下代码：

```
console.log('dk is handsome.');
```

在 dos 窗口中切换到项目根目录下，输入 node index.js 可以看到打印出来的信息。

```
$ d:
$ cd D:\demo
$ node index.js
```

这是 node 的入门 demo，当然这里并不介绍 Node。

package.json 在整个项目中还是很重要的，我们拿到一个新项目，第一步要看的就是 package.json 文件，这里面会传递项目信息。

## package.json 文件详解

> 阅读本文之前，先看下面这个 package.json 的配置文件，如果每一项你都懂，那本文能带给你的收获也许就比较有限，你可以快速浏览或直接跳过；如果你和十天前的我一样，对很多选项存在着疑惑，那花一段时间慢慢阅读本文，你的疑惑一定一个一个都会消。

```
  "private": true,
  "dependencies": {
    "antd": "^2.11.1",
    "classnames": "^2.2.5"
  },
  "devDependencies": {
    "axios": "^0.15.3",
    "babel-eslint": "^6.1.2"
  },
  "bin": {
	"dk-cli": "./bin/dk-cli.js"
  },
  "scripts": {
    "start": "node index.js"
  },
  "engines": {
    "node": ">=6.9.0",
    "npm": ">=3.10.10"
  }
  "publishConfig": {
    "registry": "http://gongsineibu/nexus/repository/npm-hosted/"
  }
```

### `private` 属性

可选字段，布尔值。如果 private 为 true，npm 会拒绝发布。

这可以防止私有 repositories 不小心被发布出去。

### `dependencies` 与 `devDependencies`

**问题一：dependencies 与 devDependencies 属性区别？**

- dependencies 指定了项目生产环境时所依赖的模块；

- devDependencies 制定了项目开发环境时所依赖的模块。

**问题二：为什么需要生产/开发两个环境？**

举个最简单的例子：

在开发阶段，我们需要对开发的模块进行单元测试，这时在开发环境 devDependencies 下就要安装单元测试模块 Mocha。

在生产环境 dependencies，用户直接使用项目，此时项目的开发已全部完成。这时就不需要单元测试模块了，再加上只会影响性能。

### `bin` 属性

如果你需要使用 npm 开发一个命令行工具，那么这个属性就会很有用。否则请跳过以节约你的时间。

bin 属性可以让我们很简单制作命令行工具，具体如何制作可以跳转：[使用 bin 属性制作命令行工具](https://my.oschina.net/u/3500483/blog/1186407)

### `scripts` 属性

scripts 中文名称为脚本，也就是说可以为我们带来便利。

以上面的配置为例：

scripts 的脚本运行方式是在命令行中输入 npm run <脚本名称>

上图中只有一个 start 值，那么运行时要输入 npm run start

如果不输入脚本，就需要在命令行中输入 node index.js 这行代码

真实情况是输入 node index.js 但是觉得这么太麻烦，就用 npm run start 去代替 node index.js。

### `engines` 属性

一直很纳闷一件事，就是怎么通过 package.json 文件就能知道当前项目依赖 node 的哪个版本，依赖 npm 的哪个版本。。。

后来偶然发现，package.json 中已经提供了这个一个属性，用来记录当前项目依赖 node 和 npm 的版本号。写法如下：
 
`
"engines": {
    "node": ">=6.9.0",
    "npm": ">=3.10.10"
}
` 

### `publishConfig` 属性

当我们开发完自己的项目想要将它发布到 Npm 仓库就需要使用这个属性。默认情况下我们是往 Npm 公共仓库中发布包，默认地址是：`https://www.npmjs.com/`，此时在 `package.json` 文件中可以不加这个属性。

但是如果公司内部搭建了 Npm 私有仓库，此时发布包就不是往 `https://www.npmjs.com/` 这个地址上发布了，而是公司内部提供的地址，`http://gongsineibu/nexus/repository/npm-hosted/`。

此时配置如下：

```
"publishConfig": {
    "registry": "http://gongsineibu/nexus/repository/npm-hosted/"
}
```

补充：
- publicConfig 属性决定了我们发布包发布到哪里去。
- 那么什么决定我们下载包从哪里下载呢？`.npmrc` 文件记录从哪里下载包。

# Npm 包管理工具

## 分清全局安装与本地安装

> 刚学习 npm 的时候，官方教程在介绍 -g 参数时是这么描述的：“加上 -g 参数是全局安装，否则本地安装”。这种说话说一半的感觉真的很让人讨厌，什么是全局什么是本地完全没有介绍。以至于后来很长一段时间才搞明白这个概念。虽然是个小知识点，但我觉得有必要在学习模块安装之前先介绍一下。

### 全局安装和本地安装都安装到了哪？
> 官方文档：使用 -g 安装的模块为全局安装，否则为本地安装。


使用 -g 安装全局模块
``` 
$ npm install express -g
```

不使用 -g 安装本地模块

```
$ npm install path
```

那么问题来了，全局模块和本地模块都安装到哪了？

- 全局模块安装路径

```
// 输入指令查询全局模块安装路径
$ npm list -g --depth=0
```

![查询全局模块安装路径](https://static.oschina.net/uploads/space/2017/0601/234946_2rIq_3500483.png)

- 本地模块安装路径

![本地模块安装路径](https://static.oschina.net/uploads/img/201707/02211548_RHS9.png "本地模块安装路径")

本地模块安装在当前目录下的 node_modules 目录下。

以上图为例：安装本地模块的当前目录是 D:\code，那么在该目录下会自动创建 node_modules 目录（如果之前有这个目录就不会创建了），在 node_modules 目录下就可以找到刚才安装的 path 模块了。

### 全局安装和本地安装的包有啥区别？

我们将一些包全局安装，另一些包本地安装，二者有什么区别呢？

官方的解释是：

- 全局安装的包直接安装到计算机中，只需安装一次，以后在计算机的任何位置都可以使用这个包；

- 看本地安装包安装到哪儿可以知道本地包是安装在当前目录下的，也就是说，只有在当前目录下才可以使用安装的本地包。比如上面贴的图片中安装的本地包 path，只有在 D:\code 目录下才能使用这个包，换做其它任何目录是不能使用它。


![全局安装与本地安装](https://static.oschina.net/uploads/img/201707/02224109_WGZi.png "全局安装与本地安装")

> 总结：全局安装安装一次重复使用，本地安装使用什么安装什么。

### 哪些包我应该全局安装，哪些包又应该本地安装？

既然全局安装安装一次就可以重复使用，那么干嘛不是所有包都使用全局安装呢？

回答上面那个问题之前，先来解释下另一个问题：哪些包应该全局安装？

约定俗成的，对于那些提供命令行工具的包进行全局安装，其它包本地安装。

举例说明：

- 安装 dva-cli 包

```
$ npm install dva-cli -g
```

使用命令行工具输入指令 dva new 创建 dva 脚手架


```
$ dva new dva-demo
```

像这种能提供指令的包通常进行全局安装

- 安装 eslint 包


```
$ npm install eslint -g
```

使用指令 --init 设置一个配置文件


```
$ eslint --init
```

像这种能提供指令的包通常进行全局安装

> 总结：不用管上面两个案例的指令具体是什么，只要提供指令的包都建议使用全局安装；其它包打包是提供一个功能，用于解决某一需求，建议安装成本地包。

你可能还会困惑，我刚开始学习 npm 还不知道哪些包是否提供指令，应该怎么办？没有关系，不知道的全部安装成本地包，在实践中如果发现这个包提供指令，也可以使用后面章节会提到的卸载包，再重新进行全局安装即可。

## 安装模块

> 前面介绍了 npm 的相关概念，从这一节开始介绍 npm 的 API 文档，不需要刻意记忆，使用时查询即可。

### 安装全局模块

- -g 参数的意义

```
$ npm install express -g
或者 
$ npm install -g express 
```

> -g 参数代表着全局，英文单词 global，使用 -g 参数安装的是全局模块。-g 可以写在要安装的包前面或后面位置。

- 全局模块安装到了哪里？


```
// 输入指令查询全局模块安装路径
$ npm list -g --depth=0
```

![查询全局模块安装路径](https://static.oschina.net/uploads/space/2017/0601/234946_2rIq_3500483.png)

### 安装本地模块

- 不使用 -g 参数安装的模块都会安装到本地模块。
```
$ npm install path
```

- 本地模块安装到了哪里？

![本地模块安装路径](https://static.oschina.net/uploads/img/201707/02211548_RHS9.png "本地模块安装路径")

本地模块安装在当前目录下的 node_modules 目录下。

以上图为例：安装本地模块的当前目录是 D:\code，那么在该目录下会自动创建 node_modules 目录（如果之前有这个目录就不会创建了），在 node_modules 目录下就可以找到刚才安装的 path 模块了。

### 安装指定版本的模块

前提是要知道具体的版本号，如果不知道，可以使用 @3.* 表示安装第三版中最新的包。


```
$ npm install react-router@3.* 
```

### 同时安装多个模块

有时候需要安装多个模块，一个个安装太过麻烦，可以一起安装，模块之间以空格隔开即可。

如下为同时安装 antd 模块和 babel-plugin-import 模块。


```
$ npm install antd babel-plugin-import 
```

### 安装 package.json 中的包

package.json 文件其中一个重要功能就是记录当前项目的依赖包有哪些。

- 这样在下载完一个项目时； 
- 查看 package.json 可以知道项目依赖哪些包； 
- 使用 npm install 指令可以安装 package.json 中记录的依赖包；
- 安装完依赖包，才可以启动项目把项目给跑起来。


```
$ npm install   // 就是这么简单，当然前提是有 package.json 并且里面配置了相关包信息
```

### --save 的用途

- --save 和 -g 一样，属于参数。

- 在安装模块时，只有当事人知道安装了哪些模块，如果换另外一个人来看这个项目，是不会知道这个项目安装了哪些模块。

- 使用 --save 可以在安装模块时，同时将安装的模块信息记录在 package.json 文件中，这样第三个人再看这个项目时，就可以直接看 package.json 文件来了解这个项目依赖了哪些包。


```
$ npm install antd --save
```

> 使用 --save 可以将模块信息记录到 package.json 文件中 dependencies 属性中。

![使用 --save 安装模块](https://static.oschina.net/uploads/img/201707/02214921_beEW.png "使用 --save 安装模块")

### --save-dev 的用途

> 与 --save 参数类似，--save-dev 也会将模块信息记录到 package.json 文件中，不同的是记录在文件中的 devDependencies 属性下。

**问题一：dependencies 与 devDependencies 属性区别？**

- dependencies 指定了项目生产环境时所依赖的模块；

- devDependencies 制定了项目开发环境时所依赖的模块。

**问题二：开发环境和生产环境是什么？**

![开发环境和生产环境](https://static.oschina.net/uploads/img/201707/02220451_uxR9.png "开发环境和生产环境")

**问题三：为什么需要生产/开发两个环境？**

举个最简单的例子：

在开发阶段，我们需要对开发的模块进行单元测试，这时在开发环境 devDependencies 下就要安装单元测试模块。

在生产环境 dependencies，用户直接使用项目，此时项目的开发已全部完成。这时就不需要单元测试模块了，再加上只会影响性能。

## 卸载模块

> 安装模块使用 install 指令，卸载模块自然就使用 uninstall 指令，下图是为卸载 lodash 模块。

### 只卸载模块

由于之前安装过，在 package.json 中的记录仍然存在

```
$ npm uninstall lodash
```

### --save 参数使用

卸载模块的同时删除在 package.json 文件中的记录

```
$ npm uninstall lodash --save
```

### 卸载指定版本的模块

```
$ npm uninstall lodash@3.*  // 卸载 lodash 模块 3.* 版本
```

### 应用场景：

    dk 在项目中先安装了 lodash 4.17.4 版本的模块；
    
    后来发现这个版本太高，存在一些新 bug 没有解决，不适合项目中使用；

    dk 想要安装版本更加稳定的3.* 版本， 于是敲入指令 $ npm install lodash@3.*；

    到这里就注意了，虽然第二次安装了 3.* 版本，但是由于之前的 4.* 版本并没有卸载，
    此时在本地安装路径中可以发现有两个版本 lodash 的模块，
    而 npm 默认在使用时会优先调用高版本的模块。

    这时将之前版本进行卸载，$ npm install lodash，会默认卸载高版本的模块。

> 总结：为避免同一个模块多个版本之间造成隐藏问题，最佳实践是安装一个模块的新版本时对这个模块之前的版本进行卸载。

## 更新模块

> 更新模块使用 npm update 指令。

### 更新模块注意事项
    
- 更新模块只能往后面版本更新，不能往老的版本回滚更新。
    
    比如先安装了 lodash 模块 3.9.* 版本，可以往后更新到 3.10.* 版本，但是不能往前更新回 3.8.* 版本。

- 更新模块只能更新到小版本号最新的那个版本，不能更新大版本号。
    
    一个模块的版本号由三部分组成：大版本号.小版本号.次版本号。
        
    如 lodash 模块的某个版本 3.9.1，其中：
            
    - 3 是大版本号
    - 9 是小版本号
    - 1 是次版本号
        
    使用 $ npm update lodash 只能将 3.9.1 更新到小版本号最大的那个版本，这里是 3.10.1，而不能更新到 4.* 版本。这一点要**特别特别特别的注意**。
 
   
### 不带参数的更新模块

会更新到小版本号最新的那个版本，但不会更新到大版本号。
    
```
$ npm update lodash 
```
    
### --save 参数

更新模块的同时将更新信息记录到 package.json 文件中

```
$ npm update lodash --save
```

### 指定更新的版本

就算指定版本，也只能更新到小版本号最新的那个版本，对大版本号不起作用

比如 lodash 的版本号有：3.9.1、3.10.1、4.13.1
    
当前已经安装的 lodash 版本是 3.9.1
    
```
$ npm update lodash@4.13.1 --save
```

此时由于对大版本号进行更新，结果没有任何响应，依然是 3.9.1 版本。

```
$ npm update lodash@3.10.* --save
```

此时并没有更改大版本号，更新结果为 lodash 的版本变成了 3.10.1 版本。

## 查看模块

> 在实际项目中，我们可能需要查询全局/本地都安装了哪些模块，来做下一步的操作。本节介绍如何查询已经安装的模块。

### 查看全局模块

与安装模块一样，-g 参数在 npm 中就代表着全局的含义。

```
$ npm list -g
```

使用这条指令不仅会打印出全局安装的模块，还会显示全局安装的路径。

> 你会发现，确实打印出来了所有的全局模块，但是这些模块的依赖模块也一并打印出来，结果就是一个很长很长的树级显示，不方便查找，后面介绍解决方法。

### 查看本地模块

不使用 -g 参数查询的就是已安装的本地模块，但前提是当前目录下有 node_modules 目录。

```
$ npm list
```

使用这条指令也会打印出本地模块安装路径。

> 良好的操作习惯是在安装模块时使用 --save 将安装了哪些模块都记录在 package.json 中，这样如果要查询可以直接去 package.json 中查看即可。

### --depth=0 参数的用法

> --depth=0 表示查询模块，只显示第一层级的模块。这里的 0 如果换成 1 就是显示第一层级和第二层级的模块，依次类推。

- 查看全局模块的一级目录

```
$ npm list -g --depth=0
```

![查询第一层级的全局模块](https://static.oschina.net/uploads/img/201707/03101304_OgUb.png "查询第一层级的全局模块")

- 查询本地模块的两级目录

```
$ npm list --depth=1
```

![查询本地模块的两级目录](https://static.oschina.net/uploads/img/201707/03101428_KwYU.png "查询本地模块的两级目录")

# 使用 npm 可能存在的困惑

## 安装的模块不知道怎么用

> 作者在刚开始学习 node 的时候，跟随大牛们的 demo 去做一个个实战项目，过程中安装了很多模块，对对于这些模块的 API 却不知道去哪里查看。后来发现有个网站基本可以查询到所有模块的 API，好东西当然要拿出来分享啦。

目标地址：[淘宝镜像官网](https://npm.taobao.org/)

1.登录官网，输入要查询的模块名，回车

![淘宝镜像一](https://static.oschina.net/uploads/img/201707/03103248_uioP.png "淘宝镜像一")

2.选择模块，点击进入

![淘宝镜像二](https://static.oschina.net/uploads/img/201707/03103332_aiYa.png "淘宝镜像二")

3.显示该模块的 API 文档

![淘宝镜像三](https://static.oschina.net/uploads/img/201707/03103407_PTYN.png "淘宝镜像三")

> 总结：整体来说 API 还算比较完整与详细，唯一的遗憾就是大多是英文文档，当然对于一个程序员来说，看懂它应该还是没问题的。

## 解决 npm 中安装包不是内部或外部命令的问题

问题背景：

经常使用 npm 全局安装了一些包后，需要使用这些指令，但是在dos窗口中却总是报错：不是内部或外部命令。如：

全局安装了 supervisor 包： （其它全局包问题类似）

$ npm install -g supervisor

$ supervisor -v // 报错 supervisor 不是内部或外部命令

报错信息：

supervisor 不是内部或外部命令.....

解决方法：

第一步：查询系统安装了哪些全局包

$ npm list -g --depth=0

备注：

1.depth=零，不是哦

2.会返回如下信息：其实就是supervisor安装的位置

C:\Program Files\nodejs\node_global

+-- express@4.14.1

+-- nodemon@1.11.0

`-- supervisor@0.12.0

第二步：

将 C:\Program Files\nodejs\node_global 添加到环境变量下的 Path 字段中

备注：

这是我电脑里的路径地址，每个人可能自己设置过不一样了，使用$ npm list -g --depth=0找到路径。

环境变量打开方法：

【我的电脑】 -- 【右键属性】 -- 【左边栏选择“高级系统设置”】 -- 【选择“环境变量”按钮】-- 【在弹出的对话框中在系统变量中找到Paht字段】 -- 【编辑添加你电脑里的上述路径】

第三步：

关闭cmd窗口重新打开，键入 supervisor 看到弹出一大串东西就ok了。

## 使用 npm 制作命令行脚本工具（一）

> 本文需要对 npm 有基本了解，并且安装了 node 和 npm，如果你对 npm 一无所知，请参阅 [通俗易懂多的 Npm 入门教程](https://my.oschina.net/u/3500483/blog/1138568) 先了解基础概念。

### 什么是命令行工具

在使用 npm 安装完全局包时，我们通常会使用它们提供的命令行操作来完成工作。

npm 本身就是一个命令行工具，它随着 node 的安装自动安装，我们可以使用 npm 的命令行操作来完成诸如安装包，卸载包等操作。

![npm 命令行工具](https://static.oschina.net/uploads/img/201707/06100022_SOBW.png "npm 命令行工具")

### 为什么需要命令行工具

> 既然是工具当然是用来提高效率。

比较常见的附带命令行工具的全局包有脚手架工具。

你可能不理解脚手架啥意思，你可以认为是在开发一个项目时，我们需要按照规范先布置好目录结构，比如下面这样：

![脚手架目录](https://static.oschina.net/uploads/img/201707/06100802_QCY6.png "脚手架目录")

现在我们需要频繁的创建项目，而每个项目的目录结构都和上面一样，你总不能一个个新建一个空目录，再一步步新建子文件夹吧。

命令行工具可以让我们偷会懒喝杯咖啡，它会自动为我们创建指定的目录结构。

当然命令行工具不仅仅可以创建脚手架这一个功能，换句话只要是频繁的行为都可以使用它来制作脚本，之后一键完成。

本篇算是命令行制作的入门，不会介绍如何创建脚手架目录，会介绍制作简单的命令行工具，可以查看版本与帮助信息。有了入门，还担心不能改变世界吗！！


### 关于 process.argv 的使用

> process 是 node 自带的全局变量，可以直接使用，process.argv 可以获得命令行中参数数组。举个栗子：
    
- 在 d 盘根目录下创建文件夹 dk-cli（d:\dk-cli）

- 在 dk-cli 目录下新建 index.js 文件，此时目录结构变成这样的：

    ```
    - dk-cli
        + index.js
    ```

- 在 index.js 中输入以下内容：

    ```
    console.log(process.argv); 
    ```

- 在命令中输入：

    ![命令行获取参数](https://static.oschina.net/uploads/img/201707/06103134_hSTc.png "命令行获取参数")

- 结论：

    使用 process.argv 可以获取参数数组;
    
    数组第一项为 node.exe 安装路径；
    
    数组第二项为当前执行 js 文件路径，可以看到当前执行的是 index.js 文件，所以路径就是 index.js 的路径；

    数组前两项我们并不关心，我们只关心从第三项开始的数据，这些是在命令行中输入的参数，参数之间以空格隔开;
    
    使用 process.argv.slice(2) 就表示从数组第三个值开始截取，获取命令行中输入的参数数组。


### 创建 dk-cli 命令行工具

- 我们希望达到的效果

    ![命令行工具运行结果](https://static.oschina.net/uploads/img/201707/06105459_W7cT.png "命令行工具运行结果")
    
- dos 窗口切换到该 dk-cli 

    ```
    $ cd d:\dk-cli
    ```

- 创始化项目

    ```
    $ npm init
    ```
    使用该命令会出现交互式提问/回答，一路回车即可，完成操作后在 d:\dk-cli 目录下会出现一个 package.json 文件。

- 在 dk-cli 目录下新建文件夹 bin

    为什么文件夹叫 bin，这是大家约定俗称的，将命令行文件都放在 bin 目录下。

- 在 bin 目录下新建文件 dk-cli.js

    此时目录结构应该是这样的：
        
    ```
    - dk-cli
        - bin
            + dk-cli.js
        + index.js
        + package.json
    ```

    dk-cli.js 内容如下：
    
    ```
    #!/usr/bin/env node
    
    function run (argv) {
    	
    	if (argv[0] === '-v' || argv[0] === '--version') {
    		
    		console.log('  version is 0.0.1');
    		
    	} else if (argv[0] === '-h' || argv[0] === '--help') {
    		
    		console.log('  usage:\n');
    		console.log('  -v --version [show version]');
    		
    	}
    	
    }
    
    run(process.argv.slice(2));
    ```
    - 文件头部必须有 #!/usr/bin/env node 这么一行，意思是使用 node 进行脚本的解释程序，那下面的就可以使用 node 的语法了；
    
    - run 是个方法，这里不解释，传入参数，if ... else 判断输出结果；
    
    - 执行 run 方法，参数为命令行中输入的参数；
    
- 打开 package.json 在对象中添加 **bin 属性**

    ```
    {
    ...
    
    "bin": {
        "dk-cli": "./bin/dk-cli.js"
      },　
    
    ...
    }
    ```
    bin 属性包含一个对象，对象中是键值对，这里是 {"dk-cli": "./bin/dk-cli.js"}，这里的键值为 "dk-cli" 所以我们在命令行中输入的是 ```$ dk-cli -v```，如果把这里的键值改为 other-cli，那么在命令行中输入的就应该是 ```$ other-cli -v``` ，简单点说键值就对应着命令行工具的命令名称。而值为 "./bin/dk-cli.js"，表示在我们写的这个 dk-cli 目录下根据这个路径可以找到 dk-cli.js 文件，然后执行它。整体意思就是：在 dos 窗口中输入键值"dk-cli"，系统会帮我们自动去执行 "./bin/dk-cli.js" 对应的文件 dk-cli.js。
    
- 将 dk-cli 目录打成一个全局包

    上面已经完成了 80 % 的工作，接下来需要将 dk-cli 目录在本地安装为一个全局包，这样 package.json 中的 bin 属性才会起作用。
    
    ![安装本地全局包](https://static.oschina.net/uploads/img/201707/06105231_zyvz.png "安装本地全局包")
    
   如果每一次修改了 dk-cli 工程，都要在 dos 窗口中敲一下 `npm install . -g`，这样很麻烦，Npm 提供了一个简便的方法，在当前项目路径下敲入：`npm link`，作用和 `npm install . -g` 一样，在测试阶段带来很大的便利。

    这里 **.** 指代的就是执行目录 d:\dk-cli。因为我们安装的是 dk-cli 下的 package.json 文件。
    
- 现在可以在 dos 窗口中测试结果了

    ![命令行工具运行结果](https://static.oschina.net/uploads/img/201707/06105459_W7cT.png "命令行工具运行结果")