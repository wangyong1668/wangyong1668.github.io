---
title: dva 中使用 axios 统一拦截错误示例
date: 2018-02-28 20:58:01
categories:
- 前端
tags:
- js框架
- Dva
---

> dva 官网使用的网络请求库是 dva/fetch，个人比较喜欢 axios，因为可以跨域，各种拦截使用起来也很舒服。项目中经常需要对错误的请求进行统一拦截，统一友好的输出错误提示。本例为实践摸索出来的一套方案，无论是在 react 还是 vue 中都可以进行参考。

# 代码示例

先贴代码，下面有各个部分详细解释。

```
// request.js
import axios from 'axios';
import NProgress from 'nprogress';
import { notification, message } from 'antd';
import { routerRedux } from 'dva/router';
import store from '../index';

/**
 * 一、功能：
 * 1. 统一拦截http错误请求码；
 * 2. 统一拦截业务错误代码；
 * 3. 统一设置请求前缀
 * |-- 每个 http 加前缀 baseURL = /api/v1，从配置文件中获取 apiPrefix
 * 4. 配置异步请求过渡状态：显示蓝色加载条表示正在请求中，避免给用户页面假死的不好体验。
 * |-- 使用 NProgress 工具库。
 * 
 * 二、引包：
 * |-- axios：http 请求工具库
 * |-- NProgress：异步请求过度条，在浏览器主体部分顶部显示蓝色小条
 * |-- notification：Antd组件 > 处理错误响应码提示信息
 * |-- routerRedux：dva/router对象，用于路由跳转，错误响应码跳转相应页面
 * |-- store：dva中对象，使用里面的 dispatch 对象，用于触发路由跳转
 */

// 设置全局参数，如响应超市时间，请求前缀等。
axios.defaults.timeout = 5000
axios.defaults.baseURL = '/api/v1';
axios.defaults.withCredentials = true;

// 状态码错误信息
const codeMessage = {
  200: '服务器成功返回请求的数据。',
  201: '新建或修改数据成功。',
  202: '一个请求已经进入后台排队（异步任务）。',
  204: '删除数据成功。',
  400: '发出的请求有错误，服务器没有进行新建或修改数据的操作。',
  401: '用户没有权限（令牌、用户名、密码错误）。',
  403: '用户得到授权，但是访问是被禁止的。',
  404: '发出的请求针对的是不存在的记录，服务器没有进行操作。',
  406: '请求的格式不可得。',
  410: '请求的资源被永久删除，且不会再得到的。',
  422: '当创建一个对象时，发生一个验证错误。',
  500: '服务器发生错误，请检查服务器。',
  502: '网关错误。',
  503: '服务不可用，服务器暂时过载或维护。',
  504: '网关超时。',
};

// 添加一个请求拦截器，用于设置请求过渡状态
axios.interceptors.request.use((config) => {
  // 请求开始，蓝色过渡滚动条开始出现
  NProgress.start();
  return config;
}, (error) => {
  return Promise.reject(error);
});

// 添加一个返回拦截器
axios.interceptors.response.use((response) => {
  // 请求结束，蓝色过渡滚动条消失
  NProgress.done();
  return response;
}, (error) => {
  // 请求结束，蓝色过渡滚动条消失
  // 即使出现异常，也要调用关闭方法，否则一直处于加载状态很奇怪
  NProgress.done();
  return Promise.reject(error);
});

export default function request (opt) {
  // 调用 axios api，统一拦截
  return axios(opt)
    .then((response) => 
      // >>>>>>>>>>>>>> 请求成功 <<<<<<<<<<<<<<
      console.log(`【${opt.method} ${opt.url}】请求成功，响应数据：%o`, response);

      // 打印业务错误提示
      if (response.data && response.data.code != '0000') {
        message.error(response.data.message);
      }

      return { ...response.data };
    })
    .catch((error) => {
      // >>>>>>>>>>>>>> 请求失败 <<<<<<<<<<<<<<
      // 请求配置发生的错误
      if (!error.response) {
        return console.log('Error', error.message);
      }

      // 响应时状态码处理 
      const status = error.response.status;
      const errortext = codeMessage[status] || error.response.statusText;
      
      notification.error({
        message: `请求错误 ${status}`,
        description: errortext,
      });
      
      // 存在请求，但是服务器的返回一个状态码，它们都在2xx之外
      const { dispatch } = store;

      if (status === 401) {
        dispatch(routerRedux.push('/user/login'));
      } else if (status === 403) {
        dispatch(routerRedux.push('/exception/403'));
      } else if (status <= 504 && status >= 500) {
        dispatch(routerRedux.push('/exception/500'));
      } else if (status >= 404 && status < 422) {
        dispatch(routerRedux.push('/exception/404'));
      }

      // 开发时使用，上线时删除
      console.log(`【${opt.method} ${opt.url}】请求失败，响应数据：%o`, error.response);

      return { code: status, message: errortext }; 
    });
}
```

