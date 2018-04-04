---
title: Webpack 学习总结
date: 2017-10-17 20:20:09
categories:
- 前端
tags:
- 打包工具
- Webpack
---

![webpack.png](http://upload-images.jianshu.io/upload_images/6693922-04119b4653cb318e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# webpack 干嘛用的？

index.js 依赖 a.js，a.js 依赖 b.js，b.js 依赖 c.js，以此类推。但是，index.js 只依赖 a.js 中的某个方法，传统打包会将 index.js 和 a.js 都打包在一起，这造成了资源的浪费。我们的期望是打包结果为 index.js + a.js 中被用到的那个方法，而 a.js 中没有用到的方法就不应该被打包到。

**优化打包大小**。webpack 会根据入口文件 index.js 找这种依赖关系，生成递归图表，并且只打包需要的的部分。

它的配置文件及其复杂，但是核心就四个概念。

- Entry
- Output
- Loaders
- Plugins

下面会概述这四个核心概念，至于更为具体的配置，参照各自对应详细介绍。

# Entry（入口文件）

配置如下，得告诉 webpack 入口文件是啥。

```
module.exports = {
  entry: './path/to/my/entry/file.js'
};
```

# Output（出口）

webpack 打包好了，得告诉它输出在什么目录下 `output.path`，打包后的文件叫啥名字 `output.filename`。

```
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js'
  }
};
```

# Loaders（加载器）

webapck 本身只能处理 .js 文件，但实际应用中还有其它静态资源如：`.css`、`.less`、`.jpg` 等。这些文件内也存在打包资源浪费问题。加载器 Loaders 做的事情就时将 .js 文件之外的其它文件转换为 webpack 能够识别的 `模块`（使用 import 或者 require() 引入的东东，你是否看到过 import styles from './index.less'），这样就可以使用 webpack 进行打包这些资源了。

    const path = require('path');

    const config = {
    entry: './path/to/my/entry/file.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'my-first-webpack.bundle.js'
    },
    module: {
        rules: [
        { test: /\.txt$/, use: 'raw-loader' }
        ]
    }
    };

    module.exports = config;


module.rules 中有两个属性：

**test** 告诉 webpack 哪些文件类型想要转换为模块；
**use** 使用啥加载器来转换这些文件类型为 webpack 能识别的模块。

# Plugins（插件）

webpack 作为打包器的功能上面基本介绍完了。

但是搞一个东东，人说要是还有这个功能就好了，人又说还有那个功能就好了，webpack 维护团长肯定想要做大做强，让 webpack 无所不能，于是透露出一个接口，用户（开发人员）可以自己开发插件，实现各种奇葩的业务需求。

比如：**压缩代码 UglifyJsPlugin 插件**。想想这个功能确实有用，索然打包只打包用到的代码可以减小打包大小，但是如果再进行一次压缩，那么打包大小会更小，这在 web 端可能没啥影响，但在移动端，就手机那丢丢内存，能省一点是一点，确实是有用的。

```
const HtmlWebpackPlugin = require('html-webpack-plugin'); //installed via npm
const webpack = require('webpack'); //to access built-in plugins
const path = require('path');

const config = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new webpack.optimize.UglifyJsPlugin(),
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```













