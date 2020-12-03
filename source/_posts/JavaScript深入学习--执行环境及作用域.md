---
title: JavaScript深入学习 -- 执行环境及作用域
date: 2020-07-27 19:50:14
tags: [执行环境, 作用域]
categories: JavaScript
---
JavaScript的执行环境和作用域是学习闭包的必要知识，所以特地用本篇文章来详细讲解相关知识点，如有错误，欢迎指正。
<!-- more -->

## 执行环境
执行环境(execution context)在JavaScript中主要分为两种：全局执行环境和函数执行环境。每个执行环境都有一个与之关联的变量对象(variable object),环境中定义的所有变量和函数都保存在这个对象中。
#### 全局执行环境
全局执行环境是最外围的一个执行环境，在web浏览器中被看成是window对象。
####  函数执行环境
每个函数都有属于自己的执行环境，当执行流进入一个函数时，函数的环境就会被推入环境栈，函数执行结束后弹出。值得一提的是，函数执行环境的变量对象是其活动对象(activation object)。这个活动对象最开始只有一个变量即arguments对象（简单理解就是函数的参数组成的对象）。
## 作用域链
当代码在一个执行环境中运行的时候，会创建变量对象的一个作用域链(scope chain)。作用域链的作用是保证对执行环境有权访问的所有变量和函数的有序访问。

下面来看一个简单的例子来加深理解
```javascript
var bigChair = "big Chair";
function getFurniture(table){
  var littleChair = "little chair";
  
  function getAnotherFurniture(){
    var miniChair = "mini chair";
  }
}
getFurniture("big table");
```
在上面的代码中，当执行
```javascript
getFurniture("big table");
```
这段代码时，此时的作用域链如下图所示，其中的arguments为函数的参数构成的类数组。
<img src="http://chunjiez.com:3000/post-1-diagram.png">
简单总结一下：当代码执行到一个需要用到变量的地方时，会从当前的作用域链的最前端的变量对象开始搜索变量，最前端找不到，就会找上一层继续搜索变量，如果找到了需要用到的变量，则搜索停止，如果找不到，则继续往上一层找，直到找到最外层的变量对象即全局执行环境(window)的变量对象。

下面是一道小例题，可以加深对上面知识的理解，答案解析可以在文章末尾找到。
```javascript
//练习题
var color = "blue";
function changeColor(color){
  if(color === "blue"){
    color = "blue++";
  }else if(color === "yellow"){
    color = "yellow++";
  }else{
    color = "red";
  }
}
changeColor("yellow");
console.log(color);//试推测此处打印的值是多少
```
## 延长作用域链
所谓延长作用域链，就是在作用域链的前端增加一个临时的变量对象，给变量对象会在代码执行后被移出。在下面两种情况在会发生这种现象：
- try-catch语句的catch块
- with语句

这两个语句都会在作用域链的前端添加一一个变量对象。对with语句来说，会将指定的对象添加到作用域链中。对catch语句来说，会创建一个新的变量对象，其中包含的是被抛出的错误对象的声明。
下面看一个例子。
```javascript
function buildUrl(){
  var qs = "?debug=true";

  with(location){
    var url = href + qs;
  }

  return url;
}
```
对于上面的代码来说，函数中包含了with语句，因此在执行这个函数的时候，当前的作用域链前端会增加一个新的变量对象，在这里的话就是with语句的中的location中的属性和方法组成的变量对象。因此不难理解with语句中的href是怎么得到的了，就是从在新增的变量对象中搜索到的。

## 块级作用域
由花括号括起来的代码拥有自己的作用域(用JavaScript的说法来说就是拥有自己的执行环境)，这样的作用域称之为块级作用域。

ES5 只有全局作用域和函数作用域，没有块级作用域。最经典的例子就是for循环了
```javascript
for(var i=0;i<10;i++){
  console.log("啥也没干");
}
console.log(i);// 10
```
此时的i存在于全局变量中。

ES6新增了let和const命令，也增加了块级作用域这一功能。同样用for循环来举例
```javascript
for(let i=0;i<10;i++){
  console.log("啥也没干");
}
console.log(i);// Uncaught ReferenceError: i is not defined"
```
此时的i就不存在全局作用域中了，所以会报错。

对于有块级作用域的语言来说，for 语句初始化变量的表达式所定义的变量，只会存在于循环的环境之中。

## 练习解析
正确答案是 "blue"。虽然在函数里进行了一波操作，但其实都并没有对全局作用域的color有任何的影响，因为函数里面的color是函数作用域里的color，因为对变量进行操作，总是优先从当前的作用域链的最前端进行查找。而
```javascript
console.log(color);
```
是在全局作用域环境下执行的，故而打印的自然是全局作用域的color，也就是"blue"了:)。

## Reference:

- JavaScript高级程序设计（第3版）
- [ECMAScript 6 入门教程](https://es6.ruanyifeng.com/)

