# react-router4.0
初探 React Router 4.0
+ `react-router` 核心
+ `react-router-dom` 用于DOM绑定的React Router
+ `react-router-native` 用于React Native 的react router
+ `react-router-redux` React Router和Redux的集成
+ `react-router-config` 静态路由配置小助手
## 引用
只需引用react-router-dom包即可，相较于react-router，多了`<Link>`和`<BrowserRouter>`之类的DOM类组件
如果需搭配redux,还需要使用react-router-redux
## 组件
### `<BrowserRouter>`
此组件拥有以下属性:
##### basename:string
作用：为所有位置添加一个基准URL
使用场景：假如你需要把页面部署到服务器的二级目录，你可以使用basename设置到此目录
```jsx
<BrowserRouter basename="/react" />
<Link to="/user" />  //最终渲染为<a href="/react/user">
```
##### getUserConfirmation:func
作用：导航到此页面前执行的函数，默认使用window.confirm
使用场景：当需要用户进入页面前执行什么操作时可用，不过一般用到的不多
```jsx
const getConfirmation = (message, callback) => {
    const allowTransition = window.confirm(message)
    callback(allowTransition)
}
<BrowserRouter getUserConfirmation={getConfirmation('hello?',yourCallBack)}>
```

