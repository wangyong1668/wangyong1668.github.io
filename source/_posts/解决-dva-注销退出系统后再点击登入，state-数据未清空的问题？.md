---
title: 解决 dva 注销退出系统后再点击登入，state 数据未清空的问题？
date: 2018-01-15 20:55:53
categories:
- 前端
tags:
- js框架
- Dva
---

> 使用 dva 开发的后管系统，当退出系统时数据容器 models 中的 state 值并不会清空，造成重新登入系统仍然可以看到上一次登入系统时的数据。下面举例其中一种场景进行说明。
  
# 问题

使用 `admin` 用户登入系统，进入 `用户管理` 菜单。

表格中总共有三条数据：

![表格中总共有三条数据](http://upload-images.jianshu.io/upload_images/6693922-b221c799a0cfb968.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过名称进行过滤查询，查的一条数据：

![用户列表](http://upload-images.jianshu.io/upload_images/6693922-c17e3ca941fde3a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在退出系统，使用 `guest` 用户再次登入系统，进入 `用户管理` 菜单，会发现竟然还是一条数据，也就是之前 `admin` 用户过滤查询之后的数据。

不同用户之间数据竟然无意间进行了共享，这是不被允许的。

# 分析原因

使用 `admin` 用户登入，通过名称过滤查询得到一条数据，该数据被放在一个叫做 `数据容器` （models 目录下的 state 里面）中，只要 `数据容器` 值变了，页面也就相应改变。所以页面由三条数据变成了一条数据。

当 `admin` 用户退出系统时，清空了 cookie，但是并没有清空数据容器 （models > 
 state） 中的值，这就导致 `guest` 用户登入后 `数据容器` （models 目录下的 state 里面）中依然还是那一条数据，所以页面还是之前 `admin` 用户查询的那条数据。

搞清楚这一点，解决这个问题也就只需在退出系统时，手动清空 `数据容器`（models 目录下的 state 里面）中的值即可。

```
// models > app.js
export default {
  namespace: 'app',
  // 数据容器即这里面的属性
  state: {
      userList: [],
  },
  effects: {
    * logout: ({ payload = {} }, { put, call }) {
      // 清空 cookie ，退出到登录页面
    }
  }
}
```

# 解决方法

**实践不可行操作方式**

在 `src > index.js` 中添加 onReducer 钩子函数，监听当触发 `app/logout` 退出系统时，手动清空数据容器中的值。

```
// ..............

const app = dva({
  history: createHistory(),
  onError (error) {
    message.error(error.message);
  },
  // >>>>>>>>>>>>>>>>>>> add start >>>>>>>>>>>>>>>>>>>>>>>>
  onReducer: r => (state, action) => {
    const newState = r(state, action);
    // 'app/logout' 为 models 目录文件中 effect 中的方法名
    if (action.payload && action.payload.actionType === 'app/logout') {
      return { app: newState.app, loading: newState.loading, routing: newState.routing };
    }
    return newState;
  },
  // >>>>>>>>>>>>>>>>>>> add end >>>>>>>>>>>>>>>>>>>>>>>>
});

// ..............
```

对于这种写法不理解的可以手动打印下几个变量的值看看（我也不理解为啥这么写，[dva API_zh-CN.md](https://github.com/dvajs/dva/blob/master/docs/API_zh-CN.md) 里提供的写法）。（**使用 onReducer 钩子函数在实践应用中并不能达到预期效果。**）

**实践可行的操作方式**

想要在注销系统时将 dva 中 state 数据清空，可以刷新页面。每次刷新页面都会重新将 html 以及 js 在浏览器中加载一次，同时运行 app.start() 方法启动 dva 这个步骤。使用 `window.location.pathname` 跳转 `/` 路由（location 方法会刷新页面）。

```
*logout ({ payload }, { call, put }) {
  // 跳转路由，刷新页面，此时 dva 中 state 数据也会在内存中重新刷新
  window.location.pathname = '/';
  message.success('注销成功。');
},
```

在路由文件 router.js 中控制跳转：

```
<LocaleProvider locale={zhCN}>
      <ConnectedRouter history={history}>
        <Switch>
          <Redirect exact from='/' to='/login' />
        </Switch>
      </ConnectedRouter>
    </LocaleProvider>
```




