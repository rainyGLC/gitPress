# Koa2开发快速入门

## 什么是koa ([文档](https://koa.bootcss.com/))
----基于Node.js平台的下一代框架(node后端框架之一)

    Koa 是一个新的 web 框架，由 Express 幕后的原班人马打造， 致力于成为 web 应用和 API开发领域中的一个更小、更富有表现力、更健壮的基石。 通过利用 async 函数，Koa帮你丢弃回调函数，并有力地增强错误处理。 Koa 并没有捆绑任何中间件， 而是提供了一套优雅的方法，帮助您快速而愉快地编写服务端应用程序。
    正因为 Koa 没有捆绑任何中间件，所以我们在开发应用的时候一定要注意自己开发，或者找 Koa 对应的中间件，并且还要注意对应的版本。

## 四条主线 
理解koa。主要需要搞懂四条主线，其实也是实现koa的四个步骤，分别是

1. 封装node http Server
2. 构造resquest,response,context对象
3. 中间件机制
4. 错误处理

在进行说明之前新建一个文件夹去初始化项目,并创建入口文件app.js

```
mkdir koaApp && cd koaApp
npm init //初始化一个文件夹

touch app.js
```

```
启动代码为
 nodemon app.js 或者 node app.js
```

### 封装node http Server:从hello world说起

首先，不考虑框架，如果使用原生http模块来实现一个返回hello world的后端app，代码如下：

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/17.png);


返回结果：
![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/18.png);

如要实现这个原生的过程进行封装，我们再新建application.js实现一个application对象

```
// application.js
let http = require('http');

class Application {

    /**
     * 构造函数
     */
    constructor() {
        this.callbackFunc;
    }

    /**
     * 开启http server并传入callback
     */
    listen(...args) {
        let server = http.createServer(this.callback());
        server.listen(...args);
    }

    /**
     * 挂载回调函数
     * @param {Function} fn 回调处理函数
     */
    use(fn) {
        this.callbackFunc = fn;
    }

    /**
     * 获取http server所需的callback函数
     * @return {Function} fn
     */
    callback() {
        return (req, res) => {
            this.callbackFunc(req, res);
        };
    }

}

module.exports = Application;
```

然后创建example.js

```
let simpleKoa = require('./application');
let app = new simpleKoa();

app.use((req, res) => {
    res.writeHead(200);
    res.end('hello world');
});

app.listen(3000, () => {
    console.log('listening on 3000');
});
```
启动代码，打开http://localhost:3000

```
nodemon example.js
```
可以看到，我们已经初步完成了对于http server的封装，主要实现了app.use注册回调函数，app.listen语法糖开启server并传入回调函数了，典型的koa风格。

但是美中不足的是，我们传入的回调函数，参数依然使用的是req和res，也就是node原生的request和response对象，这些原生对象和api提供的方法不够便捷，不符合一个框架需要提供的易用性。因此，我们需要进入第二条主线了。

### 构造request,response,context对象

如果阅读koa文档，会发现koa有三个重要的对象，分别是request, response, context。其中request是对node原生的request的封装，response是对node原生response对象的封装，context对象则是回调函数上下文对象，挂载了koa request和response对象。下面我们一一来说明。

首先要明确的是，对于koa的request和response对象，只是提供了对node原生request和response对象的一些方法的封装，明确了这一点，我们的思路是，使用js的getter和setter属性，基于node的对象req/res对象封装koa的request/response对象。


对于simpleKoa request对象，实现query读取方法，能够读取到url中的参数，返回一个对象。

对于simpleKoa response对象，实现status读写方法，分别是读取和设置http response的状态码，以及body方法，用于构造返回信息。

而simpleKoa context对象，则挂载了request和response对象，并对一些常用方法进行了代理。

首先创建request.js:

```
// request.js
let url = require('url');

module.exports = {

    get query() {
        return url.parse(this.req.url, true).query;
    }

};
```
很简单，就是导出了一个对象，其中包含了一个query的读取方法，通过url.parse方法解析url中的参数，并以对象的形式返回。需要注意的是，代码中的this.req代表的是node的原生request对象，this.req.url就是node原生request中获取url的方法。稍后我们修改application.js的时候，会为koa的request对象挂载这个req。

然后创建response.js:

```
// response.js
module.exports = {

    get body() {
        return this._body;
    },

    /**
     * 设置返回给客户端的body内容
     *
     * @param {mixed} data body内容
     */
    set body(data) {
        this._body = data;
    },

    get status() {
        return this.res.statusCode;
    },

    /**
     * 设置返回给客户端的stausCode
     *
     * @param {number} statusCode 状态码
     */
    set status(statusCode) {
        if (typeof statusCode !== 'number') {
            throw new Error('statusCode must be a number!');
        }
        this.res.statusCode = statusCode;
    }

};
```
```
也很简单。status读写方法分别设置或读取this.res.statusCode。
同样的，这个this.res是挂载的node原生response对象。而body读写方法分别设置、读取一个名为this._body的属性。这里设置body的时候并没有直接调用this.res.end来返回信息，这是考虑到koa当中我们可能会多次调用response的body方法覆盖性设置数据。真正的返回消息操作会在 application.js中存在。

```
然后我们创建context.js文件，构造context对象的原型：

