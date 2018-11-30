---
title: vue打包优化
date: 2018-01-10
tag: vue
categories: 前端开发
---
通常在使用Vue开发单面应用时，由于第三方库大都是在main.js中进行引用的，而webpack在打包时，会将所引用的第三方库都打包到vendor.js中，导致vendor.js过大。针对这个问题可以进行以下优化：

<!--more-->

## 优化方案
1. 使用CDN引用第三方库
2. vue-router懒加载

### CDN引用
* 可以使用的CDN很多，例如：
    1. [unpkg](https://unpkg.com/#/)就是全球好用的CDN
    ```html
    // https://unpkg.com/包名@version/ ,不加@version的话，则是默认最新版本
    <script src="https://unpkg.com/vue@2.5.2/dist/vue.min.js"></script>
    ```

* 例： 将vue和iview以CDN的方式引用到项目中

##### 步骤一:引用CDN
```html
    <script src="https://unpkg.com/vue@2.5.2/dist/vue.js"></script>
    <!-- 生产环境，改用vue.min.js -->
    <!--<script src="https://unpkg.com/vue@2.5.2/dist/vue.min.js"></script>-->
    <script src="https://unpkg.com/iview/dist/iview.min.js"></script>
    <script src="https://cdn.bootcss.com/echarts/4.2.0-rc.1/echarts.common.min.js"></script>
```
- 在引用后，默认`Vue`，`iview`是全局变量可以直接拿到，针对iview的一些修改，可以直接用`iview`变量
- 注意： `iview`使用CDN方式时，其`Form`组件应使用`i-form`，而`<FormItem>`对应`<form-item>`
- 例：调用iview的loadingBar,`iview.loadingBar.start()`

##### 步骤二：修改webpack
```js
// webpack.base.conf.js
module.exports={
    //....
    externals:{
        'vue':'Vue',
        'iview':'iView',
        'echarts':'echarts'
    },
    //...
}

```

### vue-router懒加载
* 通常我们在写router时，时以下方式

```js
import Hello from '@/components/Hello.vue'
export const router=[{
    path:'/',
    name:'home',
    component:Hello
}]
```
- 这种方式会将所有组件都打包在一些，导致打包文件很大。

##### 改进

```js
export const router=[{
    path:'/',
    name:'home',
    component:()=>import('@/components/Hello.vue')
}]
```
- 通过vue-router懒加载，你会发现打包后，会有很多js文件生成，这些都是对应不同组件，当匹配到某路由时，才会加载对应js
