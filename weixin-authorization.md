# 实现微信授权和通过云函数获取用户的openid 完成用户的登录功能


### 思路
1. 首先在调用用户信息的时候需要用户授权，
2. 获取用户授权设置，we.getsetting获取用户当前的授权状态
3. 定义获取用户的全局方法，打开设置界面，可调用wx.opensetting查看当前用户的授权状态
4. 如果有授权，直接调用wx.getUserInfo获取用户信息并存储在globalData中，调用云函数获取open_id，
5. 提前发起授权请求，在跳用需要授权API之前，使用wx.authorize.用户选择scope来进行授权
6. 页面在onLoad页面信息设置page.data.userInfo内
5. 用户点击授权按钮，同样调用全局getUserInfo

[参考文档](https://developers.weixin.qq.com/miniprogram/dev/api/wx.getUserInfo.html)

demo：

```
<button class="login-btn" hover-class="login-btn-hover" open-type="getUserInfo" bindgetuserinfo="onGetUserInfo" wx:if="{{!userInfo.nickName}}">
    授权登录使用
</button>
```

```app.js
App({
  onLaunch: function () {
    
    if (!wx.cloud) {
      console.error('请使用 2.2.3 或以上的基础库以使用云能力')
    } else {
      wx.cloud.init({
        traceUser: true,
      })
    }
    this.globalData = {}
  }
})
```

```
const App = getApp(); //通过getApp方法来引用全局对象

Page({
  data:{
    userInfo:{},
    logged:false
  },
  onLoad:function(){
    wx.getSetting({
      success: res => {
        if (res.authSetting['scope.userInfo']) {
          // 已经授权，可以直接调用 getUserInfo 获取头像昵称，不会弹框
          this.getOpenid();
          App.globalData.userInfo = res.userInfo
          wx.getUserInfo({
            success: res => {
              this.setData({
                userInfo: res.userInfo
              })
            }
          })
        }
      }
    })
  },
  onGetUserInfo:function(e){
    if (!this.logged && e.detail.userInfo) {
      this.setData({
        logged: true,
        userInfo: e.detail.userInfo
      })
    }
  },
  getOpenid: function() {
    wx.cloud.callFunction({
      name: 'login',
      data: {},
      success: res => {
        console.log('[云函数] [login] user openid: ', res.result.openid)
        App.globalData.openid = res.result.openid
      },
      fail: err => {
        console.error('[云函数] [login] 调用失败', err)
      }
    })
  }

})
```