# 明确响应体

以微信小程序为例，请求响应数据分为两部分：

- 网络请求是否成功；
- 业务场景值。即便网络请求成功了，业务处理上可能有时也会出错，比如校验不通过。

我们在拦截响应时要分别对这两部分进行处理。

```
response = {
  status: 200,            // 网络请求状态。
  statusText: 'xxx',
  data: {
    code: '1001',         // 业务请求状态。这里 '0000' 表示业务没问题，其它都有问题
    message: 'yyy',
    data: {  },
  }
}
```

# 依赖包分析

```
import axios from 'axios';
import NProgress from 'nprogress';
import { notification, message } from 'antd';
import { routerRedux } from 'dva/router';
import store from '../index';
```

上面几个都是比较常见的包，最后一个 `import store from '../index';` 可能有些同学会比较陌生，这是 dva 中导出的对象。即下面代码最终导出的 app._store，引入它是因为 `dispatch` 对象在里面，我们需要 `dispatch` 对象进行路由跳转。
 
```
// index.js
import dva from 'dva';
import { message } from 'antd';
import { createBrowserHistory as createHistory } from 'history';

// 1. Initialize
const app = dva({
  history: createHistory(),
});

// 2. Plugins
app.use(createLoading());

// 3. Model
app.model(require('./models/app/global').default);

// 4. Router
app.router(require('./router').default);

// 5. Start
app.start('#root');

export default app._store;
```
# axios 全局配置

```
// 设置全局参数，如响应超市时间，请求前缀等。
axios.defaults.timeout = 5000
axios.defaults.baseURL = '/api/v1';
axios.defaults.withCredentials = true;
```

