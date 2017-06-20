---
title: nodemon-解决开发时无限npm start的痛点
date: 2017-10-25
tag: Node
categories: 后端学习
---
- 刚开始学习Node时，每次做了修改，都要重新`npm start`启动服务才能查看上次修改结果，而`nodemon`就是为了开发node时进行热更新的一款工具
<!--more-->

## 目录
> 
>> -[安装](#安装)
>> -[配置nodemon](#配置nodemon)

## 安装
- 先全局安装，再以开发依赖形式安装

```bash
# 全局安装
npm install nodemon -g
# 依赖安装
npm install nodemon -D
```

## 配置nodemon
- 新建`nodemon.json`文件默认为nodemon的配置文件，从而进行自定义启动配置
- 也可以直接在`package.json`中新增`nodemonConfig`字段进行配置

```json
{
    "restartable":"rs",
    "verbose": true,
    "ignore":[
        ".git",
        ".svn",
        "node_modules/**/node_modules"
        ],
    "execMap": {
        "js": "node --harmony"
    },
    "env":{
        "NODE_ENV": "development"
    }
}
```
> **配置项说明**：
> 属性|说明
> ---|---
> restartable|设置重启模式
> verbose|设置日志输出模式，true为详细模式
> ignore|设置忽略文件
> execMap|设置运行服务的后缀名与对应的命令
> watch|监听哪些文件的变化，当变化时行动重启
> ext|监控指定的后缀文件名

```json
"execMap":{
    "js":"node -harmony"
}
```
- 表示使用`nodemon`代替node

## 启动服务
- nodemon './bin/www.js'