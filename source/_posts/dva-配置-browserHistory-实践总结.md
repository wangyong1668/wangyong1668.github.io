---
title: dva 配置 browserHistory 实践总结
date: 2017-10-16 20:35:26
categories:
- 前端
tags:
- js框架
- Dva
---

# 什么是 History

## 概述
history 是历史记录管理。点击 a 标签跳转或者点击浏览器后退按钮，总之页面进行跳转时，history 都会记录这一步操作，方便后面 go、back 等操作。

三种 history：
- hashHistory  老版本浏览器的 history。
- browserHistory   h5的history。现在的 web 项目都使用这种。
- memoryHistory   node环境下的 history，存储在 memory中。

## 如何辨别  history 属于哪一种？

- 浏览器地址看地址url，如果有 #，那么就是 hashHistory了；
    
- 在代码中 'pushState' in window.history 如果为 true 说明是 browserHistory。

    这是因为 browserHistory API 有 pushState, replaceState, popState。保持 ui（页面） 和 url（浏览器地址栏地址） 同步。地址栏地址变化，页面也同步变化。

## browserHistory 为什么会出现，相对 hashHistory 有什么进步？

- hashHistory 连续点击同一个链接会报错

    使用 hashHistory，连续点击一个链接会报错：Warning: Hash history cannot PUSH the same path; a new entry will not be added to the history stack
    but 使用 browserHistory 就不会出现这种情况。连续点击一个链接，第二次依然会有反应，但不会报错。
    
    产生这种差异的原因是机制问题，hashHistory 制作一个栈来存放历史记录，且不允许连续两个历史记录相同。browserHistory 就允许连续两次历史记录相同。
    
    测试：连续点击一个链接四次，再点击浏览器的后退按钮，看 
    hashHistory 与 browserHistory 的差别。

- 还有一个差异：hashHistory 不能很好的支持 basename。
 
## 如何查看 history 中历史记录。

直接查看具体的历史记录只能到浏览器-选项-历史记录中查看。
通过代码的形式查看 window.history 打印出对象有个 length 属性，记录历史记录的条数。点击一条链接之后再打印 window.history 可以查看变化。

补充：浏览器中一个选项卡会单独记录它的历史记录，关闭浏览器选项卡重新打开之后又会从 1 开始计数。

# dva 中如何使用 History

dva 官方文档提供的 api 可自由选择使用哪种 history，但是测试时貌似不行，只能使用 hashHistory，这个 bug 截止 2.0.3 版本还未修复。 

```
import { browserHistory } from 'dva/router';
const app = dva({
  history: browserHistory,
});
```

【替代方法】使用 `history` 这个第三方库。

1. 安装

    ```
    $ npm install history
    ```

2. 修改 index.js 文件

    ```
    import { createBrowserHistory as createHistory } from 'history';
    
    const app = dva({
      history: createHistory()
    });
    ```

通过以上配置就已经使用了 browserHistory 了，如果这里没有感官认识，可以换成 hashHistory 刷新页面观察浏览器地址栏变化。

- 创建 hashHistory：

`import { createHashHistory as createHistory } from 'history';`

- 创建 memoryHistory：

`import { createMemoryHistory  as createHistory } from 'history';`
