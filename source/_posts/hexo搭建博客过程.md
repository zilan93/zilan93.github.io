---
title: hexo搭建博客过程
date: 2017-03-17 15:26:58
tags: hexo
---
其实hexo搭建博客超级简单。不过因为开始对这个不熟悉，跟着网上找到的教程操作反而入了很多坑。这篇文章算是做个总结吧。下面分几个阶段逐渐由简入深来讲解。
## 快速熟悉hexo ##
### hexo博客成型 ###
1. 打开hexo官方文档，根据"安装前提"检查是否已经安装需要的应用程序。都安装好了后，按照文档上的步骤建站。
![hexo1](http://omsqnp96s.bkt.clouddn.com/static/images/hexo1.png)
2. 执行完上面命令后，接着执行以下命令，这行命令的作用是生成我们在浏览器里看到的html静态文件。
`$ hexo generate`
该命令可以简写为
`$ hexo g`
3. 怎么在本地查看这些页面呢？那么需要搭建一个服务器。hexo提供了"hexo-server"模块，通过以下命令来安装。
`$ npm install hexo-server --save`
安装完成后，在命令行里输入以下命令可启动服务器
`$ hexo server`在浏览器里输入 http://localhost:4000 即可查看网站。在服务器启动期间，hexo会监视文件变动并自动更新，您无须重启服务器。
注意：有时在端口4000下很长时间无法加载出页面，此时可以试着更换端口。如下：
`$ npm server -p 5000`
完成上述步骤后，在浏览器里可以看到如下界面：
![hexo2](http://omsqnp96s.bkt.clouddn.com/static/images/hexo2.png)
一个hexo默认的博客已经成型了。
### 配置博客 ###
