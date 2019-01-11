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
#### children：func
有时候你可能只想知道访问地址是否被匹配，然后改变下别的东西，而不仅仅是对应的页面。
```jsx
<ul>
  <ListItemLink to="/somewhere" />
  <ListItemLink to="/somewhere-ele" />
</ul>

//不太明白。。
const ListItemLink = ({to,...rest}) => (
  <Route path={to} children={({match}) =>(
    <li calssName={match ? 'active':''}>
      <Link to={to} {...rest}/>>
    </li>
  )}/>
)
```
#### path:string
任何可以被 path-to-regexp解析的有效 URL 路径
```jsx
<Route path="/users/:id" component={User} />
```
#### exact: bool
如果为 true，path 为 '/one' 的路由将不能匹配 '/one/two'，反之，亦然。
#### strict: bool
对路径末尾斜杠的匹配。如果为 true。path 为 '/one/' 将不能匹配 '/one' 但可以匹配 '/one/two'。
### `<Link>`
#### to: string
作用：跳转到指定路径
使用场景：如果只是单纯的跳转就直接用字符串形式的路径。
```jsx
<Link to="/courses" />
```
#### to: object
作用：携带参数跳转到指定路径
作用场景：比如你点击的这个链接将要跳转的页面需要展示此链接对应的内容，又比如这是个支付跳转，需要把商品的价格等信息传递过去。
```jsx
<Link to={{
  pathname: '/course',
  search: '?sort=name',
  state: { price: 18 }
}} />
```
###  `<NavLink>`
这是 <Link> 的特殊版，顾名思义这就是为页面导航准备的。因为导航需要有 “激活状态”。
#### activeClassName: string
导航选中激活时候应用的样式名，默认样式名为 active
```jsx
<NavLink
  to="/about"
  activeClassName="selected"
>MyBlog</NavLink>
```
#### activeStyle: object
如果不想使用样式名就直接写style
```jsx
<NavLink
  to="/about"
  activeStyle={{ color: 'green', fontWeight: 'bold' }}
>MyBlog</NavLink>
```
#### exact: bool
若为 true，只有当访问地址严格匹配时激活样式才会应用
#### strict: bool
若为 true，只有当访问地址后缀斜杠严格匹配（有或无）时激活样式才会应用
#### isActive: func
决定导航是否激活，或者在导航激活时候做点别的事情。不管怎样，它不能决定对应页面是否可以渲染。
### `<Switch>`


