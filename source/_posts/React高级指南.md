---
title: React进阶教程
date: 2017-07-19 17:11:40
tags: React
categories: 前端学习
---
- 入门React后，基本上可以写一个小型项目了，但React还有很多高级功能及API可以上手方便开发。以下便是React进阶教程说明。
<!--more-->

## 目录

- [代码拆分](#代码拆分)
- [上下文Context](#上下文Context)

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
#### 错误处理
- 当其他模块无法加载（如：网络故障时），则会触发错误，这时可以使用React拿出的[错误边界处理Error Boundary](https://react.css88.com/docs/error-boundaries.html)。
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