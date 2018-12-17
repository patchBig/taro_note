# 生命周期对应关系

## app.js

由于入口文件会包含一个 Component 组件基类，他同样拥有组件生命周期，但因为入口文件的特殊性，它的生命周期并不完整

| 生命周期方法 | 作用 | 说明 |
| :---- | :----| :---- |
| componentWillMount | 程序被载入 | 对应 app 的 onLaunch |
| componentDidMount | 程序被载入 | 对应 app 的 onLaunch，在 componentWillMount 后执行 |
| componentDidShow | 程序展示出来 | 对应 onShow，在 H5 中同样实现 |
| componentDidHide | 程序被隐藏 | 对应 onHide，在 H5 中同样实现 |
| componentDidCatchError | 错误监听函数 | 对应 onError |
| componentDidNotFound | 页面不存在监听函数 | 对应 onPageNotFound |

> 在微信小程序中 onLauch 通常带有一个参数 options, 在 Taro 中可以在所有生命周期和普通事件方法中通过 this.$route.params 访问到，其他端也适用

入口文件需要包含一个 render 方法，一般返回程序的第一个页面，但值得注意的是不要在入口文件中的 render 方法里写逻辑及引用其他页面、组件，因为编译时 render 方法中的内容会被直接替换掉，逻辑代码不会起作用。

## Page

页面 JS 要求必须有一个 render 函数，函数返回 JSX 代码

| 生命周期方法 | 作用 | 说明 |
| :---- | :----| :---- |
| componentWillMount | 页面被载入 | 对应 onLoad |
| componentDidMount | 页面渲染完成 | 对应 onReady |
| shouldComponentUpdate | 页面是否需要更新 | |
| componentWillUpdate | 页面即将更新 | |
| componentDidUpdate | 页面更新完毕 | |
| componentWillUnmount | 页面退出 | 对应 onUnload |
| componentDidShow | 页面展示出来 | 对应 onShow，在 H5 中同样实现 |
| componentDidHide | 页面被隐藏 | 对应 onHide，在 H5 中同样实现 |

> 微信小程序中 onLoad 通常带有一个参数 options，在 Taro 中你可以在所有生命周期和普通事件方法中通过 this.$router.params 访问到，在其他端也适用

## 组件

Taro 的组件同样是继承自 Component 组件基类，与页面类似，组件也必须包含一个 render 函数，返回 JSX 代码。

与页面相比，组件没有自己的 config，同时组件的生命相比页面来说多了一个 **componentWillReceiveProps** ，表示当父组件（或页面）发生更新时将带动子组件进行更新时调用的方法。