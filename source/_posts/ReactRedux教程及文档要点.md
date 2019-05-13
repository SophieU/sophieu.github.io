---
title: ReactRedux教程及文档要点
date: 2017-12-11 11:17:24
tags: React
categories: 前端学习
---
Redux 是 JavaScript 状态容器，提供可预测化的状态管理。它以让你构建一致化的应用，运行于不同的环境（客户端、服务器、原生应用），并且易于测试。参考文档：[Redux中文文档](https://www.redux.org.cn/)，[官方文档](https://redux.js.org/)

Redux 除了和 React 一起用外，还支持其它界面库。 它体小精悍（只有2kB，包括依赖）。实际上，你并不一定需要使用到Redux,参考文章:[ “You Might Not Need Redux”](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)
<!-- more -->
## 目录
> - [安装](#安装)
> - [核心要点](#核心要点)
> - [Action](#Action)
> - [Reducer](#Reducer)
> - [Store](#Store)
> - [搭配React使用](#搭配React使用)

## 安装
- 多数多数情况下，你除了安装`redux`外，还需要使用 [React 绑定库](http://github.com/gaearon/react-redux)和[开发者工具](http://github.com/gaearon/redux-devtools)。
```bash
$ yarn add redux
# 安装react绑定库及开发工具
$ yarn add react-redux 
$ yarn add redux-devtools --dev
```

## 核心要点
> Redux是将整个应用状态存储到一个叫`store`的对象上,里面保存着一个状态树store tree,整个app内部的组件都订阅了`store`中的状态`state`来对应自己的视图，用户产生`action`传递给`store`，`store`找到对应`reducer`来处理此行为action，并返回新的`state`到store中，从而影响了订阅store的组件的视图。
1.  组件产生行为：Component-dispatch-action
2. store为action找到对应处理函数reducer
3. reducer返回新的state到store
4. 因为组件订阅了store,所以当store发生变化时，组件视图也会发生相应变化。
- 组件可以`派发(dispatch)`用户的`行为(action)`给store,而不是直接通知其他组件，组件内部通过订阅store中的状态state来刷新自己的视图。
- Redux工作流：
![Redux工作流](https://upload-images.jianshu.io/upload_images/6548744-df461a22f59ef7da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800/format/webp)

#### redux三大原则
1. **唯一数据源**：整个应用的state都被存储在一个状态树中，而这个状态树由`store`对象管理
2. **操持只读状态**：state是只读的，唯一改变state的方法就是触发action，*`action`是一个用于描述以发生时间的`普通对象`*
3. **数据改变只能通过纯函数来执行** : 使用纯函数(`reducer`)来执行修改，为了对action做出响应并改变对应state，你需要编写`reducers`

#### 关键词
- `store` ： 全局state树，视图的更新都依赖于store
- `action`:把数据从应用传到store的JS对象，通过`store.dispatch()`将action传到store，通常用一个单独的js来传递应用中的action。
- `reducer` ： 根据不同的action做出相应处理并返回新的state

 以下是一个不规范的简单使用redux示例：

```js
/* store.js 统一的state管理*/
import {createStore} from 'redux';
// reducer
function counter(state=0,action){
    switch(action.type){
        case 'INCREMENT':
            return state +1;
        case 'DECREMENT':
            return state -1;
        default:
            return state
    }
}
let store = createStore(counter);
//actions
export const INCREMENT={type:'INCREMENT'};
export const DECREMENT = {type:'DECREMENT'};
export default store;

/*Component.js*/
import React from 'react';
import store,{INCREMENT,DECREMENT} from './redux/index'
// 通过 store.getState()取值，这里目前只考虑了只有一个state的情况
function Component (props){
    return (
        <Router>
            <div>
               <div>这是counter: {store.getState()}</div>
                <div>
                    <button onClick={()=>store.dispatch(INCREMENT)}>加</button>
                    <button onClick={()=>store.dispatch(DECREMENT)}>减</button>
                </div>
            </div>
        </Router>
    )
}
export default Component;

/*index.js ：统一渲染的index.js*/
import React from 'react';
import ReactDOM from 'react-dom';
import Component from './Component';
import store from './redux/index';
//首次渲染
ReactDOM.render(<App />, document.getElementById('root'));
// 通过store.subscribe订阅store，才能让store中的state变化时能及时更新到视图上
store.subscribe(function(){
    ReactDOM.render(<App />, document.getElementById('root'));
});

```
> 总结：
> - `action`通常长这样儿：`{type:'INCREMENT'}`，通常type对应的是一串大写的字符串
> - 通过`createStore(reducer)`来创建store对象,这样就能把reducer中对应的state传入到store中来统一管理
> - 通过`state.getState()`来获取state的值，通过`store.subscribe(fn)`来订阅store，传入reactDOM.render即为更新视图
> - 通过`store.dispatch(action)`来向store传递action，从而改变对应的state

## Action
> Action 是把数据从应用传到 store 的有效载荷。它是 store 数据的唯一来源。一般来说你会通过 `store.dispatch()` 将 action 传到 store。本质为一个普通的JS对象。

> 我们约定，action 内必须使用一个字符串类型的 type 字段来表示将要执行的动作。多数情况下，type 会被定义成字符串常量。当应用规模越来越大时，建议使用单独的模块或文件来存放 action。`import { ADD_TODO, REMOVE_TODO } from '../actionTypes'`

Action的创建有以下几种方式：
- 定义JS对象的方式：`{type:'ADD'}`
- 通过`创建函数`来创建
```js
// 如下就是一个单独的action.js文件的常见内容
/*
 * action 类型
 */

export const ADD_TODO = 'ADD_TODO';
export const TOGGLE_TODO = 'TOGGLE_TODO'
export const SET_VISIBILITY_FILTER = 'SET_VISIBILITY_FILTER'
/*
 * 其它的常量
 */
export const VisibilityFilters = {
  SHOW_ALL: 'SHOW_ALL',
  SHOW_COMPLETED: 'SHOW_COMPLETED',
  SHOW_ACTIVE: 'SHOW_ACTIVE'
}
/*
 * action 创建函数
 */
export function addTodo(text) {
  return { type: ADD_TODO, text }
}
export function toggleTodo(index) {
  return { type: TOGGLE_TODO, index }
}
export function setVisibilityFilter(filter) {
  return { type: SET_VISIBILITY_FILTER, filter }
}

```

## Reducer
**Reducers**指定了应用状态的变化如何响应 actions 并发送到 store 的，记住 actions 只是描述了有事情发生了这一事实，并没有描述应用如何更新 state。
- reducer 就是一个**纯函数**，接收旧的 state 和 action，返回新的 state。
- 永远不要在reducer内部做以下事：
    - 修改传入参数：
    - 执行有副作用的操作，如API请求和路由跳转
    - 调用非纯函数，如Date.now()或Math.random();

以下是TODO示例中reducers.js的代码:   
```js
import {combineReducers} from 'redux';
import{
    ADD_TODO,
    TOGO_TODO,
    SET_VISIBILITY_FILTER,
    Visibility,
} from "./actions";
const {SHOW_ALL} = Visibility;

function visibilityFilter(state=SHOW_ALL,action){
    switch (action.type) {
        case SET_VISIBILITY_FILTER:
            return action.filter;
        default:
            return state
    }
}

function todos(state=[],action){
    switch(action.type){
        case ADD_TODO:
            return [
                ...state,
                {
                    text:action.text,
                    completed:false
                }
            ]
        case TOGO_TODO:
            return state.map((todo,index)=>{
                if(index===action.index){
                    return Object.assign({},todo,{
                        completed: !todo.completed
                    })
                }
                return todo
            });
        default:
            return state;
    }
}

const todoApp = combineReducers({
    todos,
    visibilityFilter
});

export default todoApp;

```
#### 总结：
- 通过`combineReducers`来将多个单一的reducer整合在一起导出
- reducer是纯函数
- reducer的函数名其实就对应了最后`store.getState()`得到的state的各个属性
- 通过`(state=xxx,action)`可以设置reducer对应的state的默认值。

## Store
`store`就是把`action`和`reducers`联系在一起的总的state 树对象。它的职责如下：
- 给的应用的`state`;
- 提供`getState()`方法用于获取state;
- 提供`dispatch(action)`方法用于更新state;
- 通过`subscribe(listenerFunc)`注册监听器用于监听state的变化，其中的函数会在state发生变化时调用
- `subscribe(listenerFunc)`的返回值就是一个函数注销监听器。

示例：一个TODO应用中的store.js
```js
// store.js
import {createStore} from 'redux';
import todoApp from './reducers';
let store = new createStore(todoApp);
export default store;

// index.js
import store from './redux/store';
store.subscribe(()=>{
    ReactDOM.render(<App />, document.getElementById('root'))
});
// App.js
import React from 'react';
import store from './redux/store';
import {addTodo,toggleTodo,setVisibilityFilter} from './redux/actions';
class App extends React.Component{
    constructor(){
        super();
        this.addInput=React.createRef();
    }
    addTodo=()=>{
        let addInput = this.addInput.current;
        let todoText = addInput.value;
        store.dispatch(addTodo(todoText));
        addInput.value='';
    };
    toggleTodo=(e)=>{
        let index = parseInt(e.target.dataset.index);
        store.dispatch(toggleTodo(index));
        console.log(index)
        console.log(typeof index)
    };
    toggleVisible=(e)=>{
        let visible = e.target.dataset.visible;
        store.dispatch(setVisibilityFilter(visible));
    };
    render(){
        let state = store.getState();
        let todos = state.todos;
        let visibilityFilter = state.visibilityFilter;
        console.log(state)
        return (
            <div>
                <input ref={this.addInput} type="text" className="input-todo"/>
                <button onClick={this.addTodo}>add TODO</button>
                <ul className="todo-list">
                    {
                        todos.map((todo,index)=>{
                            return (<li key={index} data-index={index} onClick={this.toggleTodo} className={todo.completed?'finished':''}>{todo.text}</li>)
                        })
                    }
                </ul>
                <div className="toggle-todo">
                    <span data-visible="SHOW_ALL" onClick={this.toggleVisible} className={visibilityFilter==='SHOW_ALL'?'active':''}>显示全部</span>
                    <span data-visible="SHOW_COMPLETED" onClick={this.toggleVisible} className={visibilityFilter==='SHOW_COMPLETED'?'active':''}>已完成</span>
                    <span data-visible="SHOW_ACTIVE" onClick={this.toggleVisible} className={visibilityFilter==='SHOW_ACTIVE'?'active':''}>未完成</span>
                </div>
            </div>
        )
    }
}
export default App;
```
- 以上结合action.js,reducer.js,store.js等就构成了一个完整的TODO应用。
#### 总结
- 通过`createStore(reducers,defaultValue)`来创建一个store,其中参数`defaultValue`是可选的，用于设置 state 初始状态。【这对开发同构应用时非常有用，服务器端 redux 应用的 state 结构可以与客户端保持一致, 那么客户端可以将从网络接收到的服务端 state 直接用于本地数据初始化。】
- 在组件中，通过`store.getState()`来获取整个state
- 通过`store.dispatch(action)`来发起action

## 搭配React使用
Redux 和 React 之间没有关系。Redux 支持 React、Angular、Ember、jQuery 甚至纯 JavaScript。只是说，Redux与React搭配也是很好用的。
#### 安装
- redux默认并不包含[React 绑定库](https://github.com/reactjs/react-redux),因此要同时安装`redux`和`react-redux`
- 可以通过`yarn add react-redux`安装，也可以通过`npm`，还可以从unpkg获取最新的UMD包（包括[开发环境包](https://unpkg.com/react-redux@latest/dist/react-redux.js)和[生产环境包](https://unpkg.com/react-redux@latest/dist/react-redux.min.js)）。
- 通过`<script>`引入UMD包会在全局抛出`window.ReactRedux`对象。
```bash
$ yarn add react-redux
```

#### 关键词
- `<Provider/>`：通常用于包裹`App`组件。用于外层包裹组件，通过设置属性`store=store`,来将store关联到整体组件中
- `connect` : 用于各react子组件内部，通过`connect(mapStateToProps,mapDispatchToProps)(Component)`例各子组件与 store建立联系
- `mapStateToProps` : 【字面理解】-将State映射到组件Props中，从而可以直接使用`this.props.xxx`来直接访问store中的state
- `mapDispatchToProps` : 【字面理解】-将Dispatch映射到组件Props中，从而可以直接使用`this.props.dispatch(action)`来达到`store.dispatch`的同等效果。

#### 对上面的TODO进行改造
```js
// index.js中，去掉store.subscribe()而使用Provider组件
ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root'));

// App.js  
// App组件中的代码段
toggleVisible=(e)=>{
    let visible = e.target.dataset.visible;
    this.props.setVisibilityFilter(visible);
};
 render(){
        let todos = this.props.todos;
        let visibilityFilter =  this.props.visibilityFilter;
        return(/*....*/)
 }

// mapStateToProps,mapDispatchToProps是自己手动映射props的函数
const mapStateToProps = state=>{
    return {
        todos:state.todos,
        visibilityFilter:state.visibilityFilter,
    }
};
const mapDispatchToProps = dispatch=>{
    return {
        toggleTodo:index=>{
            dispatch(toggleTodo(index))
        },
        addTodo:text=>{
            dispatch(addTodo(text))
        },
        setVisibilityFilter:filter=>{
            dispatch(setVisibilityFilter(filter))
        }
    }
}

const AppStore = connect(mapStateToProps,mapDispatchToProps)(App)
export default AppStore;
```

## 和ReactRouter搭配使用
- 参考使用这个库：[connected-react-router](https://github.com/supasate/connected-react-router)，原`react-router-redux`作者已经不再维护了。

## 一些中间件
- [redux-saga](https://github.com/redux-saga/redux-saga): 一个用来处理异步任务的redux中间件。
    > 在 reducers 中的所有操作都是同步的并且是纯粹的，即 reducer 都是纯函数，纯函数是指一个函数的返回结果只依赖于它的参数，并且在执行过程中不会对外部产生副作用，即给它传什么，就吐出什么。但是在实际的应用开发中，我们希望做一些异步的（如Ajax请求）且不纯粹的操作（如改变外部的状态），这些在函数式编程范式中被称为“副作用”。 而`redux-saga`就是用来做这些异步操作的
- [redux-thunk](https://github.com/reduxjs/redux-thunk):也是一个异步中间件，是redux作者写的。特点：action可以是一个以dispatch为参数的函数，在函数内部做异步操作。缺点：要redux本身的action为对象的概念冲突，导致类型不统一

