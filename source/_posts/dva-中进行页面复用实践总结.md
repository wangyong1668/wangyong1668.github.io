---
title: dva 中进行页面复用实践总结
date: 2017-12-28 20:45:13
categories:
- 前端
tags:
- js框架
- Dva
- 模块化
---

```
目录：
一、要做的事
二、思路
1. 路由改变 pathname —— 菜单数组
2. dva 监听 pathname 变化，并显示与当前 pathname 对应的页面 —— 路由数组
3. 导出菜单数组和路由数组
三、踩坑
1. 引入 npm 包运行报错
2. npm 包进行页面复用注意事项
3. npm 包样式问题
```

> 本文针对有 dva 开发经验的阅读，没实际开发过可能看着有点晕。

# 要做的事

最近经过尝试，发现页面也可以进行复用。如下是最最普通的后台管理系统，左边的一个菜单对应右边的一个页面，该页面内有基本的增删改查功能。现在可以将这样一个页面发布成 npm 包，在其他系统如果也需要这个页面，直接 install 对应的包通过简单配置即可。

![图1：普通后管系统](http://upload-images.jianshu.io/upload_images/6693922-c5c4c27d43268c0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我有这个想法源自我开发过好几个后管系统，每个后管系统都有基础的权限管理功能，也就是 `用户管理`、`角色管理`、`机构管理`。每次我都需要重新开发，哪怕是复制粘贴代码也显示很麻烦。要是其中一个后管系统的权限管理功能有一个地方需要修改，还要去其它后管系统一个个修改。如果将权限管理功能涉及的代码打成 npm 包，每次只需要修改 npm 包对应的那部分代码，之后发布新的版本，用到权限管理菜单的后管系统只需要简单的更新到最新版本即可，这显得很人性化。

dva 可以很方便的做到这一点，下面就介绍 dva 中页面的复用步骤以及注意事项。

# 思路

## 路由改变 pathname —— 菜单数组

> 通过路由改变浏览器地址栏中 pathname。

dva 中使用的路由是 react-router@4.0，其中有个标签是这样的：`<Link to="/user">`，它的目的是将浏览器地址栏中 `pathname` 替换成 `/user`。 

![图2：菜单树](http://upload-images.jianshu.io/upload_images/6693922-65fde6567c6f4bae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于菜单树，每个菜单项都有名称（name），有的菜单项还有图标（icon），菜单有父子关系（parentId）,如果菜单是父级菜单，是没有路由的，点击父级菜单只会打开折叠显示所有子菜单，如果菜单是子菜单，拥有路由属性（router）,点击菜单会改变浏览器地址栏中的 pathname 值。

```
let menus = [
  {
    id: '1',
    name: 'List Page',
    parentId: null,
  },
  {
    id: '11',
    name: 'Table List',
    router: '/table-list',
    parentId: '1',
  },
];
```
通过组装如上的数据结构生成菜单树的 html 代码。如果需要添加新的菜单，只需要追加一个数组即可。数据源变化，左边菜单树会自动新增一项菜单。

```
// ......
const newMenu = [{
    id: '2',
    name: 'Form Page',
    parentId: null,
}]
menus  = menus.concat(newMenu);
```

## dva 监听 pathname 变化并显示对应页面 —— 路由数组

**（1）routes 目录和 models 目录**

使用 `dva-cli` 创建的项目有三个很特别的目录：`models`、`routes`、`services`。

其中 `routes` 目录下的内容是用 react jsx 语法写的 `页面`。也就是图1中点击菜单后右边显示的页面。

`models` 目录提供数据，如右面页面中的表格内容是一个数组，就是由 models 提供给 routes 的，然后 routes 中把这些数据转化为表格。一个 models 对应一个 routes。

**（2）dva 监听 pathname 变化并显示对应页面**

dva 提供一个 API 叫做注册路由表 `app.router(({ history, app }) => RouterConfig)`。

```
import { Router, Route } from 'dva/router';
// 引入 routes 目录里的“页面”
import TableList from './routes/table-list/index.js'
app.router(({ history }) => {
  return (
    <Router history={history}>
      <Route path="/table-list" component={TableList } />
    <Router>
  );
});
```
上述代码意思是当 `pathname` 变成 `/table-list` 时，就显示 `routes` 目录下的 `TableList`  页面。（还记得 `pathname` 在哪里改变吗？上面路由中有介绍）这里涉及两个东东：`path` 和 `页面`，如果页面需要数据，事实上还需要一个 `models`。

实际编码中可以对上述代码稍作修改：

```
import React from 'react';
import dynamic from 'dva/dynamic';
import { routerRedux, Route, Switch, Redirect } from 'dva/router';

const { ConnectedRouter } = routerRedux;

function RouterConfig({ history, app }) {
  const error = dynamic({
    app,
    component: () => import('./routes/system/error/index'),
  });
   
  // >>>>>>>>>>>>>>>路由数组 START <<<<<<<<<<<<<<<
  let routes = [
    {
      path: '/table-list',
      models: () => [import('./models/table-list.index.js')],
      component: () => import('./routes/table-list.index.js'),
    },
  ];
  // >>>>>>>>>>>>>>>路由数组 END <<<<<<<<<<<<<<<

  // 将数据源转换为 <Router > 标签的形式........
  return (
    <ConnectedRouter history={history}>
      <Page>
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
      </Page>
    </ConnectedRouter>
  );
}

export default RouterConfig;
```

这里将 `path` 和 `页面` 也抽离为数组作为数据源，在通过处理转换为 dva 提供的 API。

上述代码中，已经成功将 path（也就是 pathname） 与 routes 目录下的页面和 models 目录下提供数据的容器绑定到了一起。此时通过 `<Link to="/table-list">`将浏览器地址栏 pathname 变换成 `/table-list` 时就可以显示对应页面。

## 导出菜单数组和路由数组

经过上面的分析，一个菜单涉及两部分内容：菜单数组决定如何改变 pathname，路由数组决定监听到 pathname 变化时显示什么页面。

只需要将这两个数组导出，在其它后管系统中追加改变数据源即可实现页面的复用。

在 src 目录下新建文件 `export.js`：

```
const menus = [
   {
    id: '2',
    name: 'Form Page',
    parentId: null,
   }
];

// 注意这里的路径使用相对路径
const routes = [
   {
      path: '/table-list',
      models: () => [import('./models/table-list.index.js')],
      component: () => import('./routes/table-list.index.js'),
    },
];

const baseObj = {
  baseMenus: menus,
  baseRoutes: routes
}

export default baseObj ;
```

修改 package.json 设置入口文件位置：

```
{
  "name": "test-user",
  "main": "src/export.js"
}
```

接着就可以发布 npm 包了。对发布过程不了解的可以跳转 [npm 发布包流程及填坑指南](https://www.jianshu.com/p/01df21c71407)。

在其它后管系统中引用也很简单，将路由数组和菜单数组追加到相应位置即可。

# 踩坑

## 引入 npm 包运行报错

引入 npm 包之后运行会报如下错误。原因是 node_modules 目录下的 npm 包里用到了 es6 的语法，虽然 roadhog 能自动将 es6 代码处理为 es5 ，但这并不包括 node_modules 目录下的代码。

```
You may need an appropriate loader to handle this file type.
| import React from 'react';
| /*
| import { render } from 'react-dom'
```

**解决方法：** 修改 .roadhogrc.js 文件，添加 `extraBabelIncludes` 属性，其中 `test-user` 为你发布的 npm 包名称。该属性意思是让 roadhog 将 `node_modules/test-user` 代码中的 es6 处理为 es5，这样就可以正常启动运行了。

```
export default {
  "entry": "src/index.js",
  "extraBabelIncludes": [
    "node_modules/test-user"
  ],
  // ..........
}
```

## npm 包进行页面复用注意事项

- 所有后管系统前端框架要一样。npm 包将业务模块的代码抽出，这样其实就是将代码位置挪到 npm 包里，然后在当前后管系统中添加一个路径引用而已；
- 测试发现，外层后管系统使用 roadhog 可以配置一些路径别名，npm 包里的代码是可以调用到外层的这些别名的，这意味着 npm 包里的 npm 可以与外层系统公用一份配置文件；
- npm 包里的 Mock 数据没法直接使用。这是因为 roadhog 在启动项目时会自动调用 .roadhogrc.mock.js 文件中的 mock 文件，除非你在该文件中引入 npm 包里的 mock 文件。

## npm 包样式问题

（2018年3月9日更新）最近发现一个比较恶心的问题，打好的 npm 包在其它后管项目中引用，npm 包里如果样式是这么写的。在 index.js 中引入 index.less，会发现 index.less 中的样式不起作用；要是把样式直接在 index.js 以内联的方式 style 发现是可以的。推测原因，外部引入 npm 包，不会执行 .less 文件。

```
- routes
  - user
    - index.js
    - index.less
```






