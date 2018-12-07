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
    - [介绍](#介绍)
    - [区别](#区别)
- [VueRouter与ReactRouter](#VueRouter与ReactRouter)
- [Vuex与Redux](#Vuex与Redux)
- [Vue与React服务端渲染](#Vuex与Redux)
- [Vue与React的代码拆分](#Vue与React的代码拆分)

## Vue与React

对比项|Vue|React
---|---|---
路由管理|VueRouter|React-Router
状态管理|Vuex|Redux
动画过度|`<Transition>`|`react-transition-group`


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
4. **vue的data和react的state**
> 它们都是用于维护组件作用域内的数据
```js
// react
constructor(){
    super();
    this.state={name:'haha'} 
}
componentDidMount(){
    this.setState({name:'lala'})
}
//vue
data:{
    name:'vue'
}
mounted(){
    this.name='vueTop'
}
```
5. **事件绑定**
```js
// react
constructor(){
    super();
    this.handleClick=this.handleClick.bind(this);
}
<button onClick={this.handleClick}>点我</button>
// vue
methods:{
   handleClick(){//...} 
}
// 或者@click
<button v-on:click={this.handleClick}>点我</button>
```
6. **表单数据**
> vue中通过`v-model`实现双向绑定，react中通过value与onChange事件监听实现受控组件
```js
// react
<input value={this.state.name} onChange={()=>this.inputChange}/>
// vue
<input v-model={this.name} />
```
7. **插槽**
> vue中使用`slot`来实现子元素内容的渲染，react通过`props.children`
```js
// react
<div>
    <h3>父标题</h3>
    {props.children}
</div>
//vue
<div>
    <h3>父标题</h3>
    <template slot="child"></template>
</div>
// 对应child中定义slot
<slot name="child"></slot>
```

## VueRouter与ReactRouter
- [VueRouter文档](https://router.vuejs.org/zh/)
- [ReactRouter文档V4.3.x](https://reacttraining.com/react-router/web/guides/quick-start)

> Vue Router是`Vue.js`官方的路由管理器，
- 示例：React简单使用
```js
// react-router
import {BrowserRouter as Router,Switch, Route, Link} from 'react-router-dom';
const Index = () => <h2>Home</h2>;
const AppRouter=()=>(
    <Router>
        <ul>
            <li><Link to="/">Home</Link></li>
        </ul>
        <Switch>
            <Route path="/" exact component={Index}/>
        </Switch>
    </Router>
)

```
- vue-router简单使用
```js
// vue
const Home = { template: '<p>home page</p>' }
const About = { template: '<p>about page</p>' }
// 创建路由对象
const routes = {
  '/': Home,
  '/about': About
}
const router = new VueRouter({
  routes
})
// 引入到实例中
const app = new Vue({
  router
}).$mount('#app')
// 跳转链接
 <router-link to="/foo">Go to Foo</router-link>
```
#### 使用时的不同点总结：
- vue-router是全局配置方式，react-router是全局组件方式。
- vue-router仅支持对象形式的配置，react-router支持对象形式和JSX语法的组件形式配置。
- vue-router任何路由组件都会被渲染到<router-view/>位置，react-router子组件作为children被传入父组件，而根组件被渲染到<Router/>位置。

#### 路由懒加载
```js
// vue-router : 通过在component处使用动态import
const routes = [
    {{ path: '/foo', component: ()=> import("@/component/foo") },}
]
// react : 使用react.lazy()
const Foo = React.lazy(()=>import("./component/foo"))
<Router>
    <Route exact path="/foo" component={Foo}/>
</Router>
```