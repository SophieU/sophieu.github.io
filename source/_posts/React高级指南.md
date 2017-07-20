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

## 代码拆分
- 类似于vue中的组件引用时加载，react也可以在引用时再加载到组件中，从而使打包的app.js被拆分为多个组件。
- 代码拆分的方法有多种，如：`CodeSpliting打包`，`react.lazy懒加载`，`基于路由进行代码拆分`
- 代码拆分可以提升首屏加载速度

#### CodeSpliting