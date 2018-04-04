---
title: Travis 持续集成自动发布博客文章
date: 2018-03-20 17:45:41
categories:
- 运维
tags:
- 持续集成
- Hexo
- Travis
- Github
- "Github Page"
---

![Travis + Hexo + Github](https://gitee.com/uploads/images/2018/0320/155028_c2b8ae3a_1623141.png "屏幕截图.png")

# 期待

上一篇 《Hexo 搭建个人博客》，最终可以在本机运行 `$ hexo server -p 8000`，在浏览器中输入 `localhost:8000` 进行访问。这显然不是我们想要的，我们期待：

1. 在浏览器中输入域名就可以看到自己的博客；
2. 在本机上写文章，通过 `git push` 将文章推送到 github 上就可以看到博客上文章的更新。

在往下读之前，确保你的机器上安装了 Git，不熟悉 Git 的看这里 => [《廖雪峰-Git教程》](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)。也确保你知道 Github 是个啥，不知道的去点一点。

<!-- more -->

# Github Pages

**注册 Github 账号**

`https://github.com/` 官网右上角注册。

我这里已经注册过了，用户名是 `dkvirus`。

**新建仓库**

这个仓库的名称和你 github 用户名有关。比如我 github 用户名是 `dkvirus`，我仓库的名称就只能是 `dkvirus.github.io`。

![新建仓库](https://gitee.com/uploads/images/2018/0320/162027_0f108c5d_1623141.png "屏幕截图.png")

**克隆代码到本地**

![克隆代码到本地](https://gitee.com/uploads/images/2018/0320/162102_02a1a158_1623141.png "屏幕截图.png")

**创建index.html文件**

![创建index.html文件](https://gitee.com/uploads/images/2018/0320/162215_f7f35874_1623141.png "屏幕截图.png")

**把index.html提交到github上**

![把index.html提交到github上](https://gitee.com/uploads/images/2018/0320/162300_1daa3205_1623141.png "屏幕截图.png")

**看效果**

在浏览器中输入你的项目名称 `username.github.io` 即可看到你刚才写的 index.html 页面。

![看效果](https://gitee.com/uploads/images/2018/0320/162350_f2c63a0c_1623141.png "屏幕截图.png")

**总结**

username.github.io 这个仓库也叫做 Github Pages，里面可以放静态页面，Github 这个网站自动为你部署，起到了 Web 服务器的作用。（不理解 Web 服务器的看这里 => [《图解 Web 服务器》](https://www.jianshu.com/p/dad9002076a9)）

而 Hexo 可以通过 `$ hexo generate` 自动生成静态页面（在 public 目录下），将 public 目录下所有文件放在 Github Pages 里，不就可以通过域名访问了吗？这是思路。

到这里为止，我们第一个期待的事情已经有思路了。**在浏览器中通过域名可以看到自己的博客**。

# Travis

在之前的博客里我有讨论过持续集成这个概念，我总结就是：**你只负责开发，其它的事让程序去做。**

## 思路

第一步：我们希望有个东东可以帮助我们监控 github 上的代码，只要代码变化就去执行程序；

第二步：程序具体做什么事情：执行 `$ hexo generate` 指令生成博客的静态资源 public，自动将 public 目录下的子文件复制到 username.github.io 仓库中。

在 username.github.io 仓库中建两个分支：`master` 分支和 `develop` 分支，`master` 分支存放静态资源，`develop` 存放 hexo 博客源代码。如下：

![图一：master 分支存放静态资源](https://gitee.com/uploads/images/2018/0320/173405_b57ab446_1623141.png "屏幕截图.png")

![图二：develop 分支存放源代码](https://gitee.com/uploads/images/2018/0320/173444_807dab5f_1623141.png "屏幕截图.png")

我们在 `develop` 分支写文章，每当提交 `develop` 分支代码时，我们希望有个东东可以帮我们执行 `$ hexo generate` 生成 public 目录，并将 public 的子文件，复制到 `master` 分支上，这样在 username.github.io 中就可以查看我们的博客了。

## Travis 监控 github 代码变化

Travis 可以帮助监控 github 代码变化，它是一个网站：[Travis CI 官网](https://travis-ci.org/)，点击进入，使用 github 账号进行登录。可以看到下面这个页面。

**第一步：监控 github 仓库的页面**

![首页](https://gitee.com/uploads/images/2018/0320/163519_e9ecab14_1623141.png "屏幕截图.png")

这个页面就是你要监控哪个 github 仓库的页面。第一次进来由于没有选择要监控的仓库，矩形框应该都是空白的（这里有是因为我之前有添加过），点击椭圆框跳转另一个页面去选择要监控哪些 github 仓库。点击跳转页面如下：

**第二步：选择要监控的仓库**

![选择要监控的仓库](https://gitee.com/uploads/images/2018/0320/170159_67f0ea41_1623141.png "屏幕截图.png")

在这个页面中，想要监控哪个仓库，就点击开关按钮。如果下方没有仓库，点击右上角按钮可以实时同步 github 仓库。选择完成后点击后面的链接进入前一个页面。

**第三步：进行相应配置**

![进行相关配置](https://gitee.com/uploads/images/2018/0320/170354_acb380f8_1623141.png "屏幕截图.png")

这里要进行相关配置，上图中数字2按要求配置。

数字3配置环境变量。这个的目的是：travis 网站之后要将 Hexo 生成的静态资源复制到 github 上，得有权限才行，这里就配置 github 给它的权限 token。**token怎么生成，看第四步，待会记得回来填值**。

**第四步：github 生成 token**

在 github 上找到 Personal access tokens 页面，具体路径看下面。

![github 生成 token](https://gitee.com/uploads/images/2018/0320/170952_9679df1e_1623141.png "屏幕截图.png")

点击 Generate new token 出现下方页面，勾选相应权限。

![输入图片说明](https://gitee.com/uploads/images/2018/0320/171047_f2bd0d03_1623141.png "在这里输入图片标题")

最终生成一串数字，回到第三步，有个环境变量 GH_TOKEN 的值还没填，把这串数字放进去。

## Travis 监控到代码变化后执行程序

Travis 监控到 github 仓库代码变化后需要执行程序。在项目根目录下创建 `.travis.yml` 文件，填写要做的事。Travis 监控到代码变化就会去项目根目录下找这个文件，找到就做事，找不到就啥也不做。

切换代码到 develop 分支，添加 .travis.yml 文件，复制下面内容。

### 配置文件

```
# 运行环境，Travis 会自动提供这个环境
language: node_js
node_js: stable

# S: Build Lifecycle
# 安装依赖包
install:
  - npm install

# 生成博客静态资源
script:
  - hexo g

# 将静态资源推送到 master 分支上
after_script:
  # 找到静态资源
  - cd ./public
  - git init
  # user.name 和 user.email 让 travis 能登录你的 github
  - git config user.name "dkvirus"
  - git config user.email "xxxxxx@qq.com"
  - git add .
  - git commit -m "docs:update articles"
  # GH_TOKEN 为你的 github 允许 travis 访问生成的凭证
  # GH_REF 告诉 travis 往哪个仓库推送代码，具体值下面配置
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master
# E: Build LifeCycle

branches:
  only:
    # 设置监控分支，当 develop 分支代码变化时，执行这个程序
    - develop
env:
  global:
    # 定义往哪个仓库推送代码，注意事项见下面
    - GH_REF: github.com/dkvirus/dkvirus.github.io.git
```

### 配置文件做了什么事

- `script:` 生成静态资源
- `after_script:` 将静态资源推送到 username.github.io 仓库的 master 分支

### 你需要修改的地方

- `git config user.name`：你登录 github 时的用户名
- `git config user.email`：你登录 github 时的邮箱地址
  
    name 和 email 能让 Travis 登录你的 github。

- `GH_REF: github.com/dkvirus/dkvirus.github.io.git`
    
    GH_REF 能让 Travis 知道往哪个仓库推送代码。

**GH_REF注意事项：**

地址为 http 地址，并且去掉协议名称，如：`https://github.com/dkvirus/dkvirus.github.io.git`，这里只需填写：`github.com/dkvirus/dkvirus.github.io.git`。

![需要注意](https://gitee.com/uploads/images/2018/0320/172803_c44416da_1623141.png "屏幕截图.png")

## 测试

将 develop 分支推送到 github 上，过几分钟查看 master 分支是否成功生成静态资源。

# 绑定自己的域名

如果你不满意 github 为你分配的域名 `dkvirus.github.io`，并且拥有自己的域名 `tmd.dkvirus.com`，可以接着往下看如何绑定自己的域名。

## 配置域名绑定

将你自己的域名与 github 域名绑定在一起。这样当你访问自己域名 `tmd.dkvirus.com`，实际上看的是 `dkvirus.github.io` 的页面。

我是在腾讯云上购买过云主机，上面免费提供域名解析，记录如下：

![输入图片说明](https://gitee.com/uploads/images/2018/0320/173756_31338640_1623141.png "屏幕截图.png")

## github 上绑定域名

github 上绑定域名的目的是当你访问 `dkvirus.github.io` 时，浏览器地址栏会自动跳转到 `tmd.dkvirus.com`。

在 Hexo 源码 source 目录下新建 CNAME 文件（没有后缀）。输入你自己的域名：`tmd.dkvirus.com`。

有的博文里会让你直接在 Github 里改，事实上 CNAME 文件被提交到 github 上之后，文件内容 `tmd.dkvirus.com` 会被自动填写到这里。使用 CNAME 文件的好处是如果域名有变化，只需要修改本地文件，而不需要登录 github 网站手动修改。

![](https://gitee.com/uploads/images/2018/0320/174201_79251528_1623141.png "屏幕截图.png")

## 测试

上述操作完成后，输入你自己的域名 `tmd.dkvirus.com` 即可看到你的博客。

**注：**有时配置完二级域名测试不行，可能是你的域名被强了，换个域名试试。（:( 别问我为什么知道，我的博客原域名应该是 `blog.dkvirus.com`，被强了才用的 `tmd.dkvirus.com`）
