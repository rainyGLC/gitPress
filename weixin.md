# 4.24要注意的知识点

## 微信小程序，跳转延时执行，定时执行
设定一个定时器，在定时到期以后执行的回调函数

```
setTime(function () {
  //要延时执行的代码
}，1000) //延时时间，这里是1秒

```

例子：

```
Okr.insertOkr(params).then(res=>{
  console.log(res);
   wx.showToast({
    title: '标记成功',
    icon: 'success',
    duration: 1500,
  })
  setTimeout(function(){
    wx.switchTab({
    url: '/pages/okr/okr'
  })
  },2000)
})
```

当执行完showToast弹出提示信息后在进行页面跳转

## 组数添加属性循环插入数据库的操作
```
keyresult=[ { title: '成就1' }, { title: '成就2' } ] 
```
在keyresult这个数组中要插入status和create_time这两个属性
在controller.js中

```
let status=0;
let create_time = new Date();
keyresults.forEach(async(data)=>{
  let title = data.title;
  await Keyresult.insert({title,status,create_time})
})
```

## 有添加的删除数据库的数据
文档例子
```
knex('accounts')
  .where('activated', false)
  .del()
Outputs:
delete from `accounts` where `activated` = false
```
eg:在model层中base基础文件中写入这个方法

```
where(params){
    return knex(this.table).where(params)
  }
```
在controller.js中引用这个方法

![images](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/15.png)







