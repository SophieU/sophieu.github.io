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
> - [HelloWorld](#HelloWorld)
> - [Express应用生成器](#Express应用生成器)
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
