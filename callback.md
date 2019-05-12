# 回调函数

## 回调函数(callback)是什么？

百度：回调函数就是一个通过函数指针调用的函数。如果你把函数的指针（地址）作为参数传递给另一个函数，当这个指针被用为调用它所指向的函数时，我们就说这是回调函数。回调函数不是由该函数的实现方直接调用，而是在特定的事件或条件发生时由另外的一方调用的，用于对该事件或条件进行响应。

因此，回调本质上是一种设计模式，并且jQuery(包括其他框架)的设计原则遵循了这个模式。
在JavaScript中，回调函数具体的定义为：函数A作为参数(函数引用)传递到另一个函数B中，并且这个函数B执行函数A。我们就说函数A叫做回调函数。如果没有名称(函数表达式)，就叫做匿名回调函数。
因此callback 不一定用于异步，一般同步(阻塞)的场景下也经常用到回调，比如要求执行某些操作后执行回调函数。

例子：一个同步(阻塞)中使用回调的例子，目的是在func1代码执行完成后执行func2。

```js
var func1 = function(callback) {
  //do somethind
  (callback && typeof(callbackr) === "function") && callback();
}
func1(func2);
  var func2 = function() {

  }
```


### 异步回调的例子

```js
$(document).ready(callback);
$.ajax({
  url: "test.html",
  context: document.body
}).done(function() {
  $(this).addClass("done");
}).fail(function() { alert("error");
}).always(function() { alert("complete");
});
/**
注意的是，ajax请求确实是异步的,不过这请求是由浏览器新开一个线程请求,当请求的状态变更时,如果先前已设置回调,这异步线程就产生状态变更事件放到 JavaScript引擎的处理队列中等待处理。见：http://www.phpv.net/html/1700.html
*/
```

### 回调什么时候执行
回调函数，一般在同步情境下是最后执行的，而在异步情境下有可能不执行，因为事件没有被触发或者条件不满足。
 
### 回调函数的使用场合
* 
资源加载：动态加载js文件后执行回调，加载iframe后执行回调，ajax操作回调，图片加载完成执行回调，AJAX等等。

* DOM事件及Node.js事件基于回调机制(Node.js回调可能会出现多层回调嵌套的问题)。
* 链式调用：链式调用的时候，在赋值器(setter)方法中(或者本身没有返回值的方法中)很容易实现链式调用，而取值器(getter)相对来说不好实现链式调用，因为你需要取值器返回你需要的数据而不是this指针，如果要实现链式方法，可以用回调函数来实现
* setTimeout、setInterval的函数调用得到其返回值。由于两个函数都是异步的，即：他们的调用时序和程序的主流程是相对独立的，所以没有办法在主体里面等待它们的返回值，它们被打开的时候程序也不会停下来等待，否则也就失去了setTimeout及setInterval的意义了，所以用return已经没有意义，只能使用callback。callback的意义在于将timer执行的结果通知给代理函数进行及时处理。

### 回调函数的传递
上面说了，要将函数引用或者函数表达式作为参数传递。

```js
$.get('myhtmlpage.html', myCallBack);//这是对的
$.get('myhtmlpage.html', myCallBack('foo', 'bar'));//这是错的，那么要带参数呢？
$.get('myhtmlpage.html', function(){//带参数的使用函数表达式
myCallBack('foo', 'bar');
});
```
另外，最好保证回调存在且必须是函数引用或者函数表达式：
(callback && typeof(callback) === “function”) && callback();
同步
“同步模式”就是上一段的模式，后一个任务等待前一个任务结束，然后再执行，程序的执行顺序与任务的排列顺序是一致的、同步的；”异步模式”则完全不同，每一个任务有一个或多个回调函数（callback），前一个任务结束后，不是执行后一个任务，而是执行回调函数，后一个任务则是不等前一个任务结束就执行，所以程序的执行顺序与任务的排列顺序是不一致的、异步的。


