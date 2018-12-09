---
title: Get Started with Express
date: 2018-11-03 19:15
updated: 2018-11-06 19:30
categories:
- Express
tags:
- Express

---

> 原文参考 [https://expressjs.com/en/guide/routing.html](https://expressjs.com/en/guide/routing.html) 。

[TOC]

# Routing

## 路由（Routing）
路由就是特定URL与其对应的请求处理函数的映射，一个URL可以对应多个处理函数（译注：就是一个URL可以对应一个或多个回调函数，下文将处理函数都称为回调函数）。在同一个URL对应多个回调函数的情况下，回调函数的参数就不仅仅是 `req` 和 `res`，还要增加一个 `next`，在回调函数中要在最后主动调用 `next()` 将请求交给下一个回调函数处理，比如：
```JavaScript
require('express');
app = express();
app.get('/', function(req, req, next){
    console.log('hello world');
    next();
}, function(req, res) {
    res.send('hello world');
});
```

## 路由方式（Route methods）
路由的方式来自于 HTTP 协议的请求方式，包括 GET、POST、PUT、DELETE、HEAD等，具体参考 [app.METHOD](https://expressjs.com/en/4x/api.html#app.METHOD)。
express 有个一特殊的路由方式 `all`， 他用来处理对某个URL的任意方式的请求（包括了所有的HTTP请求方式）。
```JavaScript
app.all('/secret', function(req,res,next){
    console.log('Accessing the secret session');
    next();
});
```

## 路由路径（Route paths）
路径与请求方式排列组合确定了哪些路径可以响应哪些请求。路径可以是字符串、字符串模式和正则表达式。`?+*()`这几个符号按照正则表达式规则来解析，`-.` 按照字面意思来解析。如果在路径中使用`$`符号，必须使用`[]` 扩上它来进行转义，比如 '/data/$book' 要写成 `/data/[\$]book`。
> express 使用 [path-to-regexp](https://www.npmjs.com/package/path-to-regexp) 进行路径匹配，可以查阅他的文档来确定路径的定义。[Express Route Tester](http://forbeslindesay.github.io/express-route-tester/) 是一个非常好用的工具，用来测试 Express的基本路由。路径中的查询字符串不属于路径定义的范围。
```JavaScript
// 1) 字符串匹配
app.get('/', function(req, res){
    res.send('root');
});
app.get('/hello.text', function(req, res){
    res.send('hello.text');
});

// 2) 字符串模式匹配
app.get('/ab?cd', function(req, res){
    // acd, abcd
    res.send('ab?cd');
});
app.get('/ab+cd', function(req,res){
    // abcd, abbcd,abbbcd,......
    res.send('ab+cd');
});
app.get('/ab*cd, fucntion(req, res){
    // abcd, abxcd, abRANDOMcd,ab123cd,.....
    res.send('ab*cd');
}');
app.get('/ab(cd)?e', function(req, res){
    // abe, abcde
    res.send('ab(cd)e');
});
// 正则表达式匹配
app.get(/a/, function(req, res){
    // 任意包含a的路径
    res.send('/a/');
});
app.get(/.*fly$/, function(req, res){
    // butterfly, dragonfly, not butterflyman, dragonflyman
    res.send('/.*fly$/');
});
```

## 路由参数（Route parameters）
即URL中的动态部分（译注：不是query string），保存在 `req.params` 中，名字就是路由中定义的参数名字，参数的名字只能包括ASCII字母和数字，a-zA-Z0-9； `-` 和 `.` 不作特别处理，按照字面原意处理；参数后可以用 `()` 包含正则表达式。
```
// 字符串参数
Route path: /users/:userId/books/:bookId
Request URL: http://localhost:3000/users/34/books/8989
req.params: { "userId": "34", "bookId": "8989" }
// - 与 . 不作特别解析
Route path: /flights/:from-:to
Request URL: http://localhost:3000/flights/LAX-SFO
req.params: { "from": "LAX", "to": "SFO" }
Route path: /plantae/:genus.:species
Request URL: http://localhost:3000/plantae/Prunus.persica
req.params: { "genus": "Prunus", "species": "persica" }
// 参数中增加正则表达式
Route path: /user/:userId(\d+)
Request URL: http://localhost:3000/user/42
req.params: {"userId": "42"}
```

## 路由回调（Route handler）
利用路由回调函数的 `next` 函数可以实现对某些请求的预处理，如果本回调不需要处理，可以直接调用 `next()`跳过当前回调。
```JavaScript
// 最简单的回调
app.get('/example/a', function(req,res){
    res.send('Hello from A');
});
// 多个回调处理同一个请求
app.get('/example/b', function(req, res, next){
    console.log('the response will be sent by next handler');
}, function(req,res){
    res.send('Hello form B');
});
// 回调数组处理同一个请求
var cb0 = function(req,res, next) {
    console.log('CB0');
    next();
}
var cb1= function(req, res, next) {
    consol.log('CB1');
    next();
}
var cb2 = function(req, res, next) {
    res.send('Hello from C');
}
app.get('/example/c', [cb0, cb1, cb2]);
// 回调数组与普通回调可以组合使用
var cb0 = function (req, res, next) {
    console.log('CB0')
    next()
}

var cb1 = function (req, res, next) {
    console.log('CB1')
    next()
}

app.get('/example/d', [cb0, cb1], function (req, res, next) {
    console.log('the response will be sent by the next function ...')
    next()
}, function (req, res) {
    res.send('Hello from D!')
})

```

## 响应方法（Response methon）
下面表中的函数可以向客户端发送响应，并结束一次请求响应的过程，如果不调用一下函数，客户断的请求将不会终止。
方法支持	说明
res.download()	提示客户端下载文件
res.end()	结束响应流程
res.json()	发送JSON响应
res.jsonp()	发送支持JSONP的JSON响应
res.redirect()	重定向请求
res.render()	渲染视图模板响应请求
res.send()	发送多种类型的响应
res.sendFile()	将文件作为octet stream响应发出去
res.sendStatus()	发送状态码，并将状态码的字符表示作为响应体发送

## app.route()
使用 `app.route()` 来链式创建路由回调，可以减少冗余代码和避免拼写错误。
```JavaScript
app.route('/book')
    .get(function(req, res){
    res.send('Get a random book');
    })
    .post(function(req, res){
        res.send('Add a book');
    })
    .put(function(req, res) {
        res.send('Update a book');
    });
```

## express.Router()
使用 `express.Router` 类可以创建模块化、可加载的路由回调函数。`Router` 实例是是完整的中间件和路由系统，因此它也经常被称为 "mini-app" 。
创建名为 `birds.js` 的路由文件：
```JavaScript
var express = require('express');
var router = express.Router();

// 定义此路由的中间件
router.use(function timeLog(req, res, next){
    console.log('Time:', Date.now());
    next();
});
// 定义根页面路由
router.get('/', function(req, res){
    res.send('bird home page');
});
router.get('/about', function(req,res){
    res.send('About birds');
});

module.exports = router
```
在app中载入路由模块：
```JavaScript
var birds = require('./birds')
app.use('/birds', birds);
```
这样 app 就可以处理 `/birds` 和 `/birds/about` 的请求了。

# Writing middleware for use in Express apps
## 简介（Overview）
中间件能够在一次请求响应循环中访问所有 req、res 和 next 。在中间件中调用 next 函数表示当前中间件执行成功。中间件可以：
- 执行任意代码
- 改变请求 req和响应res 对象
- 结束请求-响应循环
- 调用下一个中间件
  中间件执行完毕必须调用 next 函数，交给下一个中间件继续处理请求，如果不调用 next 函数，the request will be left hanging （译注：翻译不好这句话，自己体会吧）。


中间件对应的请求方式，比如get、post、delete等
中间件需要处理的路径
中间件回调函数定义
下一个中间件的回调函数，按照传统，称为next
此中间件的HTTP响应参数，称为res
此中间件的HTTP请求参数，称为req

## 实例（Example）
本实例包括两个中间件，一个叫做 myLogger，打印一句简单的 log；另一个叫 requestTime，打印HTTP请求的时间戳。
```JavaScript
var express = require('express');
var app = express();

app.get('/', function(req, res){
    res.send('Hello world');
});

app.listen(3000);
```

### myLogger 中间件
```JavaScript
var myLogger =  function(req, res, next) {
    console.log('LOGGED');
    // 调用 next 函数将请求交给下一个中间件处理
    next();
};
```

### requestTime 中间件
```JavaScript
var requestTime = function(req, res, next) {
    req.requestTime = Date.now();
    next();
};

app.use(requestTime);
app.get('/', function(req, res) {
    res.send(`${req.requestTime}`);
});
```

## 可配置的中间件（Configurable middleware）
在使用时，传递参数给中间件。
```JavaScript
var configurableMiddleware = function(options) {
    return function(req, res, next) {
        // whatever you do with options here
        next();
    };
};
```
你也可以把中间件单独写在单独module中，通过 `module.exports` 供外界使用。
```JavaScript
// configurableMiddleware.js
var configurableMiddleware = function(options) {
    return function(req, res, next) {
        // whatever you do with options here
        next();
    };
};

module.exports = configurableMiddleware;
```

```JavaScript
// app.js
cmw = require('./configurableMiddleware');
app.user(cmw({option1: 'o1 value'}));
```
# 使用中间件（Using middleware）
Express 自身功能很少，是依赖路由和中间件的 web 框架：一个 Express App基本上就是一个路由和中间件的集合。
如果当前中间件不想结束当前处理的请求-响应过程，它必须调用 `next()`，将请求交给下一个中间件处理。
Express App可以使用以下4类中间件：
- 应用级中间件
- 路由级中间件
- 错误处理中间件
- 内置中间件
- 三方中间件

## 应用级中间件（Application-level middleware）
使用 `app.user()` 和 `app.METHOND()` 来将中间件绑定到application中。
```JavaScript

var express = require('express');
var app = express();
// 与路径无关的中间件（middleware with no mount point）
app.user(function(req, res, next){
    console.log('middleware with no mount point');
    next();
});
// 与路径相关的中间件
// 这种多个中间件一起称为middleware sub-stack
app.user('/user/:id', function(req, res, next){
    console.log('user mounted middleware 0');
    next();
}, fucntion(req, res, next) {
    console.log('user mounted middleware 1');
});
```
调用 `next('route')` 跳过middleware stack中的剩余中间件（只对app.METHOD和router.METHOD两类方法载入的中间件生效）。

## 路由级中间件
路由级中间件与应用级中间件没有区别，只是路由级的中间件绑定到 `express.Router()` 对象。使用 `router.user()` 和 `router.METHOD()` 来绑定路由级中间件。使用 `next('router')` 跳过当前middleware substack的剩余中间件。
> 路由级中间件与应用级中间件使用方法一致，此处跳过实例代码。

## 错误处理中间件（Error-handling middleware）
> 错误处理中间件回调函数的参数是4个，必须提供4个参数的中间件回调函数来标识这是一个错误处理中间件。
```JavaScript
express = require('express');
app = express();
// 注意错误处理函数的形式（函数签名）：4个参数，第一个参数为error。
app.user(function(err, req, res, next) {
    console.error(err.stack);
    res.status(500).send('Somethind broken');
});
```
## 内置中间件（Built-in middleware）
Express 内置的中间件包括：
- `express.static`：提供静态资源供访问。
- `express.json`：解析请求中包含的JSON数据。
- `express.urlencoded`：解析请求中URL-encoded 数据。

## 三方中间件（Third-party middleware）
安装第三方中间件并载入到 Express 应用中。
```bash
npm install cookie-parser
```
```JavsScript
var express = require('express');
var cookieParser = require('cookie-parser');
app = express();
app.use(cookieParser());
```
