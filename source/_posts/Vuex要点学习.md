---
title: Vuex要点学习
date: 2017-06-07
tag: Vue
categories: 前端学习
---
Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。Vuex 也集成到 Vue 的官方调试工具 devtools extension，提供了诸如零配置的 time-travel 调试、状态快照导入导出等高级调试功能。
<!--more-->
## 目录
> - [安装](#安装)
> - [核心要点](#核心要点)
> - [State状态](#State状态)

## 安装
```bash
npm install vuex
```
## 核心要点
![Vuex流程图](https://vuex.vuejs.org/vuex.png)
1. `Vue Components`：Vue组件。HTML页面上，负责接收用户操作等交互行为，执行dispatch方法触发对应action进行回应。
2. `dispatch`：操作行为触发方法，是唯一能执行action的方法。
3. `actions`：操作行为处理模块。负责处理Vue Components接收到的所有交互行为。包含同步/异步操作，支持多个同名方法，按照注册的顺序依次触发。向后台API请求的操作就在这个模块中进行，包括触发其他action以及提交mutation的操作。该模块提供了Promise的封装，以支持action的链式触发。
4. `commit`：状态改变,提交操作方法。对mutation进行提交，是唯一能执行mutation的方法。
5. `mutations`：状态改变操作方法。是Vuex修改state的唯一推荐方法，其他修改方式在严格模式下将会报错。该方法只能进行同步操作，且方法名只能全局唯一。操作之中会有一些hook暴露出来，以进行state的监控等。
6. `state`：页面状态管理容器对象。集中存储Vue components中data对象的零散数据，全局唯一，以进行统一的状态管理。页面显示所需的数据从该对象中进行读取，利用Vue的细粒度数据响应机制来进行高效的状态更新。 
    - getters：state对象读取方法。图中没有单独列出该模块，应该被包含在了render中，Vue Components通过该方法读取全局state对象。
- **Vue组件接收交互行为，调用dispatch方法触发action相关处理，若页面状态需要改变，则调用commit方法提交mutation修改state，通过getters获取到state新值，重新渲染Vue Components，界面随之更新**

- 回顾补充：Readux工作流
![readux工作流](https://upload-images.jianshu.io/upload_images/5590388-304c6de2cf25067f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/645/format/webp)

#### 简单的应用实例
```js
// store
const store = new Vuex.Store({
    state:{
        count:0
    },
    mutations:{
       increment (state) {
        state.count++
        }
    }
});
// component
new Vue({
    el:'#app',
    computed:{
        count:function(){
            return store.state.count;
        }
    },
    methods:{
        increment:function(){
            store.commit('increment');
        }
    }
})
```
## State状态
Vuex 使用单一状态树：`用一个对象就包含了全部的应用层级状态`。至此它便作为一个“唯一数据源 (SSOT)”而存在。这也意味着，每个应用将仅仅包含一个 store 实例。
单一状态树让我们能够直接地定位任一特定的状态片段，在调试的过程中也能轻易地取得整个当前应用状态的快照。
#### 组件中获取状态
1. ~~原始方法：在每个组件中import Store来使用，并在组件computed属性中来获取~~
2. 全局注入应用：在根组件中注册`store`选项，该 store 实例会注入到根组件下的所有子组件中，且子组件能通过 `this.$store` 访问到。
3. mapState函数：当一个组件需要获取多个状态时候，将这些状态都声明为计算属性会有些重复和冗余。为了解决这个问题，我们可以使用` mapState 辅助函数`帮助我们生成计算属性，让你少按几次键.

```js
// 全局注入应用
import store from 'xxx';
new Vue({
    el:'#app',
    store,
    components: { Counter },
    template: `
        <div class="app">
        <counter></counter>
        </div>
    `
})
// Counter组件中，通过this.$store.state获取
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}

/* 3、mapState函数：
    三种使用方式：  1、完全映射覆盖computed（部分属性需处理） 2、完全映射   3、部分映射到属性中
*/
import {mapState} from 'vuex';
export default {
    //....
    computed:mapState({
        // 箭头函数可使代码更简练
        count: state => state.count,

        // 传字符串参数 'count' 等同于 `state => state.count`
        countAlias: 'count',

        // 为了能够使用 `this` 获取局部状态，必须使用常规函数
        countPlusLocalState (state) {
            return state.count + this.localCount
        }
    })
}
// 若要完全映射state，不需要单独处理state中的某些值，可以直接通过：传入对应状态名即可
    computed:mapState(['count','xxx']);
// 3、部分映射
computed: {
  localComputed () { /* ... */ },
  // 使用对象展开运算符将此对象混入到外部对象中
  ...mapState({
    // ...
  })
}
```

## Mutation ：改变
- 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。
- 注意：`Mutation必须是同步函数`,即在vuex中，mutation是同步事务，若要执行异步事务，请使用actions
```js
//基础示例
new Vuex.Store({
    state:{count:0},
    mutations:{
        increment:function(state){
            state.count++;
        }
    }
})
//调用
store.commit('increment');
```
#### 传入参数（提交载荷（Payload））
可以向 store.commit 传入额外的参数，即 mutation 的 载荷（payload），有两种提交参数的方式：
- 普通参数风格的提交： `store.commit('mutation_type',param)`;
- 对象参数风格的提交：`store.commit({type:'mutation_type',param:10})`
```js
// 传入参数
//...
mutations:{
    increment:function(state,n){
        state.count+=n;
    }
}
// 调用
store.commit('increment',10)
```
- 建议：多数情况下建议参数应该以对象的方式提交，这样可以包含多个字段并且记录的 mutation 会更易读：
```js
// ...
mutations:{
    increment:function(state,payload){
        state.count+=payload.count;
    }
}
//调用
store.commit('increment', {
  count: 10
})
```
#### Mutation 需遵守 Vue 的响应规则
1. 最好提前在Store中声明好所有需要用到的state
2. 当需要在对象上添加新属性时，你应该
    - 使用` Vue.set(obj, 'newProp', 123)`, 或者
    - 以新对象替换老对象：`state.obj = { ...state.obj, newProp: 123 }`

#### 使用常量替代 Mutation 事件类型
- 使用常量替代 mutation 事件类型，并用单独的文件来管理所有mutations常量，可以让你的代码合作者对整个 app 包含的 mutation 一目了然：
```js
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'

// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
    [SOME_MUTATION] (state) {
      // mutate state
    }
  }
})

//component
import {SOME_MUTATION} from './mutation-types';
export default {
    computed:{
        ...mapState({SOME_MUTATION})
    }
}
```
- 注意：`Mutation必须是同步函数`

#### 在组件中使用mutations
两种方法：
1. 通过`this.$store.commit('mutation_type')`触发
2. 通过`mapMutations`辅助函数
```js
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```

## Action
Action 类似于 mutation，不同在于：
- Action提交的是mutaion,而不是直接变更状态。
- Action 可以包含任意异步操作。
```js
// 注册一个简单的action
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    },
    increment2({commit}){
        //通过参数解构可以简化代码
        commit('increment');
    }
  }
})
```
- Action 函数接受一个与 store 实例具有相同方法和属性的` context 对象`，因此你可以调用 `context.commit` 提交一个 `mutation`，或者通过` context.state` 和 `context.getters` 来获取 `state` 和 `getters`。

#### 分发 Action[触发action]
- 通过`store.dispatch()`来触发action
> 1. 在store内触发
```js
// store.js
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}


// 1. 简单触发
store.dispatch('incrementAsync')
// 2. 以载荷形式分发(传参)
store.dispatch('incrementAsync', {
  amount: 10
})
// 3. 以对象参数形式分发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```
**一个购物车实例：**
```js
actions: {
  checkout ({ commit, state }, products) {
    // 把当前购物车的物品备份起来
    const savedCartItems = [...state.cart.added]
    // 发出结账请求，然后乐观地清空购物车
    commit(types.CHECKOUT_REQUEST)
    // 购物 API 接受一个成功回调和一个失败回调
    shop.buyProducts(
      products,
      // 成功操作
      () => commit(types.CHECKOUT_SUCCESS),
      // 失败操作
      () => commit(types.CHECKOUT_FAILURE, savedCartItems)
    )
  }
}
```

> 2. 在组件中触发Action
有两种方式在组件中分发Action: `this.$store.dispatch('action_type')`，或者`mapActions`辅助函数
```js
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```
#### 组合Action
- store.dispatch 可以处理被触发的 action 的处理函数返回的 Promise，并且 store.dispatch 仍旧返回 Promise。即store.dispatch后面可以跟`then`函数实现异步调用组合
```js
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}
// 现在你可以
store.dispatch('actionA').then(() => {
  // ...
})
// 或者
actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
        commit('someOtherMutation')
    })
}
```
- 利用`Aysn/Await`函数
```js
// 假设 getData() 和 getOtherData() 返回的是 Promise

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}
```
## Getters
Vuex 允许我们在 store 中定义“getter”（可以认为是` store 的计算属性`）。就像计算属性一样，**getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算**。
```js
// getters接收state作为第一个参数
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```
#### 访问Store的Getters
有三种访问getter的方式：
1. 通过store的属性方式访问
2. 通过store的方法方式访问
3. 通过mapGetters辅助函数访问
##### 1、通过属性方式访问
```js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    },
    // 第二个参数是getters可用来访问其他getter
    doneTodoCount:(state,getters)=>{
        return getters.doneTodos.length;
    }
  }
})
// 组件中使用
computed: {
  doneTodosCount () {
      //属性方式使用
    return this.$store.getters.doneTodosCount
  }
}
```
##### 2、方法访问
通过让 getter 返回一个函数，来实现`给 getter 传参`。在你对 store 里的数组进行查询时非常有用。
- 注意，getter 在通过方法访问时，每次都会去进行调用，而不会缓存结果。
```js
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
//使用
store.getters.getTodoById(2) // -> { id: 2, text: '...', done: false }
```
##### 3、mapGetters函数
mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：
```js
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ]),
    mapGetters({
    // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
    doneCount: 'doneTodosCount'
    })
  }
}

```
## Module模块
Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：
```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```
#### 模块的局部状态
- 在各模块内都拥有各自的state，因此其内部的actions,mutations中的state也指的是当前模块对应的state
- 对于模块内部的 action，局部状态通过 `context.state `暴露出来，根节点状态则为` context.rootState`
```js
// 
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```
#### 命名空间
> 默认情况下，模块内部的 action、mutation 和 getter 是注册在全局命名空间的——这样使得多个模块能够对同一 mutation 或 action 作出响应。所以在命名mutaion和action时要防止命名冲突。
> 如果希望你的模块具有更高的封装度和复用性，你可以通过添加 namespaced: true 的方式使其成为带命名空间的模块。当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名。
```js
const store = new Vuex.Store({
  modules: {
    account: { //account模块
      namespaced: true,

      // 模块内容（module assets）
      state: { ... }, // 模块内的状态已经是嵌套的了，使用 `namespaced` 属性不会对其产生影响
      getters: {
        isAdmin () { ... } // -> getters['account/isAdmin']
      },
      actions: {
        login () { ... } // -> dispatch('account/login')
      },
      mutations: {
        login () { ... } // -> commit('account/login')
      },

      // 嵌套模块
      modules: {
        // 继承父模块的命名空间
        myPage: {
          state: { ... },
          getters: {
            profile () { ... } // -> getters['account/profile']
          }
        },

        // 进一步嵌套命名空间
        posts: {
          namespaced: true,

          state: { ... },
          getters: {
            popular () { ... } // -> getters['account/posts/popular']
          }
        }
      }
    }
  }
})
```
##### 在带命名空间的模块内访问全局内容（Global Assets）
如果你希望使用全局 state 和 getter，rootState 和 rootGetter 会作为第三和第四参数传入 getter，也会通过 context 对象的属性传入 action。
若需要在全局命名空间内分发 action 或提交 mutation，将 { root: true } 作为第三参数传给 dispatch 或 commit
```js
modules: {
  foo: {
    namespaced: true,

    getters: {
      // 在这个模块的 getter 中，`getters` 被局部化了
      // 你可以使用 getter 的第四个参数来调用 `rootGetters`
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
      },
      someOtherGetter: state => { ... }
    },

    actions: {
      // 在这个模块中， dispatch 和 commit 也被局部化了
      // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
      someAction ({ dispatch, commit, getters, rootGetters }) {
        getters.someGetter // -> 'foo/someGetter'
        rootGetters.someGetter // -> 'someGetter'

        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        // 通过设置 {root:true}，则会在全局命名空间内分发
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
      },
      someOtherAction (ctx, payload) { ... }
    }
  }
}
```
##### 在带命名空间的模块注册全局 action
若需要在带命名空间的模块注册全局 action，你可添加 root: true，并将这个 action 的定义放在函数 handler 中。
```js
{
  actions: {
    someOtherAction ({dispatch}) {
      dispatch('someAction')
    }
  },
  modules: {
    foo: {
      namespaced: true,

      actions: {
        someAction: {
          root: true,
          handler (namespacedContext, payload) { ... } // -> 'someAction'
        }
      }
    }
  }
}
```
##### 带命名空间的绑定函数
当使用` mapState`, `mapGetters`, `mapActions` 和 `mapMutations` 这些函数来绑定带命名空间的模块时，写起来可能比较繁琐
- 通过使用` createNamespacedHelpers `创建基于某个命名空间辅助函数。它返回一个对象，对象里有新的绑定在给定命名空间值上的组件绑定辅助函数
```js
//原始：绑定带命名空间的模块
methods: {
  ...mapActions([
    'some/nested/module/foo', // -> this['some/nested/module/foo']()
    'some/nested/module/bar' // -> this['some/nested/module/bar']()
  ])
}
// 提升：使用createNamespacedHelpers
import { createNamespacedHelpers } from 'vuex'

const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')

export default {
  computed: {
    // 在 `some/nested/module` 中查找
    ...mapState({
      a: state => state.a,
      b: state => state.b
    })
  },
  methods: {
    // 在 `some/nested/module` 中查找
    ...mapActions([
      'foo',
      'bar'
    ])
  }
}
```

#### 动态注册模块
- 当在项目运行过程中，需要新增一个模块来处理Vuex中的状态时，可以用动态注册的方式来增加模块。【模块动态注册功能使得其他 Vue 插件可以通过在 store 中附加新模块的方式来使用 Vuex 管理状态】
- 如，**[vuex-router-sync](https://github.com/vuejs/vuex-router-sync) 插件就是通过动态注册模块将 vue-router 和 vuex 结合在一起，实现应用的路由状态管理。**
- 在 store 创建之后，你可以使用 `store.registerModule` 方法`注册模块`：
- 使用 `store.unregisterModule(moduleName)` 来动态`卸载模块`。
```js
/* 注册模块 `myModule`
    { preserveState: true }用于保留过去的 state，例如从一个服务端渲染的应用保留 state
*/
store.registerModule('myModule', {
  // some modules
},{ preserveState: true })
// 注册嵌套模块 `nested/myModule`
store.registerModule(['nested', 'myModule'], {
  // ...
})
```