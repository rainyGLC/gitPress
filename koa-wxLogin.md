# 实现微信小程序的用户登录

本项目为 Node.js 构建 Web API，并使用微信小程序构建前台工具。主要考察对微信小程序项目中的应用能力，分成以下两个部分：

Node.js API 的运用，结合 Koa2 框架输出应用的 API 接口
微信小程序的基本运用，框架、组件以及 API 的运用能力


## 用户登录思路:

项目结构：![Image text](32.png);



1. 在欢迎页面中，检测本地存储是否有用户识别 token，如果有自动跳转到代办页面。
2. 如果没有，当用户点击登录按钮时候，调取 wx.login 接口获取 code，发送到服务器的登录 API，
3. 登录 API 接受到 code 后向微信服务器发送请求获取用户 open_id 并存进数据库返回 user_id。
4. 可以使用加密方法将 user_id 进行加密返回给客户端。
5. 在客户端发送请求时，如果已经登录的用户，在请求头 headers 中带上 token 字段。
6. 服务器新建一个判断用户的中间件，获取到请求时如果请求头上有 token 字段，
对 token 字段进行解密获取到其 user_id ，并写一个测试接口进行测试，成功后返回 user_id 。

实现步骤：
1. 完成用户登录api:POST/api/login
2. 创建中间件middlewares/user.js,获取ctx.headers中的token字段进行解密，解密后返回用户id存放在ctx.state.user_id中进行备用
4. 使用testAPI进行测试，登陆后请求testAPI返回该用户的id;


## 登录流程
应用小程序官方文档的登录流程图，整个登录流程基本如下图所示：

![Image text](31.png);


* 前端调用wx.login(),获取临时登录凭证code
* 通过wx.request()将code发给服务器（需要后端创建接口接收code）
* 后端进行登录凭证校验，入参为(appid,secret,js_code,grant_type)
(
appid 小程序唯一标识
secret 小程序的 app secret
js_code 登录时获取的 code
grant_type 填写为 authorization_code
)

微信客户端获取code和服务器请求获取用户open_id大致讲解，如图所示:
weixinClient/pages/welcome.js中

![Image text](33.png);

koaApp/controllers/login.js


![Image text](34.png);


下面进行全局配置和路由封装（即MVVC模式）
在微信客户端新建api.js、request.js、user.js文件


request.js

```
const requset = function(params={}){
  let token = wx.getStorageSync('token');
  params.headers = {}
  if(token){
    params.headers.token = token
  }//如果已经登录的用户，在请求头headers中带上 token 字段,把token存放进headers中

  return new Promise((resolve, reject)=>{
    wx.request({
      url: params.url,
      method: params.method || 'get',
      data: params.data || {},
      header: params.headers,
      success: (res)=>{
        if(res.data.code == 200){
          resolve(res.data)
        }else{
          reject(res.data)
        }
      },
      fail: (err) => {
        reject(err)
      }
    })
  })
}

module.exports = requset
```

request.js封装公共方法



api.js
```
module.exports = {
  login:'http://localhost:3000/api/login',
  test:'http://localhost:3000/api/test',
}
```

user.js
```
const request = require('./request.js')
const api = require('./api.js')

module.exports =  {
  login(code) {
    return request({
      // url:'http://localhost:3000/api/login',
      url: api.login,
      method:'post',
      data: { code }
    })
  },
  test() {
    return request({
      // url: 'http://localhost:3000/api/test',
      url:api.test
    })
  }
}
```
user.js为微信客户端的请求方法


 在pages/welcome.js中引用user.js方法
```
const request = require('./../../models/request.js')
const User = require('./../../models/user.js')


Page({
  data: {

  },
  onLoad:function(options){
    let token = wx.getStorageSync('token');
    console.log(token,'ooop');
    if(token){
      wx.switchTab({
        url: '/pages/todo/todo'
      })
    }
  },//主要作用为当前页面刷新时，用户有登录并存在token，在刷新当前页面
  startLogin:function(){
    wx.login({
      success(res) {
        if(res.code) {
          User.login(res.code).then(res => {
            // console.log(res.data);
            let token = res.data.token;
            wx.setStorageSync('token',token)
            wx.switchTab({
              url: '/pages/todo/todo'
            })
          })
        }else {
          console.log('登录失败！'+ res.errMsg)
        }
      }
    })
  }
})
```

在后台也要进行分离封装，为了更方便于修改代码和排错

引用路由routes/api.js
```
const apirouter = require('koa-router')({
  prefix:'/api'
})

const loginController = require('../controllers/login.js');


apirouter.post('/login', loginController.login);


module.exports = apirouter
```


在koaApp/models/weixin.js中

```
const configs = require('./../config.js');
const axios = require('axios');
const APPID = configs.miniapp.appid;
const SECRET = configs.miniapp.secret;


const LOGINAPI = function(APPID,SECRET,JSCODE){
  return `https://api.weixin.qq.com/sns/jscode2session?appid=${APPID}&secret=${SECRET}&js_code=${JSCODE}&grant_type=authorization_code`
}

class Weixin{
  code2Session (code) {
    let api_url = LOGINAPI(APPID,SECRET,code);
    return axios.get(api_url)
  }
}

module.exports = new Weixin()
```



在koaApp/controllers/login.js中引用weixin.js中的code2Session方法
```
const authCode = require('./../utils/authCode.js');
const { formateDay } = require('./../utils/date.js'); 
const UserModel = require('./../models/user.js');
const User = new UserModel();
const Weixin = require('./../models/weixin.js');


const loginControll = {
  login: async (ctx,next) =>{
    let code = ctx.request.body.code;
    if(!code) {
      ctx.state.body ={code:0,message:'code empty!'}
    }

    let weixinRequest = await Weixin.code2Session(code);
    let weixinData = weixinRequest.data;
    let open_id    = weixinData.openid;
    console.log(open_id);



    const users = await User.select({open_id});
    const user = users[0];
    // console.log(user,'ooo');
    let id;
    if(!user){
        const userArr = await User.insert({open_id});
        id = userArr[0];
    }else{
        id = user.id;
    }
    // console.log(id);
    let create_time = formateDay(new Date());
    let auth_Code = 'rainy' +'\t' + create_time + '\t' + id;
    let token = authCode(auth_Code,'ENCODE');
    // console.log(token);
    ctx.state.code = 200;
    ctx.state.data={taken:token}
  }
} 
module.exports = loginControll;
```
代码demo:(https://github.com/rainyGLC/reconsitution-okr)





































