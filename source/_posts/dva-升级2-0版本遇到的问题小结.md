---
title: dva 升级2.0版本遇到的问题小结
date: 2017-10-13 20:29:53
categories:
- 前端
tags:
- js框架
- Dva
---

# 使用 browserHistory 
[dva 配置 browserHistory 实践总结](http://www.jianshu.com/p/2e9e45e9a880)

# 在 router.js 中配置动态加载。

```
import dynamic from 'dva/dynamic';
const UserPageComponent = dynamic({
  app,
  models: () => [import('./models/users'),],
  component: () => import('./routes/UserPage'),
});
```
注：app.router() 方法中参数为  (history, app)
    
# 处理全局错误
```
import { message } from 'antd';
const app = dva({
  onError (error) {
    message.error(error.message);
  },
});
```

# 设置别名

通过工具包将相对路径写成绝对路径。并且还要处理 eslint 的校验。

安装  babel-plugin-module-resolver 包

```
npm install babel-plugin-module-resolver --save-dev
```

在 .roadhog 文件中配置别名

```
"extraBabelPlugins": [
    ["module-resolver", {
      "alias": {
        "routes": `${__dirname}/src/routes`,
        "models": `${__dirname}/src/models`,
        "services": `${__dirname}/src/services`,
        "utils": `${__dirname}/src/assets/utils`
      }
    }]
],
```

如果 eslint 不能通过，在 .eslintrc 文件中添加规则
```
"rules": {
    "import/no-unresolved": 0,
}
```

实践中发现：只有写在文件顶部的 import 中的路径可以使用路径别名。在 js 方法里的 import 无法使用别名。

# 使用 dll 加速启动

- 默认关闭
- 通过 .roadhogrc 的 dllPlugin 配置开启，可以配 exclude 和 include 的包
    
    ```
    dllPlugin: {
        exclude: ["babel-runtime", "roadhog", "cross-env"],
        include: ["dva/router", "dva/saga", "dva/fetch"]
    }
    ```
    
- 提供 roadhog buildDll 命令手动打 dll bundle
- 开启 dllPlugin 后，roadhog server 时检到没有 manifest.json，提醒执行 roadhog buildDll
    ```
    D:\code\dva\dva-demo>npm run dev
    
    > @ dev D:\code\dva\dva-demo
    > cross-env BROWSER=none HOST=0.0.0.0 roadhog server
    
    Failed to parse .roadhogrc config.
    
    Error in .roadhogrc
    Syntax error: Unexpected token 'd' , (12:3)
    
    dllPlugin: {
    ```
    在 .roadhogrc 中使用 dllPlugin 属性会报错，可以重命名为 .roadhogrc.js 文件，使用 export default 导出对象就不会报错了。
    
    在 package.json 中给 script 对象添加一条属性
    
    ```
    "scripts": {
        "build:dll": "roadhog buildDll"
    },
    ```
    
- 然后用户还需手动在调试的 html 里引入 roadhog.dll.js（后面可以考虑用 HtmlWebpackPlugin 生成）

    实际测试，roadhog@1.2 不需要手动引入 roadhog.dll.js，在运行 npm run dev 时会自动下载。

注：road-dlls 还没有发布成 npm 包，无法通过 npm 进行下载。

能正常启动，但是浏览器中页面报错：roadhog is not define.

# 使用 cross-env 启动

package.json 文件中进行配置，使用 cross-env，在 linux/windows/mac 系统都可以运行 roadhog server。

```
"scripts": {
    "dev": "cross-env BROWSER=none HOST=0.0.0.0 roadhog server"
},
```

# 配置代理

在 .roadhogrc.js 中添加配置属性

```
"proxy": {
    "/api/v1": {
        "target": "http://localhost:8080/api",
        "changeOrigin": true,
        "pathRewrite": { "^/api/v1" : "/v1" }
    }
},
```
# dva-loading 处理页面过渡效果
[dva-loading 实践用法](http://www.jianshu.com/p/61fe7a57fad4)

# dva 中使用 react-router 4.0 实践总结
[dva 中使用 react-router 4.0 实践总结](http://www.jianshu.com/p/c5ec9ffa29be)

# dva 2.0中如何使用代码进行路由跳转
[dva 2.0中如何使用代码进行路由跳转](http://www.jianshu.com/p/7de59752b8a8)