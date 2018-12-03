---
title: React进阶要点汇总(一)
date: 2017-07-19 17:11:40
tags: React
categories: 前端学习
---
- 入门React后，基本上可以写一个小型项目了，但React还有很多高级功能及API可以上手方便开发。以下便是React进阶教程说明。
<!--more-->

## 目录

- [代码拆分](#代码拆分)
- [错误处理ErrorBoundaries](#错误处理ErrorBoundaries)
- [上下文Context](#上下文Context)
- [Refs](#Refs)
- [片段Fragment](#片段Fragment)

## 代码拆分
- 类似于vue中的组件引用时加载，react也可以在引用时再加载到组件中，从而使打包的app.js被拆分为多个组件。
- 代码拆分的方法有多种，如：`CodeSpliting打包优化`，`react.lazy懒加载`，`基于路由进行代码拆分`
- 代码拆分可以提升首屏加载速度

#### CodeSpliting
> 将代码拆分引入到应用程序中的最好方法是通过动态 import() 语法。
> 在`create-react-app`中已经配置好对应的webpack语法，Next.js也支持开箱即用，而如果自己设置`webpack`话，需要阅读关于`code spliting`的[代码拆分指南](http://www.css88.com/doc/webpack/guides/code-splitting/)
> babel对应解析动态导入语法：`babel-plugin-syntax-dynamic-import`
- 示例：[create-react-app动态import](https://facebook.github.io/create-react-app/docs/code-splitting)
```js
// asyncComponent.js 创建一个动态导入组件函数
export default function asyncComponent(importComponent){
    class AsyncComponent extends Component{
        constructor(props){
            super(props);
            this.state={
                component:null
            }
        }
        async componentDidMount(){
            const {default:component} = await importComponent();
            this.setState({
                component:component
            })
        }
        render(){
            const C = this.state.component;
            return C?<C {...this.props}/>:null
        }
    }
    return AsyncComponent;
}
// 在create-react-app中使用 动态import
const asyncForm = AsyncComponent(()=>import('./page/form/Form'));
class Routes extends React.Component {
    render() {
        return (<Switch>
            <Route path="/form" component={asyncForm}></Route>
        </Switch>)
    }
}
```
#### React.lazy
- React.lazy就是用于动态加载组件的工具包
> **注意：**
> React.lazy 和 Suspense 尚不可用于服务器端渲染。如果要在服务器渲染的应用程序中进行代码拆分，我们建议使用 [Loadable Components](https://github.com/smooth-code/loadable-components)。它有一个很好的[服务器端渲染打包拆分指南](https://github.com/smooth-code/loadable-components/blob/master/packages/server/README.md)。

```js
const OtherComponent = React.lazy(()=>import('./OtherComponent'));
function MyComponent(){
    return (
        <div><OtherComponent/> </div>
    )
}
```
#### Suspense
- 当组件还未加载完成时，可以渲染一些loading动画，这时，可以使用`Suspense`组件完成
```js
const OtherComponent = React.lazy(()=>import('./OtherComponent'));
function MyComponent(){
    return (
        <div>
            <Suspense fallback={<div>loading....</div>}>
                <OtherComponent/> 
            </Suspense>
        </div>
    )
}
```
- `fallback prop（属性）` 接受在等待加载组件时要渲染的任何 React 元素

## 错误处理ErrorBoundaries
- 当其他模块无法加载（如：网络故障时），则会触发错误，这时可以使用React拿出的[错误边界处理Error Boundary](https://react.css88.com/docs/error-boundaries.html)。
> 如果一个类组件定义了生命周期方法中的任何一个（或两个）`static getDerivedStateFromError()` 或` componentDidCatch()`，那么它就成了一个错误边界。 使用`static getDerivedStateFromError()`在抛出错误后渲染回退UI。 使用 `componentDidCatch()` 来记录错误信息。
- 注意 **错误边界(Error Boundaries) 仅可以捕获其子组件的错误。**
```js
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { error: null, errorInfo: null };
  }
  
  componentDidCatch(error, errorInfo) {
    // Catch errors in any components below and re-render with error message
    this.setState({
      error: error,
      errorInfo: errorInfo
    })
    // You can also log error messages to an error reporting service here
  }
  
  render() {
    if (this.state.errorInfo) {
      // Error path
      return (
        <div>
          <h2>Something went wrong.</h2>
          <details style={{ whiteSpace: 'pre-wrap' }}>
            {this.state.error && this.state.error.toString()}
            <br />
            {this.state.errorInfo.componentStack}
          </details>
        </div>
      );
    }
    // Normally, just render children
    return this.props.children;
  }  
}
```
- 以上，当出现错误时，`this.state.errorInfo`为true，则会渲染预设好的错误展示页面，没错则直接渲染子组件内容
- 错误边界的粒度完全取决于你的应用。你可以将其包装在最顶层的路由组件并为用户展示一个 “发生异常（Something went wrong）“的错误信息，就像服务端框架通常处理崩溃一样。你也可以将单独的插件包装在错误边界内部以保护应用不受该组件崩溃的影响。

## 上下文Context
上下文`context`提供了一种通过组件树传递数据的方法，无需在每个级别手动传递` props `属性。

- 何时使用`Context`?
    - Context 旨在共享一个组件树内可被视为 “全局” 的数据,例如当前经过身份验证的用户，主题或首选语言等。
    - 当你不想在组件树中通过逐层传递`props`或者`state` 的方式来传递数据时，可以使用`Context`来实现`跨层级`的组件数据传递。
- 如何使用`Context`？
    - 要context发挥作用，需要用到两种组件，一个是context生产者（`Provider`）[通常是父节点]，一个是消费者`Consumer`【通常是一个或多个子节点】—— 所以context的使用是基于**生产者消费者模式**
- 参考文章：[React Context 的理解以及应用](https://www.jianshu.com/p/eba2b76b290b)

#### Context对应的API
1. **React.createContext(defaultValue)**
    - 用于创建一个context对象，其中`defaultValue`为默认初始值，当Provider中没有匹配的value时，会在defaultValue中查找
    ```js
    const MyContext = React.createContext();
    ```
2. **Context.Provider**
    - 生产者，作为一个组件，用它包裹的后代组件将共享它的`value`属性，当`value`属性发生变化时，所有作为 `Provider(提供者)` 后代的 `consumer(使用者)` 组件 都将重新渲染。
    ```js
    <MyContext.Provider value={/*some value*/}>
    ```
3. **Context.Consumer**
    - 一个可以订阅 context 变化的 React 组件。需要接收一个函数作为子节点。该函数接收当前 context 值并返回一个 React 节点，基参数即为`Provider`中传入的value属性值（对象）或者`createContext`中的`defaultValue`
    ```js
     <MyContext.Consumer>
      {({theme}) => (
        <button style={{backgroundColor: theme.background}}>主题色</button>
      )}
    </MyContext.Consumer>
    ```
4. **Class.contextType**
    - 在React的普通Class组件中，通过contextType声明分配由`React.createContext()`创建的Context 对象。之后便可以使用`this.context`使用该context对象的`value`值
    ```js
    class MyClass extends React.Component {
        static contextType = MyContext;
        render() {
            let value = this.context;
            /* render something based on the value */
        }
    }
    ```
#### 示例
```js
// themecontext.js
import React from 'react';
export const themes= {
    light:{
        foreground:'#000',
        background:'#eee'
    },
    dark:{
        foreground: '#fff',
        background:'#222'
    }
};
export const ThemeContext=React.createContext(
    themes.dark
);
// themeButton.js
import {ThemeContext} from "./theme-context";
import React from 'react';

export default class ThemeButton extends React.Component{
    static contextType=ThemeContext;
    render(){
        let props = this.props;
        let theme = this.context;
        return (
           <div>
               <button {...props}></button>
               <div style={{width:100,height:100,background:theme.background}}></div>
           </div>
        )
    }
}
// Context.js
import React from 'react';
import {ThemeContext,themes} from "./theme-context";
import ThemeButton from './theme-button'

function Toolbar(props){
    return (
        <ThemeButton onClick={props.changeTheme}>
            Change Theme
        </ThemeButton>
    )
}
class Context extends React.Component {
    constructor(props) {
        super(props);
        this.state={
            themes:themes.light,
        };
    }
    toggleTheme=()=>{
        this.setState((preState)=>{
            return {
                themes:preState.themes===themes.dark?themes.light:themes.dark
            }
        });
    };
    render() {
        return (<div>
            <ThemeContext.Provider value={this.state.themes}>
                <Toolbar changeTheme={this.toggleTheme}></Toolbar>
            </ThemeContext.Provider>
        </div>)
    }
}

export default Context;
```
- 更多示例参考：[context上下文](https://react.css88.com/docs/context.html)

## Refs
- `Refs`提供了一个对真实DOM（组件）的引用，我们可以通过这个引用直接操作DOM（跟vue的refs类似） 
- **什么时候用??** 当需要处理元素的focus,文本的选择或者媒体的播放等，以及触发强制动画或者同第三方库集成的时候。
- **如何用？**：在`V16`版本后，官方提供了`React.createRef()`这个API用于创建Ref变量，然后再将这个变量赋值给组件声明中ref属性就好了。
```js
class fancy extends React.Component {
    constructor(props) {
        super(props);
        this.textInput=React.createRef();
        this.focusInput=this.focusInput.bind(this);
    }
    focusInput(){
        // 通过this.xxx.current获取组件
        this.textInput.current.focus();
    }
    render() {
        return (<div>
            <h3>这将有一个button</h3>
            <div>
                <input ref={this.textInput} type="text" />
                <button onClick={this.focusInput}>按钮呀</button>
            </div>
        </div>)
    }
}
// 在v16版本之前
// 元素上ref属性定义
 <input ref={ele=>{this.textInput=ele}} type="text" />
 // 获取ref值
 this.textInput.focus();
```
- 在`v16`版本后，通过`createRef()`来生成`ref`并赋值给`对应组件`或`DOM元素`。之前是通过在元素上`ele=>this.refName=ele`完成赋值
- 在`v16`版本后，通过`this.refName.current`来获取`refName`对应`对应组件`或`DOM元素`。之前是直接通过`this.refName`获取
- 当`ref`属性赋在DOM元素上时，`this.refName.current`返回的就是DOM元素，当赋给React组件时，`this.refName.current`返回的就是React组件

#### 传递Refs
- 当父组件需要拿到子组件的`ref`时，在v16版本后可以通过`React.forwardRef()`实现
- 思路：通过父组件向子组件传递`ref`属性，在子组件中获取到父组件赋值的`ref`值后，对应的赋给相应元素实现ref的传递
```js
// 示例1：简单函数式组件中传递ref。如下，通过this.refFan.current可以直接获取到button
const FancyButton = React.forwardRef((props,ref)=>(
    <div>
        <h3>子组件</h3>
        <button ref={ref}>{props.children}</button>
    </div>
));
const refFan = React.createRef();
<FancyButton ref={refFan}>我是按钮</FancyButton>
// 示例2：在类组件中传递Ref
// child.js
import React from 'react';
class Child extends React.Component {
    render() {
        const {forwardedRef,...rest} = this.props;
        return(
            <div>
                <h3>这是子组件的另一个按钮</h3>
                <button ref={forwardedRef}>另一个按钮</button>
            </div>
        )
    }
}
export default React.forwardRef((props,ref)=>(<Child forwardedRef={ref}/>));
// parent.js
this.childRef= React.createRef();
<Child ref={this.childRef}>
```
- 通过`React.forwardRef`包裹组件用于传递父组件赋值的`ref`
- 在`类组件`中，通过属性`forwardedRef`来获取父组件赋值的`ref`,并赋给对应的元素或组件实例

## 片段Fragment
> React中一种常见模式是为一个组件同时返回多个并列元素。而render只接受一个子组件，所以通过`片段（Fragment）`可以将子元素列表添加到一个分组中，**且不会在DOM中增加多余的节点**。[应用场景](https://react.css88.com/docs/fragments.html)
> - 通过`<React.Fragment>`来表示这是一个代码片段。（类似于Vue的`<template>`标签）
> - `<React.Fragment>`支持`key`属性，即当使用`data.map`返回列表时，可以直接在`<React.Fragment>`标签上设置`key={xxx}`
```js
// table.js
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}
// column.js
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```


