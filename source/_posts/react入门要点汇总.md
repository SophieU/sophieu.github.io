---
title: react入门要点汇总
date: 2017-06-05 09:50:28
tags: React
categories: 前端学习
---
React是一个声明式的，高效的，并且灵活的用于构建用户界面的 JavaScript 库。[参考文档-React中文文档](https://react.css88.com/docs/introducing-jsx.html)

React主要用于构建UI，很多人认为 React 是 MVC 中的 V（视图）。
<!--more-->
> **React特点：**
> 1. 声明式设计 −React采用声明范式，可以轻松描述应用。
> 2. 高效 −React通过虚拟DOM，最大限度地减少与DOM的交互。
> 3. 灵活 −React可以与已知的库或框架很好地配合。
> 4. JSX − JSX 是 JavaScript 语法的扩展。React 开发不一定使用 JSX ，但我们建议使用它。
> 5. 组件 − 通过 React 构建组件，使得代码更加容易得到复用，能够很好的应用在大项目的开发中。
> 6. 单向响应的数据流 − React 实现了单向响应的数据流，从而减少了重复代码，这也是它为什么比传统数据绑定更简单。

## 目录
> - [安装](#安装)
> - [JSX语法](#JSX语法)
> - [React组件](#React组件)
> - [Props属性](#Props属性)
> - [State状态](#State状态)

## 安装
- React有两种使用方式：`CDN`或`通过npm安装使用`
1. CDN方式：
```html
<!DOCTYPE html><html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello React!</title>
    <script src="https://cdn.bootcss.com/react/15.4.2/react.min.js"></script>
    <script src="https://cdn.bootcss.com/react/15.4.2/react-dom.min.js"></script>
    <script src="https://cdn.bootcss.com/babel-standalone/6.22.1/babel.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      ReactDOM.render(<h1>Hello, world!</h1>,
        document.getElementById('example')
      );    </script>
  </body></html>
```
2. NPM安装
- 通过`create-react-app`脚手架可以快速构建react开发环境，并通过修改`App.js`入门
```bash
$ npm install react react-dom
# 快速构建React开发环境
$ npm install create-react-app
$ create-react-app my-app
$ cd my-app && npm start
```

## JSX语法
- JSX是一种JS的语法糖，它执行更快，类型安装。编写React可以不用JSX，但官方推荐使用。
- 编译之后，JSX 表达式就变成了常规的 JavaScript 对象。因此可以在`if`或`for`中使用JSX,用它给变量赋值，或者将它作为函数的返回值。
- 在JSX内部不能使用`if else`语句，但可以使用`三元运算`
```js
// JSX表达式
function sayHello(user){
    if(user){
        return <h1>hello,{user}!</h1>
    }else{
        return <h1>hello,stranger.</h1>
    }
}
// 三元运算
ReactDOM.render(
    <div>
      <h1>{i == 1 ? 'True!' : 'False'}</h1>
    </div>
    ,    document.getElementById('example')
);
```

#### JSX属性
- 属性值可以是`字符串`，也可以是`对象`
- 属性名应该使用`驼峰命名法`，`class-->className`。【因为JSX比HTML更接近JS】

```js
// 1. 属性值-对象
const elm=<img className="avatar" src={user.avatarUrl} />
// 2. 属性值-字符串
const elm2=<div tabIndex="0"></div>

```

#### JSX样式
- React 推荐使用内联样式。
- React 会在指定元素数字后自动添加 px 
- 注意：样式名用`驼峰写法`
```js
// 行内直接写
const elm = <div style={{width:'100px',color:'#fff'}}>红色字体</div>
// 样式对象- 
const styleElm = {fontSize:100,color:'#ff0'};
ReactDOM.render(<h1 style={styleElm}></h1>,document.getElementById('app'))
```

## React组件
- 组件(Components) 让你可以将用户界面分成独立的，可复用的小部件，并可以对每个部件进行单独的设计。
- 从定义上来说， 组件就像JavaScript的函数。组件可以接收任意输入(称为”props”)， 并返回 React 元素，用以描述屏幕显示内容。
- 组件可分为：`函数式组件`和`类组件`两种
- [参考文章](https://segmentfault.com/a/1190000015699401)

```js
// 函数式组件 - 创建简单，功能单一，常用作一些不变内容的渲染，又称为无状态组件
function Welcome(props){
    return <h1>Hello,{props.name}</h1>
}

// 类组件- 类组件有状态，生命周期等更多特性
class Welcome extends React.Component {
    render (){
        return <h1>Hello,{this.props.name}</h1>
    }
}
```
- 注意：组件名称总是以`大写字母`开始
- `函数组件`：又称为“无状态组件”，“受控组件”或“木偶组件”，因为函数组件只负责接收 props 并返回 UI，它自身并不能拥有可改变的数据，在真实的 React 应用开发场景下，我们经常尽可能的使用函数组件，将整个应用的 UI 拆分成尽可能小的视觉单元。【记住：如果你的组件不需要追踪内部状态，尽量使用函数组件。】

#### 复合组件
- 使用组件的一大优势在于其可利用性高
- `复合组件`：在一个React组件内部引用了其他组件，从而构成了复合组件

```js
// 子组件
function Avatar(props){
    return <img src={props.user.avatarUrl} alt={props.user.name}/>
}
// 父组件
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
```
- 注意：JSX最外层只能由一个元素包裹，即return后只能是单个子元素

## Props属性
- `Props`是不可变的，只能读取，不能修改，可以理解为`props是从外部传入组件的数据`
- 由于React是`单向数据流`，所以props基本上也就是从服父级组件向子组件传递的数据。
- 在React中跟数据有关的三个属性是：`state`,`props`,`context`
- [参考文档](https://segmentfault.com/a/1190000011184076)
```js
// 子组件 item.js 。通过this.props拿值
export default class Item extends React.Component{
    render(){
        return (<li>{this.props.dataItem}</li>)
    }
}
// 父组件
import Item from './item.js'
export default class Lists extends React.Component{
    render(){
        const itemLists = data.map(item=><Item dataItem={item}/>)
        return ({itemLists})
    }
}
```
- 如上：数据`data`在通过遍历后，将属性值`dataItem`传入到子组件中，子组件通过`this.props.dataItem`接收数据

>**只读性**:props经常被用作渲染组件和初始化状态，当一个组件被实例化之后，它的props是只读的，不可改变的。如果props在渲染过程中可以被改变，会导致这个组件显示的形态变得不可预测。只有通过父组件重新渲染的方式才可以把新的props传入组件中。

#### 默认参数
在组件中，我们最好为props中的参数设置一个defaultProps，并且制定它的类型。比如，这样：

```
Item.defaultProps = {
  item: 'Hello Props',
};

Item.propTypes = {
  item: PropTypes.string,
};
```
- 关于`propsTypes`,可以声明为以下几种类型：
```
optionalArray: PropTypes.array,
optionalBool: PropTypes.bool,
optionalFunc: PropTypes.func,
optionalNumber: PropTypes.number,
optionalObject: PropTypes.object,
optionalString: PropTypes.string,
optionalSymbol: PropTypes.symbol,
```

#### 总结Props
> `props`是一个从外部传进组件的参数，主要作为就是从父组件向子组件传递数据，它具有`可读性`和`不变性`，只能通过外部组件主动传入新的props来重新渲染子组件，否则子组件的`props`以及展现形式不会改变。

## State状态
- 一个组件的显示形态可以由数据状态和外部参数所决定，外部参数也就是`props`，而数据状态就是`state`。
```js
export default class ItemList extends React.Component{
  constructor(){
    super();
    this.state = {
      itemList:'一些数据',
    }
  }
  render(){
    return (
      {this.state.itemList}
    )
  }
}
```
- 首先，在组件初始化的时候，通过this.state给组件设定一个初始的state，在第一次render的时候就会用这个数据来渲染组件。

#### 修改状态(setState)
- 通过`this.setState()`来修改state，而不能直接通过`this.state`做修改
- `setState`有两个参数，`状态对象【可以是函数返回值】`,`状态修改后的回调`
```js
// 示例：通过异步操作获取数据后，更新state
componentDidMount(){
  fetch('url')
    .then(response => response.json())
    .then((data) => {
      this.setState({itemList:item});  
    }
}

// 示例2： 传入回调
this.setState({
  name:'xb'
},()=>console.log('setState 完成'))

// 示例3：以函数返回值形式修改State 【这种方式一般是，新状态的值依赖于旧状态】
this.setState((prevState) => ({
    name: "mr." + prevState.name
}))
```
**注意：通过this.state=来初始化state，使用this.setState来修改state，constructor是唯一能够初始化的地方。**

#### state更新常犯的错
- state的更新可能是异步的，因此不要在setState中直接引用this.state.xxx来赋值
```js
// 错误代码
this.setState({
    counter:this.state.counter + this.props.increment
})
//正确
this.setState((state,props)=>{
    counter:state.counter+props.increment
})
```

#### 总结State
> state的主要作用是用于组件保存、控制以及修改自己的状态，它只能在constructor中初始化，它算是组件的私有属性，不可通过外部访问和修改，只能通过组件内部的this.setState来修改，修改state属性会导致组件的重新渲染。

#### State 和Props的区别
> 1. state是组件自己管理数据，控制自己的状态，可变；
> 2. props是外部传入的数据参数，不可变；
> 3. 没有state的叫做无状态组件，有state的叫做有状态组件；
> 4. 多用props，少用state。也就是多写无状态组件。

## React组件API
- 常用React的组件API有以下几个：
    - 设置状态：`setState`
    - 替换状态：`replaceState`
    - 设置属性：`setProps`
    - 替换属性：`replaceProps`
    - 强制更新：`forceUpdate`
    - 获取DOM节点：`findDOMNode`
    - 判断组件挂载状态：`isMounted`

#### 替换状态：replaceState
- `replaceState()`方法与`setState()`类似，但是方法只会保留nextState中状态，原state不在nextState中的状态`都会被删除`