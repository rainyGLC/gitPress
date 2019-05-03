# koa实现get和Post请求参数获取

## 安装Koa 
1. 初始化项目

```js
cd ~/Desktop && mkdir koaApp && cd koaApp
```
2. 下载koa 

```js
npm install koa --save
```

3. 创建入口文件 app.js

```js
touch app.js
```

app.js

```js
const Koa = require('koa')
const app = new Koa()

app.use( async (ctx) => {
    ctx.body = 'hello koa or hello world!'
    console.log(ctx);
})

app.listen(3000, () => {
  console.log('ok')
})
```
4. 启动启动代码,打开http://localhost:3000/,就能看到'hello koa or hello world!'

```js
node app.js
```

## 其中ctx.body是什么？

ctx是上下文的意思，但它究竟是什么呢？为了试图搞明白，用console.log(ctx)将它输出，但是返回的内容里并没有ctx或content之类的东西。输出结果如图：

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/26.png)

ctx是context的缩写中文一般叫成上下文，这个在所有语言里都有的名词，可以理解为上(request)下(response)沟通的环境，所以koa中把他们两都封装进了ctx对象，koa官方文档里的解释是为了调用方便，
ctx.req=ctx.request,ctx.res=ctx.response，最终执行还是request和response对象

⚠️:ctx.request是context经过封装的请求对象，ctx.req是context提供的node.js原生HTTP请求对象，  同理ctx.response是context经过封装的响应对象，ctx.res是context提供的node.js原生HTTP请求对象。

