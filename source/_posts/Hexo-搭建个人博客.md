---
title: Hexo 搭建个人博客
date: 2018-03-20 11:12:11
categories:
- 运维
tags:
- 建站工具
- Hexo
---

![Hexo 搭建 Next 主题博客](https://gitee.com/uploads/images/2018/0320/111942_89857a27_1623141.png "屏幕截图.png")

# 关于 Hexo

**介绍**

Hexo 可以快速搭建个人博客，不需要任何编码技巧，这对于大多数人很友好。

如果您要写博客，只需在计算机上创建文件。window 上一般会创建 .doc 文件、.txt 文件，这里 Hexo 有严格限制，必须创建 .markdown 格式（和 doc、txt 一样都是做文本编辑用的）的文件。将您写好的 markdown 文件放在指定目录下即可在网站里看到发布的新博文。

**搭建环境** 

Npm，即 Node 环境。不熟悉 Npm 的看这里 > [《通俗易懂的 Npm 入门教程》](https://dkvirus.gitbooks.io/-npm/content/chapter1.html)

# 创建博客

通过五步操作完成博客搭建和发布文章。

**第一步：安装 hexo 脚手架**

```
$ npm install -g hexo-cli
```

**第二步：创建博客目录**

```
$ hexo init <folder>
$ cd <folder>
$ npm install
```

`<folder>`：你博客放在哪个目录下面，如：`dkvirus_blog`。

**第三步：创建文章**

在 `source > _posts` 目录下新建文件 `我的第一篇文章.md`。

如果觉得上面手动创建文件太 lower 逼，可以使用下面这行命令也可以。`$ hexo new 我的第一篇文章`，这里的文章标题如果有空格，需要加上引号，如：`$ hexo new "你好 我的祖国"`。

```
$ hexo new 我的第一篇文章
```

**第四步：写文章**

打开 `我的第一篇文章.md` 文件（没有编程经验的同学可能打不开，使用文本编辑器打开，notepad/sublime/vscode），编写内容如下：

```
---
title: 我的第一篇博文
date: 2018-03-20 10:40:48
tags:
---

# 我的博客

这是我的第一篇博客。
```

**第五步：看效果**

```
$ hexo server -p 8000
```

在浏览器中输入 `http://localhost:8000/` 即可看到下面的页面。（默认端口 4000，非常容易和计算机上其它应用程序的端口冲突）

![输入图片说明](https://gitee.com/uploads/images/2018/0320/104439_4bbf667d_1623141.png "屏幕截图.png")

# 更改主题

如果你觉得官方默认的主题不好看，或者现在你压根不知道主题啥啥意思，跟着继续操作，让你的博客改头换面。

**第一步：下载 nexT 主题**

登录 [hexo-next主题官网](https://github.com/iissnan/hexo-theme-next)，按如下截图下载压缩包。

![输入图片说明](https://gitee.com/uploads/images/2018/0320/105401_10de67af_1623141.png "屏幕截图.png")

**第二步：替换主题**

解压缩，将解压后的文件夹放到你的博客目录下的 themes 目录下。并且将 `hexo-theme-next-master` 重命名为 `next`。

打开博客目录下的 `_config.yml` 文件，拉到最下面，修改 theme 的值。

```
# theme: landscape
theme: next
```

**第三步：看效果**

```
$ hexo server -p 8000
```

在浏览器中输入 `http://localhost:8000/` 即可看到不一样的个人博客，只是外观/主题变了，文章还是原来的文章。

**第四步：去主题商店选主题**

Hexo 社区提供了很多主题，上面那个 nexT 主题是目前社区最受欢迎的一款主题，当然你也可以在 [主题商店](https://hexo.io/themes/) 中选择其他主题。

![主题商店](https://gitee.com/uploads/images/2018/0320/110801_e8bd29c5_1623141.png "屏幕截图.png")

点击图片可以预览别人使用这个主题做的网站的效果，如果喜欢的话点击下面的文字进入 github 页面，此时又回到了第一步那个页面。

# 更多

到这一步，已经对 Hexo 搭建个人博客有了大概认识。

Hexo 还提供了很多个性化的东东，此时再去 [Hexo 官网](https://hexo.io/zh-cn/docs/index.html) 看文档，去完善你的博客吧。






