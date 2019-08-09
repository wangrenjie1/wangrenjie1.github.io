---
title: express初识
date: 2019-06-19 15:40:34
tags: ['express','nodejs']
categories: nodejs
---
# express的基础认识和基础使用
> 一入前端深似海，只敢闷头苦学识

## 安装
新建nodeexpress文件夹 注意 这里起名字是不允许起 express的 ，中间件  暂时忽略 
```js
npm init -y
npm install express --save

//linux mac
sudo npm install express --save
```
安装 supervisor 用于热启动
```js
//安装到 全局
sudo npm install supervisor -g
```

## Start
- 新建app.js
```js
var express = require('express');
var app = express();

app.get('/',function(req,res){
    res.send("Hello world")
})
app.listen(8081,function(){
    console.log("接口已启动")
})
```
- 访问 localhost:8081 这样一个基础的网站服务器就实现了

## request&response

- request是客户端向服务端请求的信息，response是服务器向客户端返回的信息
- request常见方法
  - request.query
  - request.params.id  参数
  - [更多方法参考](http://www.runoob.com/nodejs/nodejs-express-framework.html)

- reponse常见方法
  - res.cookie(name，value [，option])：设置Cookie
  - res.get()：返回指定的HTTP头
  - res.json()：传送JSON响应
```js
app.get('/index/:id',function(req,res){
    res.json({
        id:'Hello 【' + req.params.id +'】'
    })
})
```
  - res.jsonp()：传送JSONP响应
  - res.redirect()：设置响应的Location HTTP头，并且设置状态码302
 - res.render() 

总结：
> - 安装并引用express 启用一个express 实例
> - app listen 一个端口 启动一个后台服务
>  -app.get 设置基础的路由 吐出数据

## 路由

- localhost:8081?username=admin&password=123
这样 可以通过 get 返回的 request.query.username 来获取
- 伪静态
```js
app.get('/index.html',function(req,res){
    res.send('Hello 【'+ req.query.username +'】')
})
```
- 正则表达式构建参数
```js
app.get('index/:id',function(req,res){
    res.send('Hello 【'+ req.params.id +'】')
})
```
- restful 形式的接口
同一个接口 访问的方式不同 进行不同的处理  这里只是简单的 提一嘴，关于详细的 restful 细节化的东西 还需要看书
请求方法：get 、post、put、delete等
## 引入静态文件
```js
app.use(express.static('public'));
app.get('/index.html',function(req,res){
    res.sendFile(__dirname + "/views/" + "index.html");
})
```
- 新建目录views用来存放静态资源模板
- 静态资源文件 放入public 目录下 如 css  、js
- index.html 引入index.css  index.js
- post 请求 借助中间件 body-parser 
- `npm install body-parser --save`
```js
var express = require('express');
var bodyParser = require('body-parser');
var urlencodedParser = bodyParser.urlencoded({
    extended:false
})
var app = express();

app.get('/index',function(req,res){
    res.sendFile(__dirname + '/views/index.html')
})
app.post('/index',urlencodedParser,function(req,res){
    console.log(req.body);
    res.redirect("http://www.baidu.com")//执行跳转页面
})
app.listen(8081,function(){
    console.log("接口已启动")
})
```

## 中间件

- 中间件的数据是可以传输的
```js
var express = require("express")
var app = express()
app.use(function(req,res,next){
    console.log("必经路由");
    next();
})
app.get('/index',function(req,res,next){
    req.data = 123;
    next();
},function(req,res,next){
    console.log(req.data) //这里是可以拿到上面的赋值的
    res.send("end")
})
app.listen(8081,function(){
    console.log("接口已启动")
})
```
- Express 应用可使用的中间件：
  - 应用级中间件  
> 应用级中间件绑定到app 对象使用app.use()和app.METHOD()，其中，METHOD是需要处理的HTTP请求的方法，例如GET、PUT、POST等等，全部小写。
```js
var app = express();

//没有挂载路径的中间件，应用的每个请求都会执行该中间件
app.use(function(req,res,next){
    console.log("Time:",Date.now());
    next();
})

//挂载至 /user/:id 的中间件，任何指向 /user/:id 的请求都会执行它
app.use('/user/:id',function(req,res,next){
    console.log("Request Type:",req.method);
    next();
})

//路由和句柄函数（中间件系统），处理指向/user/:id 的GET请求
app.get('/user/:id',function(req,res,next){
    res.send("USER");
})
```
在一个挂载点装载一组中间件
```js
app.use('/user/:id',function(req,res,next){
    console.log("Request URL:",req.originalUrl);
    next();
},function(req,res,next){
    console.log('Request Type:',req.method);
    next();
})

```
  - 路由级中间件
> 路由级中间件和应用级中间件一样，只是它绑定的对象为 express.Router()。与app 用法一样
> router 没有特别复杂的app里的api router->只有路由相关的api mini-app
```js
var app = express();
var router = express.Router();
router.use('/user/:id',function(req,res,next){
    console.log("Request URL:",req.originalUrl);
    next();
},function(req,res,next){
    console.log('Request Type:',req.method);
    next();
})
```
  - 错误处理中间件
> 错误处理的中间件和其他中间件类似，只是要使用4个参数，而不是三个  参数为（err, req, res, next）。
```js
app.use(function(err, req, res, next){
    console.log(err.stack);
    res.status(500).send("Something broke!")
})
```
  - 内置中间件
> express.static(root,[options])  唯一内置的中间件。它基于 serve-static ，负责在Express应用种提托管静态资源。
> 参数 root 是指体哦那个静态资源的根目录。
> [options参数](http://www.expressjs.com.cn/4x/api.html#express.static)

  - 第三方中间件
例如：
`npm install cookie-parser`
```js
var express = require("express");
var app = express();
var cookieParser = require('cookie-parser');

//加载用于解析 cookie 的中间件
app.use(cookieParser());
```

## 路由进阶

- 基本路由示例
```js
var express = require("express");
var app = express();

app.get('/',function(req,res){
    res.send("hello world");
})
```
- 路由方法
> 路由方法源于HTTP请求方法，和express 实例相关联。
```js
// get method
app.get('/',function(req,res){
    res.send('GET request to the homepage');
})
app.post('/',function(req,res){
    res.send('POST requeset to the homepage');
})
```
- 使用多个回调函数处理路由
```js
app.get('/index/add', function (req, res, next) {
  console.log('the response will be sent by the next function ...')
  next()
}, function (req, res,next) {
  res.send('Hello from B!')
  next()
},function(req,res,next){
    console.log("虽然不可以继续向客户端吐数据，但是nodejs代码还是可以执行的")
})
```
- 使用回调函数数组处理路由器

```js
var cb0 = function (req, res, next) {
  console.log('CB0')
  next()
}

var cb1 = function (req, res, next) {
  console.log('CB1')
  next()
}

var cb2 = function (req, res) {
  res.send('Hello from C!')
}

app.get('/example/c', [cb0, cb1, cb2])
```

- 响应方法
[参考链接](http://www.expressjs.com.cn/guide/routing.html)
   - res.download()   提示下载文件。
   - res.end()   终结响应处理流程。
   - res.json()  发送一个json格式的响应
   - res.redirect() 重定向请求
   - res.render()  渲染视图模板
   - res.sendFile()  以八位字节流的形式发送文件 

- app.route()
```js
app.route('/')
  .get(function (req, res) {
    res.send('Get a random book')
  })
  .post(function (req, res) {
    res.send('Add a book')
  })
  .put(function (req, res) {
    res.send('Update the book')
  })
```

- express.Router

```js
var express = require('express')
var router = express.Router()

//  该路由使用的中间件 所有的路由都会走这里
router.use(function timeLog (req, res, next) {
  console.log('Time: ', Date.now())
  next()
})
// define the home page route
router.get('/', function (req, res) {
  res.send('Birds home page')
})
// define the about route
router.get('/about', function (req, res) {
  res.send('About birds')
})

module.exports = router
```

## express 错误处理

- 可以使用log4j.js
- 自行定义
```js
var express = require('express');
var app = express();

app.get('/',function(req,res,next){
    res.end("Hello world");
})
app.use(logErrors)
app.use(clientErrorHandler)
app.use(errorHandler)

function logErrors (err, req, res, next) {
  console.error(err.stack)
  next(err)
}
function clientErrorHandler (err, req, res, next) {
  if (req.xhr) {
    res.status(500).send({ error: 'Something failed!' })
  } else {
    next(err)
  }
}
function errorHandler (err, req, res, next) {
  res.status(500)
  res.render('error', { error: err })
}
app.listen(8081,function(){
    console.log("接口已启动")
})
```
- 缺省错误处理句柄
> express内置了一个错误处理句柄，它可以捕获应用种可能出现的任意错误。这个缺省的错误处理中间件将被添加到中间件堆栈的底部。
```js
function errorHandler (err, req, res, next) {
  if (res.headersSent) {
    return next(err)
  }
  res.status(500)
  res.render('error', { error: err })
}
```

## express使用模板引擎

- 新建 public 文件夹 用于存放静态文件
- 新建 views 来存放 模板
- 安装 swig  `npm install swig`
- 新建layout.html 放入 views目录下
```js
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>{% block title %}{% endblock %}</title>
  {% block head %}
  {% endblock %}
</head>
<body>
  {% block content %}{% endblock %}
</body>
</html>
```
- 新建index.html
```js
{% extends 'layout.html' %}
 
{% block title %}index {{title}} {%endblock%}
 
{% block head %}
{{title}}
{% endblock %}
 
{% block content %}
<p>This is just an awesome page.</p>
{% endblock %}
```
- 配置app.js
```js
var express = require("express");
var app = express();
var swig = require('swig');
app.set('view engine','html');
app.engine('html',swig.renderFile)
app.use(express.static("public"));
app.listen(8081,function(){
    console.log("接口已启动")
})

app.get('/',function(req,res,next){
    res.render('index',{
        title:'首页'
    })
})
```
总结：
> 大概的了解了一下express 如何使用
