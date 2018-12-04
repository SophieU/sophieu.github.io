---
title: React高级指南二
date: 2017-07-26 14:11:40
tags: React
categories: 前端学习
---
- 入门React后，基本上可以写一个小型项目了，但React还有很多高级功能及API可以上手方便开发。以下便是React进阶教程说明。[参考文档-react中文文档](https://react.css88.com/docs/higher-order-components.html)
<!--more-->
## 目录

- [高阶组件HOC](#高阶组件HOC)
- [深入JSX](#深入JSX)
- [渲染属性](#渲染属性)
- [PropTypes类型检查](#PropTypes类型检查)
- [不受控组件](#不受控组件)
- [ReactDOM的API](#ReactDOM的API)
- [常见问题](#常见问题)
  - [事件处理函数](#事件处理函数)
  - [State和Props](#State和Props)
  - [项目文件结构](#项目文件结构)

## 高阶组件HOC
> 高阶组件（HOC-Higher-Order-Components）是 React 中用于重用组件逻辑的高级技术。**高阶组件是一个函数，能够接受一个组件并返回一个新的组件**。简单来说，高阶组件将一个组价转化（包装）成另外一个组件。
> 在第三方组件库中，HOC非常常见。如例如Redux的[connect](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options) 和 [Relay’s createFragmentContainer](http://facebook.github.io/relay/docs/en/fragment-container.html)。
- 高级组件是纯函数，没有副作用。
```js
// 示例1：创建一个高阶组件，使每个组件在更新时，打印其新旧prop值
// hocWrapper.js
export default function logProps(WrappedComponent){
    return class extends React.Component{
        componentDidUpdate(prevProps){
              console.log('old props',preProps);
            console.log('new props',this.props)
        }
        render(){
            return <WrappedComponent {...this.props}/>
        }
    }
}
// componentOne.js
import logProps from './hocWrapper';
class More extends React.Component{....}
export default logProps(More)
```
- 如上，每当传入More组件的props发生变化时，都会打印其prop修改状况
- **注意**：不要改变原始组件原型，推荐用容器组件组合包裹组件且不修改包裹组件【如示例】。
- **注意**：不要在`render`函数中使用高阶组件
- **注意**：Refs不会被传递，若向一个高阶组件赋值`ref`，那么通过这个ref拿到的是最外层的窗口组件。解决这个问题可以使用`React.forwardRef`API
- **注意**：高阶组件的静态方法需要从原组件进行复制，即`HOCComponet.staticMethod = WrappedComponent.staticMethod`;

## 深入JSX
> 从本质上讲，JSX只是为`React.createElement(component, props, ...children)`函数提供的语法糖。
```js
// JSX代码
<MyButton color="blue" shadowSize={2}>Click Me</MyButton>
// 编译为【类似于Vue的render方法】
React.createElement(
    MyButton,
    {color:'blue',shadowSize:2},
    'Click Me'
)  
```
- 用户定义组件必须以`大写字母`开头
- JSX可以使用`.`语法。如：`<React.Fragment></React.Fragment>`
- 若没给prop属性传值，那么他默认为`true`。如：`<MyTextBox autocomplete />`中autocomplete默认为true
- JSX表达式中，标签内的元素可以通过`props.children`获取到，即内容插槽。`props.children`可以是字符串内容，也可以是DOM标签或者React组件实例
- `Boolean`，`Null`,`Undefined`会被忽略。false，null，undefined，和 true 都是有效的的 children(子元素) 。但是并不会被渲染。这在`条件渲染`React元素时非常有用。

## 插槽Portals
> Portals 提供了一种很好的方法，将子节点渲染到父组件 DOM 层次结构之外的 DOM 节点。
> Portals看似跟`{ptops.children}`功能类似，但其主要功能是，它可以将子元素插入到`任意的DOM节点`位置
> 详细解读：[官方文档--插槽Portals](https://react.css88.com/docs/portals.html)
```js
// props.children展示子元素
render() {
  // React 装载一个新的 div，并将 children 渲染到这个 div 中
  return (
    <div>
      {this.props.children}
    </div>
  );
}
//将子元素插入到任意DOM节点
render() {
  // React *不* 会创建一个新的 div。 它把 children 渲染到 `domNode` 中。
  // `domNode` 可以是任何有效的 DOM 节点，不管它在 DOM 中的位置。
  return ReactDOM.createPortal(
    this.props.children,
    domNode,
  );
}
```
- 通过`react.createPortal`可以实现如`Modal`，`对话框`等特定组件功能

## 渲染属性
>  “render prop” 是指一种技术，用于使用一个值为函数的 prop 在 React 组件之间的代码共享。
> - 使用Render props的库包括`React Router`和`Downshift`
> - 参考文章：[渲染属性render](https://react.css88.com/docs/render-props.html)
> **render prop 是一个组件用来了解要渲染什么内容的函数 prop。**
> - 关于 render props 一个有趣的事情是你可以使用一个带有 render props 的常规组件来实现大量的 高阶组件 (HOC)。

```js
<DataProvider render={data => (
  <h1>Hello {data.target}</h1>
)}/>
```
- 示例：封装一个记录鼠标坐标的组件，且可扩展使用图片跟随鼠标移动
- 思路：1、创建一个Mouse组件用于跟随和显示鼠标，2、通过render属性向Mouse组件传递需要扩展的内容
```js
// Mouse.js
class Mouse extends React.Component{
  constructor(props) {
        super(props);
        this.handleMove=this.handleMove.bind(this);
        this.state={
            x:0,
            y:0
        }
    }
    handleMove(e){
        this.setState({
            x:e.clientX,
            y:e.clientY
        })
    }
    render() {
        return (
            <div style={{height:'500px',width:'800px',background:'#ff0'}} onMouseMove={this.handleMove}>
                {this.props.render?this.props.render(this.state):<p>the mouse position is ({this.state.x},{this.state.y})</p>}
            </div>
        )
    }
}
// 扩展用的图片
class Cat extends React.Component{
  render(){
    const mouse = this.props.mouse
    return <img src="./cat.jpg" style={{position:'absolute',left:mouse.x,top:mouse.y}} alt=""/>
  }
}
// 组合
class MouseTracker extends React.Component {
    render() {
        return (<div >
            <h1>Move the mouse around!</h1>
            <Mouse render={mouse=>(<Cat mouse={mouse}/>)}/>
        </div>)
    }
}
```
- 这里的render属性不一定非要用`render`表示，只是方便解读，它可以是任意命名的属性，只是一个返回值是组件的渲染函数

## PropTypes类型检查
> **注意**：从v15.5开始，React.PropTypes助手函数已被弃用，需要单独引入`prop-types`库来定义contextTypes
```js
import PropTypes from 'prop-types';
class Hello extends React.Component{
  render(){
    return(<h1>Hello,{this.props.name}</h1>)
  }
}
Hello.propTypes={
  name:PropTypes.string
}
```
- 更多prop-types对应的类型：[使用PropTypes进行类型检查](https://react.css88.com/docs/typechecking-with-proptypes.html)

#### 默认Prop值
> 通过`defaultProps`属性可以设置组件的默认属性值：
```js
MyComponent.defaultProps={
  name:'haha'
}
```
## 不受控组件
> 在处理表单的时候，推荐使用受控组件。当然，也可以使用不受控组件来处理。相对于为每个状态更新编写一个事件处理程序，不受控组件的值可能通过使用一个`ref`来从DOM获得表单值。
- **默认值**：通过`defaultValue`来表示不受控组件的默认值。对于checkbox则使用`defaultChecked`来设置默认值
- **file Input** : 在React中`file Input`永远是一个不受控制的组件，因为它的值只能由用户设置，而不是以编程方式设置。`通过给file input添加ref属性来获取到file input对应的files文件`
```js
class Form extends React.Component{
  constructor(){
    super();
    this.textInput = React.createRef();
    this.fileInput = React.createRef();
    this.submitForm=this.submitForm.bind(this);
  }
  submitForm(e){
    e.preventDefault();
    const userName = this.textInput.current.value;
    const fileName = this.fileInput.current.files[0].name
    console.log(userName,fileName)
  }
  render(){
    return (
      <div>
        <input defaultValue="lala" name="username" type="text" ref={this.textInput}/>
        <input name="file" type="file" ref={this.fileInput}/>
        <button onClick={this.submitForm}>点我</button>
      </div>
    )

  }
}
```
## ReactDOM的API

## 常见问题
#### 事件处理函数
- **如何绑定？**：`onClick={this.handleClick}`用于绑定
- **如何确保处理函数在组件实例作用域内？**:
  1. 在`constructor`中，通过`this.handleClick=this.handleClick.bind(this)`绑定作用域
  2. 使用箭头函数来声明处理函数，`handleClick=()=>{xxx}`
  3. 在`render`中使用bind绑定【此方法会在每次组件渲染时创建一个新函数，而影响性能】，`<button onClick={this.handleClick.bind(this)}>Click Me</button>`
  4. 在`render`中使用箭头函数【同3一样，会影响性能】,`<button onClick={() => this.handleClick()}>Click Me</button>`
- **如何传参？**：
  1. 使用`bind`:`<button onClick={() => this.handleClick(id)} />`
  2. 通过`data-*`属性传参：`<button data-letter={letter} onClick={this.handleClick}/>`，在handleClick中通过`e.target.dataset.letter`拿取data-letter的值

#### State和Props
- **区别？**：`state`是组件内部管理的，而`props`是从外部付入的
- **state是异步的**，如果更新state需要用到当前的state，那么应该向`setState`中传入一个函数，而不是对象。`this.setState((prevState)=> return {count:prevState+1})`
- **第三方状态管理**：像`redux`或者`Mobx`的状态管理库都挺好的，但是如果项目体量小，其实只用React也是可以实现的

#### 项目文件结构
> 项目文件该以何种结构来组织？这其实不是一个应该纠结的问题。有以下几种设置项目目录结构的方式：
  1. 资源就近维护：将CSS，JS和测试文件一起放入按功能或路由分组的文件夹中
  2. 按文件类型来分组：如`imgs`统一维护项目图片

#### AJAX及APIs
- **如何发送AJAX？**：你可以使用任意你喜欢的库，如`Axios`，`jQuery Ajax`或浏览器自带的`window.fetch`
- **何时发送？**：在`componentDidMount`生命周期方法内发送Ajax请求数据。这样你才能够在请求的数据到达时使用 setState 更新你的组件。
- **如何取消发送Ajax?**：在`componentWillUnmount `生命周期中取消未完成的Ajax请求。如在`axios`中，`this.serverReq=axios.get(xxx).`再在componentWillUnmount中通过`this.serverReq.abort()`取消