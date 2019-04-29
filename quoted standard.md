# 用模块封装代码－－－－导入导出的基本语法
## 什么是模块？
模块是自动运行在严格模式下并且没有办法退出运行的javaScript代码，
模块真正的魔力为仅导出和导入你需要的绑定，而不是所有的东西都放在一个文件中。

export,export default || exports,module.exports || import,require 傻傻分不清，头大了!😣

接下来先理理他们的使用范围  

主要分为两大块，涉及到es6的导入导出和nodejs的导入导出

```js
require: node和 es6都支持的引用
export / import : 只有es6支持的导出引用
module.exports / exports:只有node支持的导出
```

## node.js

Node里面的模块系统遵循的是CommonJS规范。

那问题又来了，什么是CommonJS规范呢？

由于js以前比较混乱，各写各的代码，没有一个模块的概念，而这个规范出来其实就是对模块的一个定义。

CommonJS定义的模块分为: 模块标识(module)、模块定义(exports) 、模块引用(require);

先解释 exports 和 module.exports
在一个node执行一个文件时，会给这个文件内生成一个 exports和module对象，
而module又有一个exports属性。他们之间的关系如下图，都指向一块{}内存区域。

```js
exports = module.exports = {};
```
![images](https://raw.githubusercontent.com/rainyGLC/gitPress/master/images/19.png)

那下面我们来看看代码的吧。

```js
//utils.js
let a = 100;

console.log(module.exports); //能打印出结果为：{}
console.log(exports); //能打印出结果为：{}

exports.a = 200; //这里exports帮 module.exports 的内容给改成 {a : 200}

exports = '指向其他内存区'; //这里把exports的指向指走

//test.js

var a = require('/utils');
console.log(a) // 打印为 {a : 200}
```

从上面可以看出，其实require导出的内容是module.exports的指向的内存块内容，并不是exports的。
简而言之，区分他们之间的区别就是 exports 只是 module.exports的引用，辅助后者添加内容用的。

尽量都用 module.exports 导出，然后用require导入。

## es6
1. export导出的基本语法
export关键字将一部分已发布的代码暴露给其他模块，在最简单的用例中，可以将export放在任何变量
函数、或类声明的前面，已将他们从模块中导出，
即导出常量，函数，文件，模块，一个文件可以export多个，导入export导出的文件时，语法如下：

```js

//导出数据

  export var color = 'red';
  export let name = 'rainy';
  export const number = 7;

//导出函数
  export function sum(num1,num2){
    return num1+num2
  }

//导出类

  export class Rectangle {
    constructor(length,width) {
      this.length = length;
      this.width = width;
    }
  }

// 这个函数是模块私有的

  function sub(num1,num2){
    return num1 - num2;
  }

//定义一个函数

  function multiply(num1.num2){
    return num1*num2
  }

将它导出为export multiply

```

即

```js
//导出,a.js

    export a = 5;
    export str = '';
    export function c () {
      return str;
    }

// 导入，需要带花括号的

import {a,str,c} from 'a.js';
```

需要注意的细节，除了export关键字外，每一个声明与脚本中的一摸一样。因为导出的函数和类声明需要一个名称，所以代码中的每一个函数或类也确实有这个名称。除非用default关键字，即模块的默认值指的是通过default关键字指定的单个变量、函数或类，只能为每个模块设置一个默认的导出值。导出时多次使用default关键字是一个语法错误


* 导出默认值


```js
export default function(num1,num2) {
  return num1  + num2
}
```

这个模块导出了一个函数作为它的默认值，default关键字表示一个默认的导出，由于函数被模块所代表，因而它不需要一个名称。
也可以在export default之后添加默认导出值的标识符，就像这样：

```js
function sum(num1,num2) {
  return num1 + num2;
}
export default sum;
```

先定义sum()函数，然后再将其导出为默认值，如果需要计算默认值，则先定义这个方法。

即

```js
导出a.js
var sex ="boy";
export default sex

//引用
import sex from './a.js'
```
其实此处相当于为sex变量值"boy"起了一个系统默认的变量名default，自然default只能有一个值，所以一个文件内不能有多个export default。

2. 导入的基本语法
从模块中导出的功能可以通过import关键字在另一个模块中访问，import语句的两个部分分别为：要导入的标识符和标识符应当从哪个模块中导入;
* 导入单个模块

```js
import {multiply} from "./example.js"
```
import后面的大括号表示从给定模块导入的绑定，关键字from表示从哪个模块导入的给定的绑定。

* 导入整个模块

```js
import * as example from './example.js'
console.log(example.Number); //7
console.log(example.multiply(1.2)); //2
```
在这段代码中，从example.js中导出的所有绑定被加载到一个被称作example的对象中。指定的导出(number和函数multiply)之后会作为example的属性被访问。这中淡如搁置被称作命名空间导入 (namespace impport)。因为example.js文件中不存在example对象，故而它作为example.js中所有导出成员的命名空间对象而被创建。


* 导入默认值

```js
import sum from './example.js';
console.log(sum(1,2));//2
```
这条import语句从模块example.js中导入了默认值，请注意，这里没有使用大括号，与上面的非默认导入的情况不一样，本地名称sum用于表示模块导出的任何默认函数，这种语法是最纯净的.

总结：
在node模块中，用 module.exports 导出，然后用require导入。
在es6模块中，用export default 导出，然后用import导入。







