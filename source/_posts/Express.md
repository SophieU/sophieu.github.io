---
title: Node Web开发框架-Express
date: 2016-10-25
banner: images/deer.jpg
thumbnail: images/deer.jpg
tag: Node
categories: 后端学习
---

Express是基于`Node.js`平台的极简的经典Web开发框架。[中文官网](http://www.expressjs.com.cn/)

<!--more-->

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
- 前提：已安装`Node.js`

```bash
$ mkdir myapp
$ cd myapp

# 初始化文件[中间会出现一些询问命令按需进行]
$ npm init

# 安装
$ npm install express -S
```

## HelloWorld
- `myapp`目录下，新建`app.js`作为入口文件
- 示例：用express创建一个简单的web服务
```js
const express = require('express')
const app = express()

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(3000, () => console.log('Example app listening on port 3000!'))

```

```bash
node app.js
```
- 访问`localhost:3000`可以看到页面显示了`Hello World`，访问其他页面会出现404，这就是一个简单的web应用服务

## Express应用生成器
- express提供了用于快速生成一个web应用程序的工具`express-generator`，它可以快速创建项目骨架，并包含多个基本应用模块

```bash
# 安装
$ npm install express-generator -g
# 查看express指令
$ express -h

# 创建一个以ejs为模板引擎的应用
$ express --view=ejs myapp
# 启动应用[默认是在3000端口启动]
$ npm start
```

- 通过生成器创建的应用一般都有如下目录结构：

```
.
├── app.js
├── bin
│   └── www
├── package.json
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
│       └── style.css
├── routes
│   ├── index.js
│   └── users.js
└── views
    ├── error.pug
    ├── index.pug
    └── layout.pug
```

## Router路由

**路由**：决定了应用如何响应客户端请求，针对请求不同的URI或Path，以及不同的请求方式（get/post），会作出不同的响应
- `app`是express的实例，`METHOD`对应前端请求方式，`PATH`对应前端请求路径，`HANDLER`对应当匹配路由时的响应函数

```js
//get请求
app.get('/next',function(req,res,next){
    res.send('针对next的请求');
})
// post请求
app.post('/set',function(req,res,next){
    res.send('这是一个post请求')
})
//所有请求
app.all('/all',functino(req,res,next){
    console.log('进到了/all')
    next();
})
```
- `app.all()`比较特殊，主要用于对所有请求都加载中间件函数
- 在项目中，路由都是分模块的，如上，`index`则代表主要首页模块，在`routes/index`文件中，又有多个路由可参考写法。

#### 路由参数（parameters）
- [参考文档](http://www.expressjs.com.cn/guide/routing.html)
- 路由路径中，` ?, +, *, ()`可以正则的方式来匹配路径
- `req.params`中可以获取到通过请求`url/:param`下的参数`param`

```js
// 1、正则匹配[示例匹配abe或abcde]
app.get('/ab(cd)?e',function(req,res){
  res.send('ab(cd)?e')
})

// 2、req.params的使用
Route path: /users/:userId/books/:bookId
Request URL: http://localhost:3000/users/34/books/8989
req.params: { "userId": "34", "bookId": "8989" }

// 3、params的扩展使用，()匹配
Route path: /user/:userId(\d+)
Request URL: http://localhost:3000/user/42
req.params: {"userId": "42"}
```

#### 路由回调
- `app.get('url',callback)`：其中callback回调可以是一个函数，也可以是一个`函数构成的数组`，也可以是`数组加函数`，详见文档
- route支持链式调用
```
app.route('/book')
  .get(function (req, res) {
    res.send('Get a random book')
  })
  .post(function (req, res) {
    res.send('Add a book')
  })
```

#### Response方法

方法名|作用
---|---
res.download()|提供一个文件下载
res.end()|终止响应进程
res.json() | 返回json对向响应
res.jsonp() | 响应一个jsonp格式的json对向
res.redirect() | 重定向
res.render() | 向前端渲染一个模板
res.send() | 发送通用类型的响应
res.sendFile() | 以文件流方式发送文件
res.sendStatus() | 设置响应code，并发送响应

#### express.Router 路由模块
- 项目中，通常路由是按模块来区分的，而不是全部写在app.js中，如下：
```js
// index.js
var express = require('express');
var router = express.Router();

// 使用中间件
router.use(function timelog(req,res,next){
    console.log('Time: ', Date.now())
  next()
})
// 路由
router.get('/', function (req, res) {
  res.send('Birds home page')
})
router.get('/about', function (req, res) {
  res.send('About birds')
})


// app.js中
var index = require('./router/index.js')
app.use('/',index);
```
## 静态文件
- 为了提供诸如图像、CSS 文件和 JavaScript 文件之类的静态文件，可以使用 Express 中的` express.static `内置中间件函数。

- 示例：访问`public`目录下的所有文件

```js
app.use(epxress.static('public'))

// 若要使用多个静态资源目录，则多次调用express.static中间件函数
// '/static'设置前缀地址来访问资源
app.use('/static',express.static('public'))
app.use(express.static('files'))

// 通常的做法
app.use('/static', express.static(path.join(__dirname, 'public')))
```
## 中间件
- **中间件函数**可以处理`请求对象(req)`，`响应对象(res)`，以及`next`函数。通常中间件是用来对请求或响应进行包装和处理。
- 示例-- 添加一个 `myLogger`中间件（用于打印日志）和一个`requestTime `中间件用于展示请求时间

```js
// web应用
const express = require('express');
const app = new express();

// myLogger中间件
var myLogger = function(req,res,next){
    console.log('LOGGER')
    next();
}
app.use(myLogger); //使用中间件

// requestTime中间件
var requestTime = function(req,res,next){
    req.requestTime = Date.now();
    next();
}
app.use(requestTime); //使用中间件

app.get('/',function(req,res){
    res.send('Hello U')
})
app.listen(3000)
```
### 可配置的中间件
- 例如express的`cookieSession`中间件，可用于处理用户session
```js
// my-middleware.js
module.exports = function(options) {
  return function(req, res, next) {
    // 基于options选项来执行逻辑
    next()
  }
}

//使用
var mw= requrie('./my-middleware.js');
app.use(mw(option1:'1',option2:'2'))
```
#### 第三方中间件
- `cookie-parser`：用于处理
```
var express = require('express')
var app = express()
var cookieParser = require('cookie-parser')

// load the cookie-parsing middleware
app.use(cookieParser())
```

## 模板引擎
- 通过`app.set('view engine', 'ejs')`可设置模板引擎
- 通过`app.set('views','./views')`可设置渲染模板

```js
// 设置模板
app.get('/', function (req, res) {
  res.render('index', { title: 'Hey', message: 'Hello there!' })
})
```
## Express设置代理
- [文档](http://www.expressjs.com.cn/guide/behind-proxies.html)
- 通过`app.set('trust proxy',params)`来设置代理，params有多种可能，如下：
  - Boolean值：为`true`时，激活反向代理，默认未激活状态`false`
 
## 数据库集成
- [详见文档](http://www.expressjs.com.cn/guide/database-integration.html)

## 常见问题

#### 1. 如何处理404响应
- 在Express中，404响应不是错误，所以需要一个针对404的中间件来捕获它


```js
app.use(function (req, res, next) {
  res.status(404).send("Sorry can't find that!")
})
```
- 当然，也可以用设置好的404页面通过`res.render`的方式渲染到客户端

#### 2. 设置一个错误处理器
- express生成的项目中，默认是有捕获错误的中间件的，当然也可以自己写，如下示例：
- 在异步函数中，必须要使用`next()`来传递错误给express并捕获相应错误

```js
// 简单的错误处理中间件
app.use(function (err, req, res, next) {
  console.error(err.stack)
  res.status(500).send('Something broke!')
})

// 异步函数中的错误处理
app.get("/", function (req, res, next) {
  fs.readFile("/file-does-not-exist", function (err, data) {
    if (err) {
      next(err); // 将错误传递给express
    }
    else {
      res.send(data);
    }
  });
});

// express生成项目中，默认的处理器
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});
```
