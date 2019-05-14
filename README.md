# JaveScript中两个非常重要的数据类型是对象和数组🤗

## JavaScript中的最重要的类型就是对象
对象是名／值的集合，或字符串到值映射到集合
```JavaScript
var book = { //对象是用花括号括起来
  topic:"javaScript",//属性“topic”的值是“javaScript”
  fat:true,          //属性"fat"的值为true
};
```
通过"."或"[]"来访问对象属性
```JavaScript
book.topic                  //"javaScript"
book["fat"]                 //true:另外一种获取属性的方式
book.author = "Flangagan"   //通过赋值创建一个新属性
book.contents = {}          //{}是一个空对象，它没有属性
```
JavaScript同样支持数组(以数组为索引的列表)
```JavaScript
var primes = [2,3,5,7];     //拥有4个值的数组
primes[0]                   //2:数组中的第一个元素(索引为0)
primes.length               //4:数组中的元素个数
primes[primes.length-1]     //7:数组的最后一个元素
primes[4] = 9;              //通过赋值来添加新元素
primer[4] =11;              //或通过赋值来改变已有的元素
var empty = [];             //[]是空数组，它具有0个元素
empty.length                //0
```
数组和对象中都可以包含另一个数组或对象:
```JavaScript
var points = [
  {x:0,y:0},   //具有两个元素的数组，每个元素都是一个对象
  {x:1,y:1}
];
var data = {
  trial1:[[1,2],[3.4]],   //一个包含两个属性的对象
  trial2:[[2,3],[4,5]]    //每个属性都是数组，数组的元素也是数组
```