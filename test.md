# HTML的简单的后台页面布局

```html
<div class="wrapper">
  <hearder class="page-header"></hearder>
  <div class="page-main">
    <div class="page-aside"></div>
    <div class="page-content"></div>
  </div>
  <footer class="page-footer"></footer>
</div>
```
```css
.wrapper{
  height:100vh;
  display:flex;
  flex-direction:column;
}
.page-header{
  padding: 20px 40px;
  text-align: right;
  font-size: 16px;
  background-color:#00adff91;
}
.page-main{
  display: flex;
  flex: 1;
  border-top: 1px solid #e4e4e4;
  border-bottom: 1px solid #e4e4e4;
}
.page-aside{
  width: 240px;
  border-right: 1px solid #e4e4e4;
  background-color:#efff0091
}
.page-content{
  flex: 1;
  padding: 0 40px;
}
.page-footer{
  padding: 10px;
  text-align: center;
  font-size: 12px;
  color: #999;
  background-color: #ff000080;
}
```
如图所示:
![Image text](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/layout.png)


## 页面垂直居中的方法
第一种:css transform

```css
.container{
  position:relative;
}
.content{
  position:absolute;
  top:50%;
  left:50%;
  width:100px;
  height:100px;
  transform:translate(-50%,-50%);
}
第二种:已知元素宽高的居中
.container{
  position:relative;
}
.content{
  position:absolute;
  top:50%;
  left:50%;
  width:100px;
  height:100px;
  margin:-50px 0 0 -50px;
}

第三种：上下左右均0定位 ＋margin:auto;
.container{
  position:relation;
}
.content{
  position:absolute;
  left:0;
  top:0;
  right:0;
  bottom:0;
  width:100px;
  height:100px;
  margin:auto
}

第四种：使用flex的居中布局
.content{
  height:100px;
  display:flex;
  align-items:center;
  justify-content:center
}//其中content是父元素，可以让子元素的位置上下左右居中
```