---
title: 图片上传本地预览实现（兼容IE8）
date: 2017-05-10 10:09:59
tags: js
---
最近项目里需要用到上传图片并预览的功能，于是写了个jQuery预览图片插件，兼容IE8,下载[地址](https://github.com/zilan93/uploadImg.git)。有需要的，可以直接下载。第一次写jQuery插件，如有不对之处，欢迎大家指正。下面是一些相关的知识点笔记。
# HTML5 File API
在HTML5 File API出现前，前端对于文件的操作的非常有局限性的。出于安全角度考虑，从本地上传文件时，代码是不可能获取文件在用户本地的地址。但是File API的出现，实现了这一功能。File API主要有以下几个接口：
1. Blob
2. File
3. FileList 
4. FileReader
<br />
<!-- more -->
## FileList API
当通过file控件获取文件后，可以通过该控件的files属性得到FileList对象。FileList对象里保存着选择的文件，即File对象。在MDN里有如下提示：

>在Gecko 1.9.2之前,通过input元素,每次只能选择一个文件,这意味着该input元素的file
>s属性上的FileList对象只能包含一个文件.从Gecko 
>1.9.2开始,如果一个input元素拥有multiple属性,则可以用它来选择多个文件.

因此需要注意，在默认状态下选择文件，每次FileList对象里只有一个File文件。
以上传图片为例。File对象保存了“name”,"size","type"等图片的信息。
```
<input id="fileItem" type="file">
var file = document.getElementById('fileItem').files[0];
```

## FileReader API实现本地图片预览

**FileReader用来异步读取本地文件**
FileReader对象允许web应用程序异步读取存储在用户计算机上的文件（或原始数据缓冲区）的内容。我们可以通过FileList获取上传的图片相关信息，但是想要实现本地预览还需要借助FileReader来实现,FileReader可以读取本地图片，并将图片数据转换成base64编码的字符串形式嵌入到页面中。
```
//创建一个FileReader对象
var reader = new FileReader();
//读取file文件；
reader.readAsDataURL(file);
```

FileReader提供了几个方法，如`readAsText()`,`readAsDataURL()`,`readAsArrayBuffer()`，分别表示用不同的数据格式来读取上传的文件，并将结果保存在result属性里。
在读取本地文件的过程中，FileReader提供了一些事件可供监听。如`onprogress`,`onload`,`onerror`,`onabort`等。在上传图片的过程中，常用到的有`onprogress`事件在读取数据过程中周期性调用，可以用来实现上传进度条效果，`onload`事件，当读取操作成功完成时调用。在我们实现上传图片的效果里，就有用到。
```
//当文件读取成功后，将结果保存到url变量里；
reader.onload = function(evt) {
    var url = evt.target.result;
}
```
最后，将该url赋值给img元素的src属性，便可以实现本地图片预览了。
关于*兼容性*，不兼容IE9及以下浏览器，其它主流浏览器一般都没有问题。

# HTML5 URL API
URL对象用于生成指向File对象或者Blob对象的URL。使用URL的好处是可以不必把文件内容读取到JavaScript中而可以直接使用文件内容。如果通过URL对象来实现本地预览，那么只需将生成的File对象的URL传递给img元素的src属性即可。

>当使用一个没有实现该构造器的用户代理时，可以通过 Window.URL 
>属性来访问该对象（基于 Webkit 和 Blink 内核的浏览器均可用 Window.webkitURL 
>代替）。

```
var url = window.URL || window.webkitURL;
```

## createObjectURL()实现本地图片预览
URL对象有两个方法，分别是`createObjectURL()`和`revokeObjectURL()`。
1. createObjectURL()的作用
生成文件File对象或者Blob对象的URL对象，通过这个URL，可以访问到URL所指向文件的整个内容。
```
var src = url.createObjectURL(file);
```

在每次调用createObjectURL()方法的时候,都会创建一个新的对象URL,即使你已经用相同的对象作为参数创建过。在你不需要这些对象URL的时候,你应该通过调用 window.URL.revokeObjectURL()方法来释放它们所占用的内容。

2. revokeOjectURL()的用法
```
url.revokeObjectURL(src); 
```
参数src是上述我们通过createObjectURL创建的URL对象。
关于*兼容性*，不兼容IE9及以下浏览器，其它主流浏览器一般都没有问题。在MDN里提到，这是一个实验中的功能。

# 图片预览兼容IE处理
IE9及以下版本不支持File API和URL API。因此需要做兼容处理。
在这里，我们需要用到document.selection。document.selection只有IE支持。代表了当前激活选中区，即高亮文本块，和/或文档中用户可执行某些操作的其它元素。selection 对象的典型用途是作为用户的输入，以便识别正在对文档的哪一部分正在处理，或者作为某一操作的结果输出给用户。
在用document.selection前，我们需要先创建选中区。如鼠标选中文本框，即是一个选中区。也可以通过js提供的select()方法创建一个选中区。创建了选中区后，我们就可以通过document.selection获取该选中区。如果要对选中区执行操作，则需要先调用createRange()方法。
```
//获取上传文件控件的值；
file.select();
var url = document.selection.createRange().text;
```
现有的获取IE低版本上传文件的value值一般都是这种方式，在IE中原本可以直接通过input的value值来获取上传图片的路径，但是在实际中很少看到使用。具体的大家可以去查查资料。
非IE6版本的IE由于安全问题直接设置img的src无法显示本地图片，但是可以通过滤镜来实现。
``` 
pic.style.filter = "progid:DXImageTransform.Microsoft.AlphaImageLoader(sizingMethod='scale',src=\"" + reallocalpath + "\")";
```
到这里，图片本地预览基本就完成了。
