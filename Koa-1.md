# Koa2开发快速入门

## 什么是koa ([文档](https://koa.bootcss.com/))
----基于Node.js平台的下一代框架(node后端框架之一)

    Koa 是一个新的 web 框架，由 Express 幕后的原班人马打造， 致力于成为 web 应用和 API开发领域中的一个更小、更富有表现力、更健壮的基石。 通过利用 async 函数，Koa帮你丢弃回调函数，并有力地增强错误处理。 Koa 并没有捆绑任何中间件， 而是提供了一套优雅的方法，帮助您快速而愉快地编写服务端应用程序。
    正因为 Koa 没有捆绑任何中间件，所以我们在开发应用的时候一定要注意自己开发，或者找 Koa 对应的中间件，并且还要注意对应的版本。

##  安装koa2

```
mkdir koaApp && cd koaApp
npm init //初始化一个文件夹

touch app.js  //新建一个入口文件
```

### Hello world 代码


```js
//app.js
const Koa = require('koa')
const app = new Koa()

app.use( async ( ctx ) => {
  ctx.body = 'hello koa2'
})

app.listen(3000)
console.log('[demo] start-quick is starting at port 3000')

```

启动代码为
由于koa2是基于async/await操作中间件，
目前node.js 7.x的harmony模式下才能使用，所以启动的时的脚本如下：


```
 nodemon app.js 或者 node app.js 
```

访问http:localhost:3000,效果如下：

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/18.png);



## async/awit 使用

快速上手理解

