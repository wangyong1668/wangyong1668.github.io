---
title: dva-loading 实践用法
date: 2017-10-16 20:37:54
categories:
- 前端
tags:
- js框架
- Dva
---

dva 中页面过渡效果封装的很好，下面介绍常用的两个 js 库。

> 之前对 dva-loading 理解存在误区，认为只要在 index.js 中配置一下就没事了，事实上 dva-loading 只是提供当前异步加载方法的状态（异步加载中状态为 true，异步加载完成状态为 false），对应加载样式由各自组件自己控制，如：Antd 中 Table 组件自身的 loading 属性。并添加完整流程示例代码。

# 过渡组件 dva-loading

## 传统做法

比如请求一个用户页面，刚进去的时候由于要去服务器请求数据需要花费时间，这段时间页面应该是不能点击的状态。

如果不使用这个组件，传统做法是写个蒙版组件，提供两个方法 start() 和 end()，当进行 ajax 请求开始时调用 start() 方法给整个页面加上一层蒙版，此时不能进行操作，在请求结束也就是 ajax 的 success 回调函数中调用 end() 方法关闭蒙版，表明数据已经请求到了，可以操作页面。

## 作用

该组件有两处亮点：

- 一次配置，其它自动。只需简单配置，无需手动写蒙版组件，无需关心什么时候开始请求数据调用 start()，什么时候数据请求完毕调用 end()；
- 在请求数据期间会产生一层蒙版，此时无法操作页面；

## 配置

dva 项目的 index.js 文件：

```
import createLoading from 'dva-loading';

const app = dva();

app.use(createLoading());
```

配置完成后，在任何一个 dva 的 routes 组件中就都会有一个 loading 对象，如果你对 dva 稍有了解的话，应该不难知道它在哪。比如下面这行代码中的 loading 对象就是由于上面的配置。

```
export default connect(({ app, loading }) => ({ app, loading }))(App);
```

打印一下 loading 对象，可看到内容如下：

```
loading: {
  global: false,
  models: {app: false},
  effects: {app: false}
}
```

我们只需要关注 loading.global 属性，当页面处于异步加载状态时该值为 true，当页面加载完成时，自动监听该值为 false。

## 怎么用？

使用 Antd 的 [Table 组件](https://ant.design/components/table-cn/) 时，查阅 API 可以看到有个 loading 的属性。如果该属性值为 true，Table 组件自身会显示加载效果，该值为 false，加载效果消失。可以通过 loading 对象判断当前是否有异步加载。具体示例代码如下：

```
// src > models >user.js
export default {
  namespace: 'user',
  state: {
    userList: [],      // 存放用户列表
  },
  effects: {
    * query ({ payload = {} }, { call, put }) {
      // 获取用户列表，赋值给 userList
      // 使用 axios 或者 ajax 请求后台返回数据
      const result = axios.request('xxx/xxx');
      // 调用 reducers 中的 updateState 方法改变 state 中 userList 的值
      yield put({ type: 'updateState', payload: { userList: result.data });
    }
  },
  reducers: {
    updateState (state, { payload }) {
      return { ...state, ...payload };
    },
  },
}
```

``` 
// src > routes > user.js
import React from 'react';
import { connect } from 'dva';
import { Table } from 'antd';

const User = ({ dispatch, user, loading }) => {
  console.log(loading)  
  /**
    控制台打印值：
    loading: {
      global: true,
      effects: { 'user/query': true },
      models: { 'user/query', true },
    }
    'user/query' 是个异步请求后台的方法，在请求过程中，global 和 effects 中值都为 true，
    当请求结束，已经有数据返回，userList 中获取到用户列表时该值为 false。
    可能有个疑问：既然 global 可以展示异步加载是否完成，为什么还要 effects 属性，这是因为一个页面中
    可能同时有多个异步加载，只要有一个异步加载没有完成，global 都是 true，但是 effects 中加载完成的
    异步方法都会变成 false，只有没加载完成的异步方法也会是 true，毕竟实际业务场景可能会复杂点。
  */
  
  /** 
    根据 loading.effects 对象判断当前异步加载是否完成，并将该值传递给 Table 组件的 loading 属性，
    Table 组件会自己控制加载样式。dva-loading 在这里的作用只是提供异步加载的状态，
    具体加载样式由对应组件自己提供。
  */
  const isLoading = loading.effects['user/query']
  const { userList } = user

  return (
    <Table
      dataSource={userList}
      loading={isLoading}
      rowKey={record => record.id}
    />
  );
}

export default connect(({ user, loading }) => ({ user, loading }))(User);
```

注：如果还有疑问，可下方留言。

# 动画组件 nprogress

## 安装

```
$ npm install nprogress 
```

## 作用

制作页面加载时动态页面，在页面顶端提供动态进度条，表明当前页面正在加载状态。

![nprogress](http://upload-images.jianshu.io/upload_images/6693922-948c3efcfeeaf4fd.gif?imageMogr2/auto-orient/strip "nprogress")

## 用法

xx.js 中

```
import NProgress from 'nprogress';
```

提供了两个方法：NProgress.start() 和 NProgress.done()。

在刚开始请求（可以认为是 ajax 请求）页面数据时调用 NProgress.start() 方法，此时页面顶端会有蓝色动态进度条；在页面请求数据完毕时（可以认为是 ajax 的 success 回调函数），调用 NProgress.done() 方法，此时蓝色进度条会瞬间加载 100% 然后消失。

# dva-loading 和 nprogress 配合使用

index.js

```
import createLoading from 'dva-loading';

const app = dva();

app.use(createLoading());
```

app.js 

```
import React from 'react';
import { connect } from 'dva';
import NProgress from 'nprogress';

const App= ({ app, loading }) => {

    let currHref = '';
    const href = window.location.href;    // 浏览器地址栏中地址
    if (currHref !== href) {  // currHref 和 href 不一致时说明进行了页面跳转
        NProgress.start();    // 页面开始加载时调用 start 方法
        if (!loading.global) {  // loading.global 为 false 时表示加载完毕
            NProgress.done();  // 页面请求完毕时调用 done 方法
            currHref = href;   // 将新页面的 href 值赋值给 currHref
        }
    }
        
}

export default withRouter(connect(({ app, loading }) => ({ app, loading }))(App));
```