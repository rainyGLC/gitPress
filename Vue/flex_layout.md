# flex:1和flex:none的布局

首先先了解一下flex的属性：
flex属性为flex-grow、 flex-shrink和flex-basis 的简写，默认值为0 1 auto.后面两个属性可选。
其中：

* flex-grow属性定义项目为放大比例，默认为0,即如果存在剩余空间，也不放大。
* flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
* flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间


```
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。


用flex属性写出如图所示的布局：

![images](36.png)

1. 假设图中的大方框为item， 此item为两列布局，可以使用flex，然后添加适当的 padding 和底部边框
2. 设置item的两侧，左侧为固定，右侧为自适应
3. 设置左侧的方框大小宽高和背景色，再加一点右边的外边距和右侧栏目有点距离
4. 设置下方的布局为flex两边两边对其，上外边距，及其字体大小和颜色

demo

```html
 <div class="item">
    <div class="item-right"></div>
    <div class="item-left">
      <div class="name">昵称</div>
      <div class="content">内容</div>
      <div class="resource">资源</div>
      <div class="info">
        <div  class="info-data">2019-05-09</div>
        <div class="info-number">回复</div>
      </div>
    </div>
  </div>
```

```css
    .item{
      display: flex;
      padding: 15px 10px;
      width: 250px;
      height: 250px;
      border:1px solid #e4e4e4;
    }
    .item-right{
     flex:none;
     width: 40px;
     height: 40px;
     margin-right: 10px;
     background-color: #f5f5f5; 
    }
    .item-left{
      flex:1;
    }
    .item-left .name{
      font-size: 14px;
      color:#606686;
    }
    .content{
      font-size: 14px;
      color: #333;
      margin-bottom: 10px;
    }
    .resource{
      width: 100px;
      height: 100px;
      background-color: #f5f5f5;
    }
    .info{
      display: flex;
      justify-content: space-between;
      margin-top:10px;
      font-size: 10px;
      color: #999;
    }
```


 demo 图如下：

![images](37.png)


实例demo，实现小程序发布样式如图

![images](35.png);


* 我们分别为 文字、图片、视频 的内容展现方式创建三个话题。
* 话题分为左右两栏，左边为头像，右边为主题内容。
* 信息包含用户昵称、文字、资源 和 信息。
* 资源中包含图片或者视频，或者两者都没有。
* 信息包含显示日期和回复数量
* 先创建仅仅有文字，没有资源的话题结构，在创建话题项的时候我们使用 navigator 标签，这样点击话题项，就会跳转到详情页面。
* 复制，修改为只有图片资源，没有文字和视频的结构
* 复制，修改为只有视频资源，没有文字和图片的结构，视频元素内另外放置一个 cover-view 作为控制器元素。



```wxml
<view class="page-container">
  <view class="topics-list">
    <!-- 先创建仅仅有文字，没有资源的话题结构 -->
    <navigator class="topics-item" url="/pages/detail/detail">
      <image class="topics-hd topics-avatar" src=""></image>
      <view class="topics-bd">
        <view class="topics-nickName">昵称</view>
        <view class="topics-content">内容</view>
        <view class="topics-resource"></view>
        <view class="topics-info">
          <text class="topics-reply-date">2018-11-11</text>
          <text class="topics-reply-number">200 回复</text>
        </view>
      </view>
    </navigator>
    <!-- 复制，修改为只有图片资源，没有文字和视频的结构 -->
    <navigator class="topics-item" url="/pages/detail/detail">
      <image class="topics-hd topics-avatar" src=""></image>
      <view class="topics-bd">
        <view class="topics-nickName">昵称</view>
        <view class="topics-content"></view>
        <view class="topics-resource">
          <image class="resource-item" src="" mode="widthFix"></image>
        </view>
        <view class="topics-info">
          <text class="topics-reply-date">2018-11-11</text>
          <text class="topics-reply-number">200 回复</text>
        </view>
      </view>
    </navigator>
    <!-- 复制，修改为只有视频资源，没有文字和图片的结构，视频元素内放置一个 cover-view 作为控制器元素 -->
    <navigator class="topics-item" url="/pages/detail/detail">
      <image class="topics-hd topics-avatar" src=""></image>
      <view class="topics-bd">
        <view class="topics-nickName">昵称</view>
        <view class="topics-content"></view>
        <view class="topics-resource" >
          <video class="resource-item" src="{{false}}" objectFit="cover">
            <cover-view class="resource-video-controls"></cover-view>
          </video>
        </view>
        <view class="topics-info">
          <text class="topics-reply-date">2018-11-11</text>
          <text class="topics-reply-number">200 回复</text>
        </view>
      </view>
    </navigator>
  </view>
</view>
```

demo样式
1. 话题项目为两列布局，我们可以使用 flex 然后添加适当的 padding 和底部边框
2. 设置话题的两侧，左侧为固定，右侧自适应
3. 设置左侧的头像图片大小宽高和背景色，再加一点右边的外边距和右侧栏目有点距离。
4. 设置昵称的字体大小和颜色及下外边距
5. 设置文字的字体大小和颜色及下外边距
6. 设置资源的大小为正方形，设置视频控制容器为100%撑满
7. 设置信息的布局为 flex 两边对其，及其字体大小和颜色


```wxss
/*1. 话题项目为两列布局，我们可以使用 flex 
  然后添加适当的 padding 和底部边框*/
.topics-item{
  display: flex;
  padding: 30rpx 24rpx;
  border-bottom: 1px solid #e4e4e4;
  background-color: #fff;
  line-height: 40rpx;
}

/*2. 设置话题的两侧，左侧为固定，右侧自适应*/
.topics-item .topics-hd{
  flex: none;
}

.topics-item .topics-bd{
  flex: 1;
}

/*3. 设置左侧的头像图片大小宽高和背景色，
  再加一点右边的外边距和右侧栏目有点距离。*/
.topics-item .topics-avatar{
  width: 80rpx;
  height: 80rpx;
  margin-right: 20rpx;
  background-color: #f5f5f5;
}

/*4. 设置昵称的字体大小和颜色及下外边距*/
.topics-item .topics-nickName{
  font-size: 14px;
  color: #606686;
}

/*5. 设置文字的字体大小和颜色及下外边距*/
.topics-item .topics-content{
  font-size: 14px;
  color: #333;
  margin-bottom: 10px;
}

/*6. 设置资源的大小为正方形, 设置视频控制容器为100%撑满*/
.topics-item .topics-resource .resource-item{
  width: 300rpx;
  height: 300rpx;
  background-color: #f5f5f5;
}
.resource-video-controls{
  width: 100%;
  height: 100%;
}

/*7. 设置信息的布局为 flex 两边对其，上外边距，及其字体大小和颜色*/
.topics-item .topics-info{
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
  font-size: 10px;
  color: #999;
}
```