```
const xhr1 = function(){
  return new Promise((resolve,reject)=>{
    setTimeout(()=>{
      const data = 1;
      resolve(data)
    },2000)
  })
}

const xhr2 = function(){
  return new Promise((resolve,reject)=>{
    setTimeout(()=>{
      const data = 2;
      resolve(data)
    },2000)
  })
}

const xhr3 = function(){
  return new Promise((resolve,reject)=>{
    setTimeout(()=>{
      const data = 3;
      resolve(data)
    },2000)
  })
}

async function xhr(){
  const data1 = await xhr1();
  console.log(data1);
  const data2 = await xhr2();
  console.log(data2);
  const data3 = await xhr3();
  console.log(data1,data2,data3);
}

xhr();
```
在chrome的console中执行结果如下
![images](https://github.com/rainyGLC/gitPress/blob/master/images/20.png);
从上面的例子可以看出async/await的特点
* 可以让异步逻辑用同步写法实现
* 最底层的await返回需要是Promise对象
* 可以通过多层 async function 的同步写法代替传统的callback嵌套




## 中间件的执行顺序

koa是中间件是由generator组成，这决定了中间件的执行顺序
Express的中间件是顺序执行，从第一个中间件执行到最后一个中间件，发出响应。
![images](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/15%20_2.png)
 
koa是从第一个中间件开始执行，遇到next进入下一个中间件，一直执行到最后一个中间件，在逆序，执行上一个中间件next之后的代码，一直到第一个中间件执行结束才发出响应。
![images](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/16.png)

### koa中间件开发和使用

1. async中间件开发

```js
/* ./middleware/logger-async.js */

function log( ctx ) {
    console.log( ctx.method, ctx.header.host + ctx.url )
}

module.exports = function () {
  return async function ( ctx, next ) {
    log(ctx);
    await next()
  }
}
```

2. async 中间件在koa@2中使用

```js
const Koa = require('koa') // koa v2
const loggerAsync  = require('./middleware/logger-async')
const app = new Koa()

app.use(loggerAsync())

app.use(( ctx ) => {
    ctx.body = 'hello world!'
})

app.listen(3000)
console.log('the server is starting at port 3000')
```

## Koa 快速开发例子
Koa 主要的优势是 ES6 + 轻量定制化，以下我们就用 Koa 快速开发一个查询接口。
1. 初始化项目

```js

cd ~/Desktop && mkdir koaApp && cd koaApp
```

```js
npm init
```

2. 下载相关依赖

＊ koa
＊ koa-router
＊ axios

```js
npm install koa koa-router axios --save
```

3. 创建入口主文件 app.js

```js
touch app.js
```
app.js

```js
// 引入 koa 框架
const Koa = require('koa');
// 引入 路由
const router = require('./routes');
// 实例化应用
const app = new Koa();

// 使用路由，监听3000 端口
app
  .use(router.routes())
  .use(router.allowedMethods())
  .listen(3000)
```

4. 新建路由

routes/index.js

```js
const router = require('koa-router')({
  prefix: '/'
})

const indexController = require('../controllers/index.js')

router.get('/', indexController.indexRender)

module.exports = router
```

5. 创建首页控制器 controllers/index.js

```js
const indexController = {
  indexRender: async (ctx, next) => {
    ctx.body = 'Hello Koa!'
  } 
}
module.exports = indexController;
```

6. 启动代码,打开http://localhost:3000/,你就可以看到，Hello Koa!

```js
nodemon app.js
```

7. 第三方请求
1. 新建豆瓣数据模型 models/douban.js

models/douban.js

```js
const axios = require('axios');
const ISBNAPI = 'https://api.douban.com/v2/book/isbn/';
// const mock_id = '9787121317989';

const douban = {
  isbn:function(isbn){
    return new Promise((resolve,reject) => {
      axios.get(ISBNAPI + isbn).then( res => {
        resolve(res.data)
      }).catch( err => {
        reject(err.response.data)
      })
    })
  }
}

module.exports = douban;
```

2. 新建书目控制器 controllers/book.js

controllers/book.js

```js

const doubanModel = require('./../models/douban');

const book = {
  info:async function(ctx, next){

    const ISBN = ctx.query.isbn;
    if(!ISBN){
      ctx.body = { code: 0,msg: 'isbn empty!'}
      return
    }

    try{
      const data = await doubanModel.isbn(ISBN);
      ctx.body = { code: 200, data: data }
    }catch(err) {
      ctx.body = err
    }
  }
}

module.exports = book;
```

3. 添加接口 routes/index.js

```js
const router = require('koa-router')({
  prefix: '/'
})

const indexControllers = require('../controllers/index.js')
const bookController = require('./../controllers/book');

router.get('/', indexControllers.indexRender)
router.get('api/isbn', bookController.info);

module.exports = router
```

4. 请求测试 http://localhost:3000/api/isbn?isbn=9787121317989


8. CORS中间件

Koa的机制和 Express 不一样的地方是，Express是一个流式，从上到下。而 Koa 的机制是洋葱形，中间件会有两次执行的时机。以下我们将创建全局中间件和局部的中间件，全局中间件有点类似 Express 的 filter作用，而局部中间件和 Express 的中间件相识，但是在 next 的时候需要添加 await。

1. middlewares/response.js

```js
const debug = require('debug')('koa-app')

/**
 * 响应处理模块
 */
module.exports = async function (ctx, next) {
  try {
    await next()

    // 处理响应结果
    // 如果直接写入在 body 中，则不作处理
    // 如果写在 ctx.body 为空，则使用 state 作为响应
    // ctx.body = {
    //     code:0,
    //     data:{}
    // }
    console.log(ctx.url)
    ctx.body = ctx.body ? ctx.body : {
      code: ctx.state.code !== undefined ? ctx.state.code : 0,
      data: ctx.state.data !== undefined ? ctx.state.data : {}
    }
  } catch (e) {
    // catch 住全局的错误信息
    debug('Catch Error: %o', e)
    // 设置状态码为 200 - 服务端错误
    ctx.status = 200

    // 输出详细的错误信息
    ctx.body = {
      code: -1,
      error: e && e.message ? e.message : e.toString()
    }
  }
}
```

2. 创建CORS中间件 middlewares/cors.js

```js
const cors = {
    allowAll: async function(ctx, next){
        ctx.set('Acess-Control-Allow-Origin','*');
        ctx.set('Access-Control-Allow-Origin', '*');
        ctx.set('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
        ctx.set('Access-Control-Allow-Credentials', 'true');
        await next()
    }
}

module.exports = cors;
```

3. 应用全局response中间件 app.js

```js
const Koa = require('koa');
const router = require('./routes');
const app = new Koa();
const response = require('./middlewares/response');

app
  .use(response)
  .use(router.routes())
  .use(router.allowedMethods())
  .listen(3000)
```

4. 引用CORS局部中间件 routes/index.js

routes/index.js

```js
const router = require('koa-router')({
  prefix: '/'
})

const indexControllers = require('../controllers/index.js');
const bookController = require('./../controllers/book');
const cors = require('./../middlewares/cors');

router.get('/', indexControllers.indexRender);
router.get('api/isbn', cors.allowAll,bookController.info);

module.exports = router
```


