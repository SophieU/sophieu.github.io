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
> - [Router路由](#Router路由)
> - [静态文件](#静态文件)
> - [中间件](#中间件)
> - [模板引擎](#模板引擎)
> - [Express设置代理](#Express设置代理)
> - [常见问题](#常见问题)

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

```js
// 函数式组件 - 创建简单，功能单一，常用作一些不变内容的渲染
function Welcome(props){
    return <h1>Hello,{props.name}</h1>
}

// 类组件
class Welcome extends React.Component {
    render (){
        return <h1>Hello,{this.props.name}</h1>
    }
}
```
