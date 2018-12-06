---
title: ReactRouter教程及API
date: 2017-08-06 09:54:53
tags: React
categories: 前端学习
---
> 往往React需要与React-router搭配使用，实现前端路由功能。好比vue与VueRouter的搭配，下面主要介绍ReactRouter的使用与主要API用法。[React-router文档地址](https://reacttraining.com/react-router/web/guides/quick-start)
<!--more-->
## 目录
- [安装使用](#安装使用)
- [Router对应组件](#Router对应组件)
- [Router的代码拆分](#Router的代码拆分)
- [集成Redux](#集成Redux)

## 安装使用
> 通常在安装时，会看到有的项目会同时安装有`react-router`和`react-router-dom`，其实只要安装`react-router-dom`就好了，react-router包含的是react-router的核心模块，而`react-router-dom`则包含了除核心模块以外，针对DOM绑定的组件，如`<Link/>`,`<BrowserRouter/>`。
> - 因此，只需要安装`react-router-dom`就好了，当然，如果搭配使用`react-redux`则还需要`react-router-redux`
- 安装
```bash
yarn add react-router-dom
```
- 使用：【示例1：基本路由】
```js
import React from "react";
import { BrowserRouter as Router, Route, Link } from "react-router-dom";

const Index = () => <h2>Home</h2>;
const About = () => <h2>About</h2>;

export default AppRouter = () => (
  <Router>
    <div>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/about/">About</Link>
          </li>
        </ul>
      </nav>
      <Route path="/" exact component={Index} />
      <Route path="/about/" component={About} />
    </div>
  </Router>
);
```
- 使用：【示例2：路由嵌套】
    - 在一级路由组件中，实现二级路由.以函数式组件为例
```js
import React from "react";
import { BrowserRouter as Router, Route, Link } from "react-router-dom";

const Home = () => <h2>Home</h2>;
export default App = () => (
  <Router>
    <div>
      <ul>
        <li><Link to="/">Home</Link></li>
        <li><Link to="/topics">Topics</Link></li>
      </ul>
      <Route exact path="/" component={Home} />
      <Route path="/topics" component={Topics} />
    </div>
  </Router>
);

const Child = ({match})=><h3>这是Topics的子路由对应组件</h3>
const Topic = ({match})=><h3>返回匹配路径的组件：{match.params.id}</h3>
const Topics = ({ match }) => (
  <div>
    <h2>Topics组件</h2>
    <ul>
      <li><Link to={`${match.url}/child`}>Child</Link></li>
      <li><Link to={`${match.url}/topicOne`}>topicOne</Link></li>
      <li><Link to={`${match.url}/topicTwo`}>topicTwo</Link></li>
    </ul>
    <Route exact path={`${match.path}/child`} component={Child} />
    <Route exact  path={match.path}  render={() => <h3>请选择一个子路由</h3>} />
    <Route path={`${match.path}/:id`} component={Topic} />
  </div>
);
```
> 上面`${match.path}/:id`可以匹配父路由下的所有路径，因此放在最后，在组件中通过`{match.params.id}`获取到`/:id`匹配的任意路径，如：`topicOne`
> 如上：路由嵌套使用 `match`对象来匹配上级。`match`对象含如下属性：
>    - isExact:Boolean
>    - params:Object，对应路由的参数
>    - path:String， 对应路由path
>    - url:String, 对应路由url

## Router对应组件
> 在React Router中，有三种类型的组件：`Router组件`，`Route路由匹配组件`,`Link导航组件`。所有组件都需要在`react-rouer-dom`中引入使用。


#### **Routers**
> react-router-dom提供了` <BrowserRouter>`和`<HashRouter>`两种路由模式，如果有服务器端的动态支持，建议使用 BrowserRouter，否则建议使用 HashRouter。`hashrouter`是以#号方式匹配路由，从url中可以看出来，这个地址对于后端来说，全部指向同一个地址,而`browserrouter`不存在#的，不同的路由对于后端也是不同的地址。

#### **Route 路由匹配组件**
> 有两种路由匹配组件：` <Route> `和`<Switch>`。
> - 路由匹配组件通过对比其`path`属性与当前浏览器的pathname是否一致来决定是否渲染其内容。如果一个`<Route>`没有path属性，将会匹配所有路径。
> - `<Switch>`组件用于对`<Route>`组件进行分组组合，switch组件只会选择渲染第一个匹配当前路径的Route组件。这对于多个组件都匹配时比较有用，或者在做路由之间的动画过渡或做404缺省配置时很有用。
> - Route组件有三种渲染方式：
>   - component ： `render={MyComponent}`经常使用的渲染属性，用于直接设置Route对应渲染的组件内容。
>   - render ： `render={()=><MyComponent someParm={this.state.param}>}`,常用，render对应一个行内函数。当你需要向这个Route组件传递当前作用域内的参数【如`this.state.param`】时，就需要用到此方法。
>   - children ： 不常用，后续介绍。

#### 导航组件
> React Router提供了如下三种导航组件：
- `<Link>`组件用于代替html中的`a`标签。
- `<NavLink>`是Link组件的一种特殊形式，它在Link之上还多出了`active`样式的功能。当当前路径匹配了NavLink对应的路径时，其会`active`样式会激活。
- `<Redirect>`组件用于强制跳转到其`to`属性对应的路径组件中。当你想在当前直接强制重定向到另一页面时可以使用。如在`/foo`对应组件内使用`<Redirect to="/bar"`时，当路径匹配`/foo`就会直接跳转到`/bar`对应组件中。
```js
import {BrowserRouter as Router,Route,Switch,Link,NavLink} from 'react-router-dom';
const MyComponent=<h3>MyComponent</h3>;
const ReactComponent=<h3>ReactComponent</h3>;
const ComponentRedirect=<div>立即跳转：<Redirect to="/login" /></div>
<Router>
    <div>
        <Link to="/">Home</Link>
        <NavLink to="/mine" activeClassName="active"> MyComponent</NavLink>
        <NavLink to="/react" activeClassName="active"> React</NavLink>

        <Switch>
            <Route path="/react" component={ReactComponent} />
            <Route path="/mine" render={()=><MyComponent title="传进this.props.title的值呀"/>} />
            <Route redirect="/toRedirect" component={ComponentRedirect} />
        </Switch>
    </div>
</Router>
```
- 注意：Router只能有一个子组件，所以其内容最好用一个div包裹

## Router的代码拆分
- 对比如下两张图：
![拆分前后](https://camo.githubusercontent.com/129f34bba8ba80f8a4a1a037619b5f7076906e06/687474703a2f2f7468656a616d65736b796c652e636f6d2f696d672f72656163742d6c6f616461626c652d73706c69742d62756e646c65732e706e67)

左图是未做Code Spliting的APP，通常这会导致首屏加载白屏或等待时间过长。右图是拆分后，各组件都动态加载，按需引入。

有以下几种Code Spliting的方法
1. 通过`webpack`做动态导入import，webpack会在打包时识别动态导入的组件，从而让`dynamic imports`组件从最终的main.js中分离形成多个不同的js文件，而app会在用到组件时再加载相应的js文件
2. 使用`babel`对应的插件[@babel/plugin-syntax-dynamic-import](https://babeljs.io/docs/plugins/syntax-dynamic-import/)。当你使用babel来编译jsx文件时，就可以使用此插件，用于告诉webpack进行动态组件的打包
```json
{
  "presets": ["@babel/react"],
  "plugins": ["@babel/plugin-syntax-dynamic-import"]
}
```
3. 使用`react-loadable`【推荐】
    - react-loadable对应github地址及文档：[react-loadable](https://github.com/jamiebuilds/react-loadable)
    - react-loadable是一个高阶组件HOC，相比于自己创建一个动态导入函数`asyncFunction`来动态导入组件，react-loadabel能够处理更多边界情况，且支持服务端渲染。
    - 常用参数：
        - loader:function,传入动态导入对应组件的函数，`loader: () => import("./Dashboard")`
        - loading:Compnonent,当组件在加载中，或者加载失败时，对应的组件，
        - timeout:ms，对应加载毫秒数，当超时时向Loading组件传入props.timeOut
```js
import Loadable from "react-loadable";
import Loading from "./Loading";

const LoadableComponent = Loadable({
  loader: () => import("./Dashboard"),
  loading: Loading
});

export default class LoadableDashboard extends React.Component {
  render() {
    return <LoadableComponent />;
  }
}

// Loading.js组件

function Loading(props) {
    if (props.error) { //加载失败
        return <div>Error! <button onClick={ props.retry }>Retry</button></div>;
    } else if (props.timedOut) { //加载超时
        return <div>Taking a long time... <button onClick={ props.retry }>Retry</button></div>;
    } else if (props.pastDelay) { //加载中
        return <div>Loading...</div>;
    } else {
        return null;
    }
}
```

## 集成Redux
通常情况，react-router和redux适配得挺好的，但是偶尔也会出现路由发生变化但当前组件未被渲染的情况。导致这种情况的原因一般有两种：
1. 组件是通过Redux的`connect()(Comp)`生成的
2. 组件不是一个Route声明的组件，即此组件未经过` <Route component={SomeConnectedThing}/>`

为了集成redux与react-router，从而实现通过`dispactch`来操作导航路由，或者同步路由数据到store中，便可以引用`react-router-redux`

```js
// before
export default connect(mapStateToProps)(Something)

// after
import { withRouter } from 'react-router-dom'
export default withRouter(connect(mapStateToProps)(Something))
```

#### 像VueRouter的Routes配置一样，配置ReactRouter
- 如果习惯VueRouter的写法来配置ReactRouter，也是可以的，官方推出了一个工具包：[react-router-config](https://github.com/reacttraining/react-router/tree/master/packages/react-router-config)
- 大致配置如下：
```js
const routes = [
  {
    component: Root,
    routes: [
      {
        path: "/",
        exact: true,
        component: Home
      },
      {
        path: "/child/:id",
        component: Child,
        routes: [
          {
            path: "/child/:id/grand-child",
            component: GrandChild
          }
        ]
      }
    ]
  }
];
```

## 主要API

### Route
- Route组件是整个ReactRouter的核心部分，用于在当前URL匹配时，做出对应渲染。

#### 渲染模式
- Route有三种渲染模式：render,component,children

##### *component渲染*
- 当路径匹配时，Route会直接渲染其component属性对应的React组件实例或者DOM。
- 其会默认向component对应组件的props传入当前URL的location对象
```js
<Route path="/user/:username" component={User} />;

class User extends React.Component{
    render(){
        console.log(this.props) //对应一个location对象，{match:obj,history:obj,location:obj}
        return <div>User组件</div>
    }
}
```

##### *render渲染*
- 当需要向路由组件传入额外的参数时，就可以使用`render`
- component 的优先级高于render，所以不要同时使用

```js
 <Route path="/bar" render={(props)=><Bar title={"这是给Bar的title"} {...props} />}></Route>

 class Bar extends React.Component{
    render(){
        console.log(this.props) //对应一个location对象，{match:obj,history:obj,location:obj}
        return <div>{this.props.title}</div>
    }
}
```

##### *children渲染*
- 无论当前path是否匹配，都想要渲染某一组件时，可以使用children
```js
<Route children={()=><div>啦啦啦</div>}></Route>
```

#### Route属性
- Route有三个属性：match,location,history

- `path`: string | string[]
    - 当前Route匹配的path值，可以是字符串，也可以是字符串数组
```js
<Route path={["/users/:id", "/profile/:id"]} component={User} />
```

- `exact`: bool
    - 为`true`时，只有当其path值与`location.pathname `完全匹配时才渲染
- `strict`: bool
    - 为`true`时，其path值需要匹配最后的斜杠`/`。如下表格：

path|location.pathname|是否匹配
---|---|---
/one/|/one|no
/one/|/one/|yes
/one/|/one/two|yes

- `location`: object
    - 默认情况下都是Route的path与浏览器的url做对比，判断是否渲染。而location属性可以提供除浏览器外的对比条件。【暂时不明白】

- `sensitive`: bool
    - 为true时，对大小写敏感，会严格区分大小写

### BrowserRouter
- 使用H5的historyAPI来渲染和处理URL
#### 参数：
- `basename`:String
    - 全局匹配的URL前缀。
```js
<BrowserRouter basename="/calendar"/>
<Link to="/today"/> // 渲染为 <a href="/calendar/today">
```

- `getUserConfirmation`:func
    - 用于确认跳转时的函数，默认使用window.confirm

- `forceRefresh`: bool
    - 如果为`true`，当页面跳转时会全页刷新，
- `keyLength`: number
    - 对应`location.key`不常用
- `Children`:node
    - 即Router包裹的子组件，只能是唯一的子节点

### HashRouter
- 使用hash来导航页面URL。【注意，hashRouter不支持location.state,location.key】

#### 参数
- `basename`:string 同BrowserRouter

- `getUserConfirmation`:func 同BrowserRouter

- `hashType`: string
    - 对应` window.location.hash`的编码方式，其值有3种
        1. "slash" ： 对应会渲染为`#/sunshine/lollipops`
        2. "noslash" ： 对应渲染为`#sunshine/lollipops`
        3. "hashbang" : 对应渲染为` #!/sunshine/lollipops`【google已弃用】

- `Children`: node 同BrowserRouter

### Link
- 用于在应用内声明式的跳转

#### 参数

- `to`: string
    - 值为字符串时，用于直接表示导航的路径，其search值也直接跟在其后
    ```js
    <Link to="/courses?sort=name" />
    ```

- `to`:object
    - 值为对象时，有以下几个可选参数
        1. pathname:对应Route组件的path
        2. search: 对应导航时的query参数
        3. hash:对应匹配URL的hash值
        4. state: [不清楚]对应location的state
```js
<Link
  to={{
    pathname: "/courses",
    search: "?sort=name",
    hash: "#the-hash",
    state: { fromDashboard: true }
  }}
/>
```

- `replace`: bool
    - 为true时，会在history栈中替换当前url值
```js
<Link to="/courses" replace />
```

- `innerRef`: function
    - 对应返回Link最终渲染出的a标签的DOM，从面可以操作a标签内的DOM
```js
const refCallback = node => {
  // `node` refers to the mounted DOM element or null when unmounted
  console.log(node)
  // <a href="/">跳转吧</a>
}
<Link to="/" innerRef={refCallback} >跳转吧</Link>
```

### NavLink
- `Link`组件的特殊形式，提供了更多对当前匹配Link的样式控制属性

#### 参数
- `activeClassName`: string
    - 当前URL匹配时，给对应元素添加className的值，默认`className=active`
```js
<NavLink to="/faq" activeClassName="selected">FAQs</NavLink>
```

- `activeStyle`: object
    - 应用在当前Link的激活样式
```js
<NavLink to="/faq" activeStyle={{fontWeight: "bold",color: "red"}}>FAQs</NavLink>
```
- `exact`: bool
    - 为`true`时，active的样式或者className只会在路由完全匹配时渲染

- `isActive`: func
    - 用于当处理当路由匹配是否激活时，做回调处理
```js
const oddEvent = (match, location) => {
  if (!match) {
    return false
  }
  const eventID = parseInt(match.params.eventID)
  return !isNaN(eventID) && eventID % 2 === 1
}

<NavLink
  to="/events/123"
  isActive={oddEvent}
>Event 123</NavLink>
```

- `location`: object
    - 用于`isActive`函数中的location参数。当不设置location属性时，isActive函数中的location参数默认为当前浏览器的location值，如果设置此属性，则isActive函数中的location参数即为比较此属性的结果
```js
const oddEvent = (match, location) => {
  if (!match) { //当跳转的url不匹配/events/777时，match即为false
    return false
  }
  const eventID = parseInt(match.params.eventID)
  return !isNaN(eventID) && eventID % 2 === 1
}

<NavLink
  to="/events/123"
  isActive={oddEvent}
  location="/events/777"
>Event 123</NavLink>
```

### Redirect
- 重定向组件，用于在当前路径中，重定向到另一个地址，且在history栈中会替换跳转前的location。类似于服务端重定向(http 3xx)

#### 参数
- `to`:string
    - 重定向到的路径
```js
<Redirect to="/somewhere/else" />
<Redirect
  to={{
    pathname: "/login",
    search: "?utm=your+face",
    state: { referrer: currentLocation }
  }}
/>
```
- `to`: object
    - 重定向到的路径,可以多个参数

- `push`: bool
    - 为`true`时，新location不会替换掉跳转前的location
- `from`: string
    - 用于设置从匹配哪个路径时，做重定向跳转。此属性只适用于`Redirect`组件作为`Switch`组件的子节点时使用。
```js
<Switch>
  <Redirect from='/users/:id' to='/users/profile/:id'/>
  <Route path='/users/profile/:id' component={Profile}/>
</Switch>
```
- `exact`: bool
    - 为true时，需要完成匹配才生效

### WithRouter
- `withRouter`可以包装任何自定义组件。其实就是一个高阶组件，通过它生成的组件，会将history,location,match 三个对象传入props中。
- 应用场景：当需要通过当前URL来做判断，或者需要根据当前URL来做下一步操作时，可以使用withRouter。如做面包屑时需要实时根据path变化作出变化
- 注意：withRouter并不会自动监听组件状态变化，因此在与redux搭配时，需要这样来：`withRouter(connect(...)(MyComponent))`
```js
import React from 'react';
import {withRouter} from 'react-router-dom';
import PropTypes from 'prop-types';

class Header extends React.Component {
    static propTypes={
        history:PropTypes.object.isRequired,
        match:PropTypes.object.isRequired,
        location:PropTypes.object.isRequired,
    };

    render() {
        const {match,history,location}=this.props;
        return (<div>
            顶部{location.pathname}
        </div>)
    }
}

export default withRouter(Header);
```



