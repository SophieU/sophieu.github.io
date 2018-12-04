---
title: vue与react详细对比
date: 2018-12-04 16:37:31
tags: vue
categories: 前端学习
---
> 每次用vue或者react做完一个项目，隔个两三个月不用vue或react时，会发现要么好多东西又忘了，要么很多API混在一起了。所以，写下这篇文章，用于以后不时的复习参考。同时也加深下对两个框架的认知。
<!--more-->

## 目录
- [Vue与React](#Vue与React)
- [VueRouter与ReactRouter](#VueRouter与ReactRouter)
- [Vuex与Redux](#Vuex与Redux)
- [Vue与React服务端渲染](#Vuex与Redux)
- [Vue与React的代码拆分](#Vue与React的代码拆分)

## Vue与React
#### 介绍
- **Vue.js是什么？？**
    - `Vue`是一套用于构建用户界面的渐进式框架。【[vue与其他框架对比](https://cn.vuejs.org/v2/guide/comparison.html)】
- **React.js是什么??**
    -`React`是一个用于构建用户界面的 JavaScript 库。
#### 区别
1. **JSX与Template**
> 在React中一切皆为JS，所以对于HTML的书写，引入了JSX的概念，JSX等同于用`React.createElement`
```js
// react 
class MyComponent extends React.Component{
    render(){
        return (<div>hello,react</div>)
    }
}
// vue
<template>
    <div>hello, vue</div>
</template>
```
2. **CSS写法**
> React中可以使用行类样式（不推荐），也可以使用[CSS-in-JS](https://react.css88.com/docs/faq-styling.html),也可以通过`import`导入样式文件的方式来引用CSS。`Vue`则直接在`Style`标签中
```js
// react
import './mycomponent.css'
// vue
<style scoped>
    div{background:'#eee';}
</style>
```
3. **React.createElement与VueRender**
> 二者都可以手动渲染对应组件。[createElement](https://react.css88.com/docs/react-without-jsx.html)、[Render](https://cn.vuejs.org/v2/guide/render-function.html)
```js
// react
React.createElement({
     type, //'div'
  [props], // {className:'hello'}
  [...children] , //'内容'
})
render: function (createElement) {
  return createElement('p', 'No items found.')
}
```
4. ****