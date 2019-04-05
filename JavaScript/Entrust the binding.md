# 事件绑定

要想让JavaScript对用户对操作做出响应，首先要对DOM元素绑定事件处理函数  
所谓事件处理函数，就是要处理用户操作的函数，不同的操作对应不同的名称

在Javascript中，有三种常用的绑定事件的方法:  
* 在DOM元素中直接代码绑定;
* 在Javascript代码中绑定;
* 绑定事件监听函数

### 在DOM中直接绑定事件  

我们可以在DOM元素上绑定onclick、onmouseover、onmouseout、onmousedown、onmouseup、ondblclick、onkeydown、onkeypress、onkeyup等。好多不一一列出了

```html
<button id="selectBtn" onclick="testfunc()">选择</button>

<script>
    function testfunc(){
        console.log('bindDOM done',this) //this对象指向window
    }
</script>
```
### 在JavaScript代码中绑定事件

在JavaScript代码中（即script标签内）绑定事件可以使JavaScript代码与HTML标签分离，文档结构清晰，便于管理和开发。  
```
var selectBtn = document.getElementById('selectBtn');
selectBtn.onclick = function(){
    console.log('bindDOM2 done',this) //this对象指向绑定元素本身
}
```
### 使用事件监听绑定事件
```
var selectBtn = document.getElementById('selectBtn');
selectBtn.addEventListener('click',function(){
    console.log('bindDOM3 done')
})
```
绑定事件的另一种方法是用 addEventListener() 或 attachEvent() 来绑定事件监听函数。下面详细介绍，事件监听  

关于事件监听，W3C规范中定义了3个事件阶段，依次是捕获阶段、目标阶段、冒泡阶段。
起初Netscape制定了JavaScript的一套事件驱动机制（即事件捕获）。随即IE也推出了自己的一套事件驱动机制（即事件冒泡）。最后W3C规范了两种事件机制，分为捕获阶段、目标阶段、冒泡阶段。IE8以前IE一直坚持自己的事件机制（前端人员一直头痛的兼容性问题），IE9以后IE也支持了W3C规范。

    js事件捕获和冒泡

      1.什么是事件冒泡：
    事件按照从最特定的事件目标到最不特定的事件目标(document对象)的顺序触发。（默认的事件处理程序，从子级到父级）

    2.什么是事件捕获：
    事件从最不精确的对象(document 对象)开始触发，然后到最精确(也可以在窗口级别捕获事件，不过必须由开发人员特别指定)。（捕获型事件先发生，由父级到子级）

    3.事件绑定中的false,true
    （1）作用：第3个参数useCapture是一个Boolean值，用来设置事件是在事件捕获时执行，还是事件冒泡时执行
    （2）true：事件捕获，父级元素先触发，子级元素后触发，即div先触发，p后触发
    （3）false:事件冒泡，子级元素先触发，父级元素后触发，即p先触发，div后触发。

    4.阻止冒泡：
        ev.cancelBubble=true;
        ev.stopPropagation();


## 事件委托
事件委托就是利用冒泡的原理，把事件加到父元素或祖先元素上，触发执行效果  
＊ 原声委托绑定事例
```
var selectList = document.getElementsByClassName('select-list')[0];
selectList.addEventListener('click',function(event){
    var that = event.target; // 获取当前点击的元素
    var selectItemsClassName = that.getAttribute('class') ? that.getAttribute('class') : '';
    var hasSelectItemClass = (selectItemsClassName.indexOf('select-item') === -1 );
    // 如果点击来源是 select-item 的子元素才触发事件
    if(!hasSelectItemClass){
        console.log('do Something')
    }
})
```
* jQuer原生绑定事例
```
$('.select-list').on('click','.select-item',function(e){
    console.log('do Something')
})
```






