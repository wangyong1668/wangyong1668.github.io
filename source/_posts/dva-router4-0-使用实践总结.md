---
title: dva router4.0 使用实践总结
date: 2017-10-16 20:33:11
categories:
- 前端
tags:
- js框架
- Dva
- 路由
- React-router
---

# 概述

dva 升级到 2.0 版本以后，也将内部使用的 dva/router 从 react-router@3.0 升级到了 react-router@4.0。[react-router@4.0 文档 API](https://reacttraining.com/react-router/web/guides/philosophy)

react-router@4.0 让路由变得更简单，最大特点就是可以路由嵌套，可是如果照搬使用 react-router@4.0 的写法，你会发现在 dva 中是行不通的，查看 dva/router 的源码可以看到：

```
// dva/router.js
module.exports = require('react-router-dom');
module.exports.routerRedux = require('react-router-redux');
```

其中第一行导出的 `react-router-dom` 就是 react-router@4.0 文件，第二行导出的 `react-router-redux` 是 react-router 配合 redux 使用的中间库。因为 dva 中使用到了 redux，所以我们在配置的时候还需要注意到这一点。

由于 dva 将 `react-router-dom` 和 `react-router-redux` 都封装到了 dva/router 中，在使用 react-router@4.0 和 redux 里面的东西时只需引入 dva/router 这个包即可。

# dva 中使用 router4.0

router.js

```
import React from 'react';
import { routerRedux, Route } from 'dva/router';
import Example from 'routes/Example';

const { ConnectedRouter } = routerRedux;

function RouterConfig({ history, app }) {

    return (
        <ConnectedRouter history={history}>
          <Route path="/" component={Example} />
        </ConnectedRouter>
    );

}

export default RouterConfig;
```

说明：
- Route 为 `react-router-dom` 内的标签
- ConnectedRouter 为 `react-router-redux` 内的对象 routerRedux 的标签，作用相当于 `react-router-dom` 中的 BrowserRouter 标签，作用为连接 redux 使用。

# `react-router@4.0` 常用标签总结

英文不错的建议直接去官网读一手文档，对于理解很有帮助。
[react-router@4.0 文档 API](https://reacttraining.com/react-router/web/guides/philosophy) 

以下内容仅为作者在阅读时总结的内容。

## BrowerRouter

```
<BrowserRouter basename="/calendar"/>
<Link to="/today"/> // 渲染为 <a href="/calendar/today">
```

## Route

- route 的三种写法
    - `<Route component>`
    - `<Route render>`
    - `<Route children>`
- route 传给组件的参数
    - match    跟爸爸有关
        - isExact        是否完全匹配
        - params       参数，一般指path中的参数
        - path           <route>中 path 属性的值，与浏览器地址栏 url 进行匹配，“/topics/:topicId”
        - url              Link 或者 a 标签 中跳转的地址，一般情况下为浏览器地址栏中地址，如果有 basename，浏览器地址栏为 basename + url 的值，“/topics/components”
    - location    跟自己有关
        - pathname    与 match 中的 url 属性相同，“/topics/components”    
        - hash: ""        不知所云，换成 hashHistory 也没有值
        - key: ""          随机生成一个6位的字符串，唯一喔
        - search: ""      参数 
    - history            
        - -history对象，可以通过代码控制前进、后退
- <route> 属性
    - path        匹配的路径  “/topics/:topicId”
    - exact        完全匹配 
    - strict        结尾斜线匹配 

## Redirect

redirect 重定向到新页面会在历史记录栈中替换当前页面，也就是点击浏览器中后退按钮，不会从新页面回到当前页面。
- to   值为字符串，跳转到新位置，自执行。
- to   值为对象，最终会转换为一个位置地址。
- push    值为 true 时不会替换当前页面，而是在历史记录栈中新增一条记录。
- from    相当于 <route> 中的 path 属性，匹配 url 地址，匹配成功，跳转到另一个 url 地址。

## Link
- to 值为字符串
    该字符串值会与 <route> 中的 path 属性值进行匹配，如果匹配成功，跳转 <route> 中的 component 属性值。
- to 值为对象
    对象值最终也会转换为 url 值。to 属性相当于 <a> 标签中的 href 属性。
    ```
    <Link to={{
      pathname: '/courses',
      search: '?sort=name',
      hash: '#the-hash',
      state: { fromDashboard: true }
    }}/>
    ```
- replace
    相当于微信小程序中的 redirect 属性
    设置为 true 表示替换 history 中的当前页面，设置为 false 为在 history 中新增一条历史记录。

## switch
- 只会运行其中一个 <route>，
- 子元素只能是 <route> 或者 <redirect>




