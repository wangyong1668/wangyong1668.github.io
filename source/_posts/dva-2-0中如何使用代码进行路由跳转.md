---
title: dva 2.0中如何使用代码进行路由跳转
date: 2017-12-06 20:42:06
categories:
- 前端
tags:
- js框架
- Dva
- 路由
---

# 概述

在上篇 [dva router4.0 使用实践总结](http://www.jianshu.com/p/c5ec9ffa29be) 中主要介绍了 dva 中使用 react-router@4.0 标签写法控制路由。如下：

```
        <Switch>
          <Route path="/" exact render={() => (<Redirect to="/dashboard" />)} />
          {
            routes.map(({ path, ...dynamics }, key) => (
              <Route
                exact
                key={key}
                path={path}
                component={dynamic({ app, ...dynamics })}
              />
            ))
          }
          <Route component={error} />
        </Switch>
```

但是实际开发中，我们可能会需要在代码中进行路由的跳转。本文主要介绍在 dva 中使用代码进行路由跳转以及参数传递。

# 理清关系
dva 升级到 2.0 版本以后，也将内部使用的 dva/router 从 react-router@3.0 升级到了 react-router@4.0。[react-router@4.0 文档 API](https://reacttraining.com/react-router/web/guides/philosophy)

react-router@4.0 让路由变得更简单，最大特点就是可以路由嵌套，可是如果照搬使用 react-router@4.0 的写法，你会发现在 dva 中是行不通的，查看 dva/router 的源码可以看到：

```
// dva/router.js
module.exports = require('react-router-dom');
module.exports.routerRedux = require('react-router-redux');
```

其中第一行导出的`react-router-dom`就是 react-router@4.0 文件，第二行导出的`react-router-redux`是 react-router 配合 redux 使用的中间库。因为 dva 中使用到了 redux，所以我们在配置的时候还需要注意到这一点。

由于 dva 将`react-router-dom`和`react-router-redux`都封装到了 dva/router 中，在使用 react-router@4.0 和 redux 里面的东西时只需引入 dva/router 这个包即可。

# 路由跳转

引入 `dva/router`，使用 routerReux 对象的 push 方法控制，值为要跳转的路由地址，与根目录下 router.js 中配置的路由地址是相同的。routerReux  就是上面 `dva/router` 第二个导出的 `react-router-redux` 包对象。

此处示例为跳转到 `/user` 路由。

```
// models > app.js
import { routerRedux } from 'dva/router';

export default {
  // ...
  effects: {
      // 路由跳转
      * redirect ({ payload }, { put }) {
        yield put(routerRedux.push('/user'));
      },
  }
  // ...
}
```

# 携带参数

有时路由的跳转还需要携带参数。

## 传参：

routerRedux.push 方法的第二个参数填写参数对象。此处示例表示跳转到 `/user` 路由，并携带参数 `{name: 'dkvirus', age: 20}`。

```
// models > app.js
import { routerRedux } from 'dva/router';

export default {
  // ...
  effects: {
      // 路由跳转
      * redirect ({ payload }, { put }) {
        yield put(routerRedux.push('/user', {name: 'dkvirus', age: 20}));
      },
  }
  // ...
}
```

## 接收参数：

```
// models > user.js
export default {
  subscriptions: {
    /**
     * 监听浏览器地址，当跳转到 /user 时进入该方法
     * @param dispatch 触发器，用于触发 effects 中的 query 方法
     * @param history 浏览器历史记录，主要用到它的 location 属性以获取地址栏地址
     */
    setup ({ dispatch, history }) {
      history.listen((location) => {
        console.log('location is: %o', location);
        console.log('重定向接收参数：%o', location.state)
        // 调用 effects 属性中的 query 方法，并将 location.state 作为参数传递 
        dispatch({
          type: 'query',
          payload: location.state,
        })
      });
    },
  },
  effects: {
    *query ({ payload }, { call, put }) {
       console.log('payload is: %o', payload);
    }
  }
  // ...
}
```
在 user.js 中 subscriptions 属性会监听路由。当 app.js 中通过代码跳转到 `/user` 路由，models>user.js>subscriptions 属性中的 setup 方法会被触发，location 记录着相关信息。打印如下。

```
location is: Object
    hash: ""
    key: "kss7as"
    pathname: "/user"
    search: ""
    state: {name: "bob", age: 21}
重定向接收参数：Object
    age:21
    name:"bob"
```

可以看到 `location.state` 就是传递过来的参数。在 subscriptions 中可以使用 dispatch 触发 effects 中的方法同时传递参数。

需要注意的事，在 dva@1.* 版本中，要获取对象还要用 `location.query` 对象，而到了 dva@2.* 就变成了 `location.state` 对象。



