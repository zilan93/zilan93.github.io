---
title: 实现一个函数对js里主要的数据类型进行值复制
tags: js
date: 2017-03-29 09:27:58
---

问题：实现一个函数clone，可以对js里五种主要的数据类型进行值复制？
考察点：
1. 对于基本数据类型和引用数据类型在内存中存放的是值还是引用这一区别是否清楚；
2. 怎样判断一个变量的类型；
3. 递归算法的设计；

代码：
<!-- more -->
```
function clone(obj){
                var obj2={};
                if (typeof obj !== 'object'||obj=='') {
                    return obj;
                }else if (Object.prototype.toString.call(obj)=='[object Array]') {
                    obj2=[];
                    for(var i in obj){
                        obj2[i]=arguments.callee(obj[i]);
                    }
                }else{
                    for(var i in obj){
                        obj2[i]=arguments.callee(obj[i]);
                    }
                }
                return obj2;
            }
```
下面逐一来讲解考察知识点。
## 不同数据类型值的保存方式   ##
js数据类型分为基本数据类型和引用数据类型。基本数据类型有string,number,boolean,undefined,null。引用数据类型有object。

基本数据类型是以值的方式保存在内存中的。如
```
var a = 1;
var b = a;
a = 2;
console.log(b);
```
在这里，将a的值赋给b后，在内存中会新增空间存放这个值"1"。将a的值重置为2后，它对b是没有影响的，a和b的值是单独存储的。因此b依然为1。

在引用数据类型里，它传递的是关于对象的引用。
```
var a = {name:'bird',age:12};
var b = a;
a.name = 'cat';
console.log(b.name);
```
在这里，打印的结果是'cat'。变量a里保存的是对象的一个引用，当把a的值赋给b时，只是把这个引用赋给了b，它们都指向同一个内存。因此当重置a.name后，b.name也发生改变了。就像取昵称，你向不同的人展示不同的昵称，可是你这个人是独一无二的。

在我们这个问题里，要对不同数据类型的*值*进行复制，上面我们说了，引用数据类型如果直接赋给一个变量，它实际上复制的是引用。因此，我们需要将基本数据类型与引用数据类型分开复制。那么怎么判断一个变量的数据类型呢？
## 判断变量的数据类型  ##
### typeof ###
基本数据类型可以通过"typeof"关键字来检查类型。typeof可返回如下值："string,number,boolean,undefined,object,function"。其中"typeof null"返回的是"object"。
### instanceof ###
instanceof用来判断一个变量是否是某个对象的实例。如
```
var a = new Object();
a instanceof Object;    //true
```
### 检测数组的方法  ###
通过"typeof"筛选出数据类型不是"object"或为null的数据，这些数据类型的数据在内存中存放的是值，因此可以直接return返回；剩下的有对象和数组，我们需要进一步区分。那么如何检测数组呢？

1. ECMAScript5的isArray函数
    ECMAScript提供的Array.isArray(obj)可以用来检测一个对象是否是数组；
    ```
    var a = [1,2];
    Array.isArray(a);    //true
    ```
    这种方式检测数组的唯一缺陷是它的兼容性。IE9+、Firefox4+、Safari5+、Opera10.5+和Chrome都实现了这个方法，但是在IE8之前的版本是不支持的。

2. 通过constructor属性来检测
    对象都有一个constructor属性，指向它的构造函数。对于数组来说，它的内建对象是Array。通过该属性也可以检测数组类型。
    ```
    var a = [1,2];
    console.log(a.constructor == Array);    //true
    ```
    
3. instanceof操作符
    instanceof操作符用来判断前面的对象是否是后面的类或对象的实例。
    ```
    var a = [1,2];
    console.log(a instanceof Array);
    ```
    不过在有iframe存在时会出现问题。当你在多个frame中来回跳时，constructor和instanceof就不管用了。由于每一个frame都有自己的执行环境，跨frame实例化的对象彼此并不共享原型链，通过instanceof操作符和constructor属性检测的方法自然会失败。如下：
    ```
    var iframe = document.createElement("iframe");    //创建一个"iframe"标签
    document.body.appendChild(iframe);    //将iframe添加到body里
    otherArray = window.frames[window.frames.length - 1].Array;//获取Array构造函数；
    var arr = new otherArray("1","2");    //在iframe里实例化一个arr数组；
    console.log(arr instanceof Array);    //false
    console.log(arr instanceof otherArray);    //true
    ```
因为arr数组是在框架里创建的，它是从iframe里的Array构造函数实例化而来（otherArray）的。

4. 对象原生toString检测
   通过`Object.prototype.toString`来检测，它的工作原理是，先取得对象的一个内部属性[[Class]]，然后依据这个属性，返回一个类似于"[object Array]"的字符串作为结果（[[]]表示语言内部用到的、外部不可直接访问的属性）。利用这个方法，再配合call，我们可以取得任何对象的内部属性[[Class]]。然后把类型检测转化为字符串比较，以达到我们的目的。
   为什么不直接obj.toString()呢？虽然Array继承自Object（所有对象都继承Object()），内部也有`toString`方法，但是这个方法可能被改写达达不到我们的要求。
检测数组方法可以参考(Javascript学习笔记：检测数组方法)[https://www.w3cplus.com/javascript/array-part-2.html] 这篇文章。

## 递归算法  ##
在程序中，递归就是函数直接调用自己或者间接调用自己。
在我们这个题目里，通过`for…in`来遍历数组或对象。通过`arguments.callee()`来接收一个属性值，`arguments.callee()`的作用是调用当前正在执行的函数，也就是`clone(obj)`。这一操作的作用是，属性值可以是任何数据类型，它可能是一个对象，如果直接`obj2[i] = obj[i]`，那么复制的就是对象的引用了。所以我们需要对这个值再进行一次深度复制。即通过`arguments.callee()`来调用当前执行的函数。
```
for(var i in obj) {
                    obj2[i]=arguments.callee(obj[i]);
                }
```
递归算法可以参考[javascript递归算法的初步认识及使用技巧](http://www.imooc.com/article/11823)这篇文章。