axios 可以设置很多全局配置，具体可参阅：[更多](https://segmentfault.com/a/1190000008470355)。

# 加载 `NProgress` 过渡组件

```
// 添加一个请求拦截器，用于设置请求过渡状态
axios.interceptors.request.use((config) => {
  // 请求开始，蓝色过渡滚动条开始出现
  NProgress.start();
  return config;
}, (error) => {
  return Promise.reject(error);
});

// 添加一个返回拦截器
axios.interceptors.response.use((response) => {
  // 请求结束，蓝色过渡滚动条消失
  NProgress.done();
  return response;
}, (error) => {
  // 请求结束，蓝色过渡滚动条消失
  // 即使出现异常，也要调用关闭方法，否则一直处于加载状态很奇怪
  NProgress.done();
  return Promise.reject(error);
});
```

NProgress 的使用主要有两个方法，当调用 NProgress.start(); 时在浏览器顶部就会出现蓝色小条，当调用 NProgress.done(); 蓝色小条就会消失。我们分别在请求开始和接收到响应调用这两个方法。

![nprogress](http://upload-images.jianshu.io/upload_images/6693922-948c3efcfeeaf4fd.gif?imageMogr2/auto-orient/strip "nprogress")

# 网络请求成功处理

```
.then((response) => 
      // >>>>>>>>>>>>>> 请求成功 <<<<<<<<<<<<<<
      console.log(`【${opt.method} ${opt.url}】请求成功，响应数据：%o`, response);

      // 打印业务错误提示
      if (response.data && response.data.code != '0000') {
        message.error(response.data.message);
      }

      return { ...response.data };
    })
```

网络请求状态码为 200-300 表示成功，此时还应该判断业务处理是否成功。这个根据具体项目具体规定，比如微信小程序有一套场景值。在实际项目中可以自行规定 code = '0000' 业务处理完全没问题，code = '1111' 校验不通过，code = '2222' 数据库出错等等。

最后别忘了要返回具体对象 `{ ...response.data }`。

![微信小程序场景值](http://upload-images.jianshu.io/upload_images/6693922-543298e2782d2842.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 网络请求失败处理

```
// 状态码错误信息
const codeMessage = {
  200: '服务器成功返回请求的数据。',
  201: '新建或修改数据成功。',
  202: '一个请求已经进入后台排队（异步任务）。',
  204: '删除数据成功。',
  400: '发出的请求有错误，服务器没有进行新建或修改数据的操作。',
  401: '用户没有权限（令牌、用户名、密码错误）。',
  403: '用户得到授权，但是访问是被禁止的。',
  404: '发出的请求针对的是不存在的记录，服务器没有进行操作。',
  406: '请求的格式不可得。',
  410: '请求的资源被永久删除，且不会再得到的。',
  422: '当创建一个对象时，发生一个验证错误。',
  500: '服务器发生错误，请检查服务器。',
  502: '网关错误。',
  503: '服务不可用，服务器暂时过载或维护。',
  504: '网关超时。',
};

// ...........

.catch((error) => {
      // >>>>>>>>>>>>>> 请求失败 <<<<<<<<<<<<<<
      // 请求配置发生的错误
      if (!error.response) {
        return console.log('Error', error.message);
      }

      // 响应时状态码处理 
      const status = error.response.status;
      const errortext = codeMessage[status] || error.response.statusText;
      
      notification.error({
        message: `请求错误 ${status}`,
        description: errortext,
      });
      
      // 存在请求，但是服务器的返回一个状态码，它们都在2xx之外
      const { dispatch } = store;

      if (status === 401) {
        dispatch(routerRedux.push('/user/login'));
      } else if (status === 403) {
        dispatch(routerRedux.push('/exception/403'));
      } else if (status <= 504 && status >= 500) {
        dispatch(routerRedux.push('/exception/500'));
      } else if (status >= 404 && status < 422) {
        dispatch(routerRedux.push('/exception/404'));
      }

      // 开发时使用，上线时删除
      console.log(`【${opt.method} ${opt.url}】请求失败，响应数据：%o`, error.response);

      return { code: status, message: errortext }; 
    });
```

网络请求失败，首先需要根据 status 打印提示消息，告诉用户为什么请求失败。如响应码为 401，那么提示用户的文字就会是 `用户没有权限（令牌、用户名、密码错误）。`。

对于常见的几个 status，要进行相应路由操作，跳转到不同页面以友好的方式提示用户。这些错误页面参考 [Ant Design Pro](https://preview.pro.ant.design/#/exception/403)。

如果是 401 错误，表示用户没有权限访问或者用户名密码输入错误，应该跳转到登录页面：`dispatch(routerRedux.push('/user/login'));`。

![403：无权访问](http://upload-images.jianshu.io/upload_images/6693922-ab36fdc7384933bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![404：访问页面不存在](http://upload-images.jianshu.io/upload_images/6693922-18adaa10dc767875.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![500：服务器错误](http://upload-images.jianshu.io/upload_images/6693922-0c6552f8fa086af7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)







