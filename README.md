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
只渲染出第一个与当前访问地址匹配的`<Route>`或`<Redirect>`
思考如下代码，如果你访问`/about`,那么组件About User Nomatch都将被渲染出来，因为它们对应的路由与访问的地址`/about`匹配。这显然不是我们想要的，我们只想渲染出第一个匹配的路由就可以了，`Switch`由此产生
```jsx
<Route path="/about" component={About}/>
<Route path="/:user" component={User}/>
<Route component={NoMatch}/>
```
也许你会问，为什么 RR4 机制里不默认匹配第一个符合要求的呢，答：这种设计允许我们将多个 <Route> 组合到应用程序中，例如侧边栏（sidebars），面包屑 等等
另外，`<Switch>` 对于转场动画也非常适用，因为被渲染的路由和前一个被渲染的路由处于同一个节点位置！
```
<Fade>
  <Switch>
    {/* 用了Switch 这里每次只匹配一个路由，所有只有一个节点。 */}
    <Route/>
    <Route/>
  </Switch>
</Fade>

<Fade>
  <Route/>
  <Route/>
  {/* 不用 Switch 这里可能就会匹配多个路由了，即便匹配不到，也会返回一个null，使动画计算增加了一些麻烦。 */}
</Fade>
```
#### children: node
`<Switch> `下的子节点只能是 `<Route>` 或 `<Redirect>`元素。只有与当前访问地址匹配的第一个子节点才会被渲染。`<Route>` 元素用它们的 path 属性匹配，`<Redirect> `元素使用它们的 from 属性匹配。如果没有对应的 path 或 from，那么它们将匹配任何当前访问地址。
### Redirect
`<Redirect>`渲染时将导航到一个新地址，这个新地址覆盖在访问历史信息里面的本该访问的那个地址。
#### to:string
重定向的 URL 字符串
#### to: object
重定向的 location 对象
#### push: bool
若为真，重定向操作将会把新地址加入到访问历史记录里面，并且无法回退到前面的页面
#### from: string
需要匹配的将要被重定向路径。
### Prompt
当用户离开当前页面前做出一些提示。
#### message: string
当用户离开当前页面时，设置的提示信息。
```jsx
<Prompt message="确定要离开？" />
```
#### message:func 
当用户离开当前页面时，设置的回调函数
```
<Prompt message={location => (
  `Are you sue you want to go to ${location.pathname}?` 
)} />
```
#### when: bool
通过设置一定条件要决定是否启用 Prompt
## 对象和方法
### history
history是RR4的两大重要依赖之一（另一个当然是React了），在不同的JavaScript环境中，history以多种能够形式实现了对回话（session）历史的管理。
我们会经常使用以下术语：
+ "browser history"-history在DOM上的实现，用于支持HTML5 historyAPI的浏览器
+ "hash history"-history在DOM上的实现，用于旧版浏览器
+ "memory history"-history在内存上的实现，用于测试或非DOM环境（例如React Native）
history对象通常具有以下属性和方法：
+ length：number浏览历史堆栈中的条目数
+ action：string路由跳转到当前执行的动作，分为PUSH，REPLACE，POP
+ location:object当前访问地址信息组成的对象，具有如下属性：
    >  pathname: string URL路径
    search: string URL中的查询字符串
    hash: string URL的 hash 片段
    state: string 例如执行 push(path, state) 操作时，location 的 state 将被提供到堆栈信息里，state 只有在 browser 和 memory history 有效。
    push(path, [state]) 在历史堆栈信息里加入一个新条目。
    replace(path, [state]) 在历史堆栈信息里替换掉当前的条目
    go(n) 将 history 堆栈中的指针向前移动 n。
    goBack() 等同于 go(-1)
    goForward 等同于 go(1)
    block(prompt) 阻止跳转

history 对象是可变的，因为建议从 <Route> 的 prop 里来获取 location，而不是从 history.location 直接获取。这样可以保证 React 在生命周期中的钩子函数正常执行，例如以下代码：
```jsx
class Comp extends React.Component {
  componentWillReceiveProps(nextProps) {
    // locationChanged
    const locationChanged = nextProps.location !== this.props.location

    // 错误方式，locationChanged 永远为 false，因为history 是可变的
    const locationChanged = nextProps.history.location !== this.props.history.location
  }
}

```
### location
location是指你当前的位置，将要去的位置，或是之前所在的位置
```json
{
  key:'hjdh',
  pathname:'/about',
  search:'?name=nini',
  hash:'#dhfj',
  state:{
    price:123
  }
}
```
在以下情境中可以获取 location 对象

- 在 Route component 中，以 this.props.location 获取
- 在 Route render 中，以 ({location}) => () 方式获取
- 在 Route children 中，以 ({location}) => () 方式获取
- 在 withRouter 中，以 this.props.location 的方式获取
location 对象不会发生改变，因此可以在生命周期的回调函数中使用 location 对象来查看当前页面的访问地址是否发生改变。这种技巧在获取远程数据以及使用动画时非常有用
```js
componentWillReceiveProps(nextProps) {
  if (nextProps.location !== this.props.location) {
    // 已经跳转了！
  }
}
```
可以在不同情境中使用 location：

- `<Link to={location} />`
- `<NaviveLink to={location} />`
- `<Redirect to={location />`
- `history.push(location)`
- `history.replace(location)`
### match
match 对象包含了 `<Route path>` 如何与 URL 匹配的信息，具有以下属性：

- params: object 路径参数，通过解析 URL 中的动态部分获得键值对
- isExact: bool 为 true 时，整个 URL 都需要匹配
- path: string 用来匹配的路径模式，用于创建嵌套的 <Route>
- url: string URL 匹配的部分，用于嵌套的 <Link>

在以下情境中可以获取 match 对象

- 在 Route component 中，以 this.props.match获取
- 在 Route render 中，以 ({match}) => () 方式获取
- 在 Route children 中，以 ({match}) => () 方式获取
- 在 withRouter 中，以 this.props.match的方式获取
-matchPath 的返回值

当一个 Route 没有 path 时，它会匹配一切路径。
