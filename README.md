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
#### basename:string
作用：为所有位置添加一个基准URL
使用场景：假如你需要把页面部署到服务器的二级目录，你可以使用basename设置到此目录
```jsx
<BrowserRouter basename="/react" />
<Link to="/user" />  //最终渲染为<a href="/react/user">
```
#### getUserConfirmation:func
作用：导航到此页面前执行的函数，默认使用window.confirm
使用场景：当需要用户进入页面前执行什么操作时可用，不过一般用到的不多
```jsx
const getConfirmation = (message, callback) => {
    const allowTransition = window.confirm(message)
    callback(allowTransition)
}
<BrowserRouter getUserConfirmation={getConfirmation('hello?',yourCallBack)}/>
```
#### forceRefresh:bool
作用：当浏览器不支持HTML5的history API时强制刷新页面
使用场景：同上
```jsx
const supporthistory = 'pushState' in window.history
<BrowserRouter forceRefresh={!supportHistory}/>
```
#### keyLength：number
作用：设置它里面路由的location.key的长度，默认为6。(key的作用：点击同一个链接时，每次该路由下的location.key都会改变，可以通过key的变化来舒心页面)
使用场景：按需设置
```jsx
<Browser keyLength={12}/>
```
#### children:node
作用：渲染唯一子元素
使用场景：作为一个react组件，天生自带children属性

---
### `<Route>`
<Route>是很重要的组件，当页面的访问地址与Route上的path匹配时，就渲染出对应的UI界面
<Route>自带三个render method和三个props
render methods分别是：
+ `<Route component>`
+ `<Route render>`
+ `<Route children>`
props 分别是：
+ match
+ location
+ history
#### component
只有当访问地址和路由匹配时，一个React component才会被渲染，此时此组件接受Route props(match,location,history)
当使用<font color=red>component</font>时，router将使用<font color=red>React.createElement</font>根据给定的component创建一个新的React元素，这意味着如果你使用内联函数（inline function）传值给<font color=red>component</font>将会产生不必要的重复装载。对于内联渲染(inline rendering),建议使用<font color=red>render</font> prop
```jsx
<Route path="/user/:username" component={User} />
const User = ({ match }) => {
  return <h1>Hello {match.params.username}!</h1>
}
```
#### render: func
此方法适用于内联渲染，而且不会产生上文说的重复装载问题。
```jsx
// 内联渲染
<Route path="/home" render={() => <h1>Home</h1} />

// 包装 组合
const FadingRoute = ({ component: Component, ...rest }) => (
  <Route {...rest} render={props => (
    <FadeIn>
      <Component {...props} />
    </FaseIn>
  )} />
)

```