---
title: hexo搭建博客过程
date: 2017-03-18 15:26:58
tags: js
---
## 问题：实现一个函数clone，可以对js里五种主要的数据类型进行值复制？    ##

考察点：
1. 对于基本数据类型和引用数据类型在内存中存放的是值还是引用这一区别是否清楚；
2. 怎样判断一个变量的类型；
3. 递归算法的设计；

代码：
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
js数据类型分为基本数据类型和引用数据类型。基本数据类型有string,number,boolean,undefined,null。引用数据类型有object。基本数据类型可以通过"typeof"关键字来检查类型。typeof可返回如下值："string,number,boolean,undefined,object,function"。其中"typeof null"返回的是"object"。

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
### 判断变量的数据类型  ###