```
// context.js
module.exports = {

    get query() {
        return this.request.query;
    },

    get body() {
        return this.response.body;
    },

    set body(data) {
        this.response.body = data;
    },

    get status() {
        return this.response.status;
    },

    set status(statusCode) {
        this.response.status = statusCode;
    }

};
```
可以看到主要是做一些常用方法的代理，通过context.query直接代理了context.request.query，context.body和context.status代理了context.response.body与context.response.status。而context.request，context.response则会在application.js中挂载。

由于context对象定义比较简单并且规范，当实现更多代理方法时候，这样一个一个通过声明的方式显然有点笨，js中，设置setter/getter，可以通过对象的__defineSetter__和__defineGetter__来实现。为此，我们精简了上面的context.js实现方法，精简版本如下：

```
let proto = {};

// 为proto名为property的属性设置setter
function delegateSet(property, name) {
    proto.__defineSetter__(name, function (val) {
        this[property][name] = val;
    });
}

// 为proto名为property的属性设置getter
function delegateGet(property, name) {
    proto.__defineGetter__(name, function () {
        return this[property][name];
    });
}

// 定义request中要代理的setter和getter
let requestSet = [];
let requestGet = ['query'];

// 定义response中要代理的setter和getter
let responseSet = ['body', 'status'];
let responseGet = responseSet;

requestSet.forEach(ele => {
    delegateSet('request', ele);
});

requestGet.forEach(ele => {
    delegateGet('request', ele);
});

responseSet.forEach(ele => {
    delegateSet('response', ele);
});

responseGet.forEach(ele => {
    delegateGet('response', ele);
});

module.exports = proto;
```
这样，当我们希望代理更多request和response方法的时候，可以直接向requestGet/requestSet/responseGet/responseSet数组中添加method的名称即可（前提是在request和response中实现了）。

最后让我们来修改application.js，基于刚才的3个对象原型来创建request, response, context对象：
```
// application.js
let http = require('http');
let context = require('./context');
let request = require('./request');
let response = require('./response');

class Application {

    /**
     * 构造函数
     */
    constructor() {
        this.callbackFunc;
        this.context = context;
        this.request = request;
        this.response = response;
    }

    /**
     * 开启http server并传入callback
     */
    listen(...args) {
        let server = http.createServer(this.callback());
        server.listen(...args);
    }

    /**
     * 挂载回调函数
     * @param {Function} fn 回调处理函数
     */
    use(fn) {
        this.callbackFunc = fn;
    }

    /**
     * 获取http server所需的callback函数
     * @return {Function} fn
     */
    callback() {
        return (req, res) => {
            let ctx = this.createContext(req, res);
            let respond = () => this.responseBody(ctx);
            this.callbackFunc(ctx).then(respond);
        };
    }

    /**
     * 构造ctx
     * @param {Object} req node req实例
     * @param {Object} res node res实例
     * @return {Object} ctx实例
     */
    createContext(req, res) {
        // 针对每个请求，都要创建ctx对象
        let ctx = Object.create(this.context);
        ctx.request = Object.create(this.request);
        ctx.response = Object.create(this.response);
        ctx.req = ctx.request.req = req;
        ctx.res = ctx.response.res = res;
        return ctx;
    }

    /**
     * 对客户端消息进行回复
     * @param {Object} ctx ctx实例
     */
    responseBody(ctx) {
        let content = ctx.body;
        if (typeof content === 'string') {
            ctx.res.end(content);
        }
        else if (typeof content === 'object') {
            ctx.res.end(JSON.stringify(content));
        }
    }

}

```

### 中间件的执行顺序

koa是中间件是由generator组成，这决定了中间件的执行顺序
Express的中间件是顺序执行，从第一个中间件执行到最后一个中间件，发出响应。
![images](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/15%20_2.png)
 
koa是从第一个中间件开始执行，遇到next进入下一个中间件，一直执行到最后一个中间件，在逆序，执行上一个中间件next之后的代码，一直到第一个中间件执行结束才发出响应。
![images](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/16.png)

### Koa 快速开发例子
Koa 主要的优势是 ES6 + 轻量定制化，以下我们就用 Koa 快速开发一个查询接口。
1. 初始化项目

```

cd ~/Desktop && mkdir koaApp && cd koaApp
```

```
npm init
```

2. 下载相关依赖

＊ koa
＊ koa-router
＊ axios

```
npm install koa koa-router axios --save
```

3. 创建入口主文件 app.js

```
touch app.js
```
app.js

```
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
```
const router = require('koa-router')({
  prefix: '/'
})

const indexController = require('../controllers/index.js')

router.get('/', indexController.indexRender)

module.exports = router
```

5. 创建首页控制器 controllers/index.js
```
const indexController = {
  indexRender: async (ctx, next) => {
    ctx.body = 'Hello Koa!'
  } 
}

module.exports = indexController;
```

6. 启动代码,打开http://localhost:3000/,你就可以看到，Hello Koa!

```
nodemon app.js
```

7. 第三方请求
1. 新建豆瓣数据模型 models/douban.js

models/douban.js
```
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

```

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

```
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

```
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

```
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

```
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

```
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