具体koa2 API文档可见([文档](https://github.com/koajs/koa/blob/master/docs/api/context.md#ctxreq))

koa是Web框架，专注HTTP生命周期即同意请求到响应结束，CTX指的HTTP生命周期的上下文，
body是http协议中的响应体，（header是指响应头）
因此ctx.body = ctx.res.body = ctx.response.body(ctx.body 就是 ctx.response.body 的别名)

更详细看github上的官方文档([文档](https://github.com/koajs/koa/blob/master/docs/api/context.md))


## 获取get请求的参数

在koa中，获取GET请求数据源头是koa中request对象中的query方法或querystring方法，
query返回是格式化好的参数对象，querystring返回的是请求字符串，由于ctx对request的API有直接引用的方式，所以获取GET请求数据有两个途径。

1. 是从上下文中直接获取

* 请求对象ctx.query，返回如{a:1,b:2}
* 请求字符串ctx.querystring,返回如  a=1&b=2


2. 是从上下文的request对象中获取
* 请求对象ctx.request.query，返回如{a:1,b:2}
* 请求字符请求字符串ctx.request.querystring,返回如  a=1&b=2

### GET请求demo

app.js

```js
const Koa = require('koa')
const app = new Koa()

app.use(async(ctx) =>{
  ctx.body ={
    url:ctx.url,//获取url
    //从上下文中直接获取
    ctx_query:ctx.query,//query返回格式化的对象
    ctx_querystring:ctx.querystring, //querystring返回原字符串
    // 从上下文的request对象中获取
    req_query:ctx.request.query,//query返回格式好的对象，
    req_querystring:ctx.request.querystring//querystring返回原字符串。
  }
});

app.listen(3000, () => {
  console.log('ok')
})
```

启动 node app.js
打开localhost:3000端口可以看到显示的内容

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/27.png)


因为没有传递参数，我们可以在url中加上两个参数http://localhost:3000/?username=rainyGLC&age=23可以看到页面的内容变成了

如图:

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/28.png)

可以看到ctx_query和req_query是相同的，ctx_querystring和req_querystring也是相同的。


## post请求

对于POST请求的处理，koa2没有封装获取参数的方法。所以获取Post请求需要以下步骤：
1. 解析上下文ctx中的原生node.js对象req
2. 将POST表单数据解析成query string（例如：a=1&b=2&c=3）
3. 再将query string 解析成JSON格式（例如：{"a":"1", "b":"2", "c":"3"}）

### 解析出POST请求上下文中的表单数据
```js
// 解析上下文里node原生请求的POST参数
function parsePostData( ctx ) {
  return new Promise((resolve, reject) => {
    try {
      let postdata = "";
      ctx.req.addListener('data', (data) => {
        postdata += data
      })
      ctx.req.addListener("end",function(){
        let parseData = parseQueryStr( postdata )
        resolve( parseData )
      })
    } catch ( err ) {
      reject(err)
    }
  })
}

// 将POST请求参数字符串解析成JSON
function parseQueryStr( queryStr ) {
  let queryData = {}
  let queryStrList = queryStr.split('&')
  console.log( queryStrList )
  for (  let [ index, queryStr ] of queryStrList.entries()  ) {
    let itemList = queryStr.split('=')
    queryData[ itemList[0] ] = decodeURIComponent(itemList[1])
  }
  return queryData
}
```

### POST请求demo
app.js

```js
const Koa = require('koa')
const app = new Koa()
app.use(async(ctx)=>{
    //当请求时GET请求时，显示表单让用户填写
    if(ctx.url==='/' && ctx.method === 'GET'){
        let html =`
            <h1>Koa2 request post demo</h1>
            <form method="POST"  action="/">
                <p>userName</p>
                <input name="userName" /> <br/>
                <p>age</p>
                <input name="age" /> <br/>
                <p>webSite</p>
                <input name='webSite' /><br/>
                <button type="submit">submit</button>
            </form>
        `;
        ctx.body =html;
    //当请求时POST请求时
    }else if(ctx.url==='/' && ctx.method === 'POST'){
        let pastData=await parsePostData(ctx);
    ctx.body=pastData;
    }else{
        //其它请求显示404页面
        ctx.body='<h1>404!</h1>';
    }
})
// 解析上下文里node原生请求的POST参数
function parsePostData( ctx ) {
  return new Promise((resolve, reject) => {
    try {
      let postdata = "";
      ctx.req.addListener('data', (data) => {
        postdata += data
      })
      ctx.req.addListener("end",function(){
        let parseData = parseQueryStr( postdata )
        resolve( parseData )
      })
    } catch ( err ) {
      reject(err)
    }
  })
}

// 将POST请求参数字符串解析成JSON
function parseQueryStr( queryStr ) {
  let queryData = {}
  let queryStrList = queryStr.split('&')
  console.log( queryStrList )
  for (  let [ index, queryStr ] of queryStrList.entries()  ) {
    let itemList = queryStr.split('=')
    queryData[ itemList[0] ] = decodeURIComponent(itemList[1])
  }
  return queryData
}


app.listen(3000, () => {
  console.log('ok')
})
```
启动 node app.js，打开打开localhost:3000端口，

展现的是一个表单页面，点击提交后发现服务器接受到了post请求。

填写并提交表单后发现已经将表单数据解析成了Json格式：

如图:
![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/30.png)


### 使用中间件：koa-bodyparser

下面这是使用中间件：koa-bodyparser，来获取post请求的参数:

app.js

```js


const Koa = require('koa')
const bodyParser = require('koa-bodyparser')
const app = new Koa()

// app.use( async (ctx) => {
//     ctx.body = 'hello koa'
// })

app.use(bodyParser())

// app.use( async (ctx) => {
//     ctx.body =  ctx.request.body
// })

app.use(async(ctx)=>{
    //当请求时GET请求时，显示表单让用户填写
    if(ctx.url==='/' && ctx.method === 'GET'){
        let html =`
            <h1>Koa2 request post demo</h1>
            <form method="POST"  action="/">
                <p>userName</p>
                <input name="userName" /> <br/>
                <p>age</p>
                <input name="age" /> <br/>
                <p>webSite</p>
                <input name='webSite' /><br/>
                <button type="submit">submit</button>
            </form>
        `;
        ctx.body =html;
    //当请求时POST请求时
    }else if(ctx.url==='/' && ctx.method === 'POST'){
      //post请求
        let pastData= ctx.request.body
    ctx.body=pastData;
    }else{
        //其它请求显示404页面
        ctx.body='<h1>404!</h1>';
    }
})

app.listen(3000, () => {
  console.log('ok')
})

```

结果如图：

![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/30.png)

再与原生方式做对比，是不是很简单，直接一个ctx.request.body就可以获取到post请求到参数了
