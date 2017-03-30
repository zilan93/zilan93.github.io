---
title: css动画里的steps()用法详解【译】
date: 2017-03-30 15:53:40
tags: css
---
原文地址：http://designmodo.com/steps-css-animations/
原文作者：<a href="http://designmodo.com/author/joni/" title="Posts by Joni Trythall" rel="author">Joni Trythall</a>

我想你在css 动画里使用`steps()`会和我一样有很多困惑。一开始我不清楚怎样使用它，于是搜索出了两个案例：<a href="http://lea.verou.me/2011/09/pure-css3-typing-animation-with-steps/" target="_blank">typing demo by Lea Verou</a> 和 <a href="http://jsfiddle.net/simurai/CGmCe/" target="_blank">animated sprite sheet by Simurai</a>.
这些例子很棒，帮助我开始理解这个特别的timming function，但是它们是如此优秀的例子，以至于在demo之外还是难以理解怎样使用`steps()`。
所以，我仔细研究了`steps()`并且做了一些动画demos用来帮助理解一些难点。
<!--more-->
## steps介绍 ##
`steps()`是一个timing function，允许我们将动画或者过渡分割成段，而不是从一种状态持续到另一种状态的过渡。这个函数有两个参数——第一个参数是一个正值，指定我们希望动画分割的段数。

> Steps(<number_of_steps>，<direction>)

第二个参数定义了这个要点 在我们的`@keyframes`中申明的动作将会发生的关键。这个值是可选的，在没有传递参数时，默认为”end”。方向为”start”表示一个左--持续函数，在动画开始时，动画的第一段将会马上完成。以左侧端点为起点，立即跳到第一个step的结尾处。它会立即跳到第一段的结束并且保持这样的状态直到第一步的持续时间结束。后面的每一帧都将按照此模式来完成动画。

方向为”end”表示一个右--持续函数。动画执行时，在每一帧里，动画保持当前状态直到这一段的持续时间完成，才会跳到下一步的起点，后面的每一帧都按照这个模式来进行，在最后一帧的起点，等到这一帧的持续时间结束，整个动画的执行也已经结束，执行动画的元素来不及跳到这一帧的终点，直接回到了整个动画起点，开始了第二次动画。每个选择本质上从一个不同的面移动这个元素并且将产生一个不同的位置在这个相同的动画里。
这面是示例图：
 
{% qnimg 170330_1.png title:"" alt:"" %}

动态解析图如下：
{% qnimg 170330_2.gif title:"" alt:"" %}

## 填充模式和迭代次数的影响#  ##
在我们开始前，明白一个不同的填充模式或者迭代次数将会怎样影响`steps()`是非常重要的，例如”forwards”或者”infinite”的使用。如果我们有两辆车，使用相同的动画持续时间、相同的`steps()`值，但是其中一个设置（infinite），另一个填充（forward）,终点的这两辆汽车看起来非常不同，即使他们从相同的轴点出发。

“forwards”的命令使这个动画元素保持着`@keyframes`里最后一个动画样式。在动画，里将它与`steps()`联系使这个动作出现，好像初始的静止状态没有计算到总的步数里。当它是”end”，好像这个车行驶了额外的步数超出了你的`steps()`的声明，取决于你怎样看待它。

这些讲解听起来还是很散乱，但是我们将在demos里慢慢讲解。（[demo地址：](http://designmodo.com/demo/stepscss/)）现在最重要的事情是留心这些变化将会怎样影响你的意图和steps数。下面是infinite VS forwards车：

{% qnimg 170330_3.png title:"" alt:"" %}


{% qnimg 170330_4.png title:"" alt:"" %}


  [1]: /img/bVDHrH

## steps demos ##
你可以点击<a href="http://designmodo.com/demo/stepscss/" data-wpel-link="internal">这里</a>查看demos,它由以下几个部分组成：

 - 纯css实现的闹钟
 - 一些节能的css车
 - 前进的熊爪印
 - 纯css实现的进度圆

### css闹钟 ###

{% qnimg 170330_5.png title:"" alt:"" %}

一个应用了`steps()`的闹钟演示。我们需要闹钟的指针旋转起来，但是不是圆滑连续的运动。使用`steps()`将允许我们模仿真实的闹钟的运动。

这里涉及到了一些数学知识，但是还不是很痛苦。我们需要秒针通过60步完成360度的旋转在60s的时间里。

{% qnimg 170330_6.png title:"" alt:"" %}

分针我们可以应用相同的`@keyframes`,只有改变动画的执行时间即可。分针转一圈，为3600s,即分针在3600s里，完成60步。

{% qnimg 170330_7.png title:"" alt:"" %}

申明：这只是一个css闹钟，并不能依赖这个闹钟来执行你的日常活动。

### css cars ###

{% qnimg 170330_8.png title:"" alt:"" %}

css车演示了在`steps()`里使用"end"和"start"的不同。"start"使小车立即移动到一步的结束处并保持当前状态直到这一步的持续时间结束。看起来就好像使用了"start"的车的位置比使用"end"的车的位置更远，但是如果你给这两辆小车添加一个动画的延迟，你可以看到它们是从相同的起点出发的。

"end"会等到每一步的执行时间结束才会开始动画。第一辆车移动的时候，这是它的第二步，所以这两辆车没有机会同步移动。动画里白色的边框是动画当前的起始位置。

{% qnimg 170330_9.png title:"" alt:"" %}

### 熊脚印 ###
{% qnimg 170330_10.png title:"" alt:"" %}

另一种更好的理解`stpes()`的方法是创建真实的步数。这个案例我们将会使用熊脚印。这个演示使用了由六个脚印组成的图片。这个图片被一个`<div>`覆盖，我们要用`steps()`移动这个`<div>`来显示出脚印，用来模仿实际的脚印。

没有使用steps()时，`<div>`将平滑的向右移动，这不是我们想要的效果。我们希望每个脚印可以立即完整的出现。

有六个脚印，我们需要向右移动`<div>`的长度为图片的整个宽度。

{% qnimg 170330_11.png title:"" alt:"" %}

我们的`<div>`将向右移动675px在7秒7步的时间里。每一步是96px宽。"end"表示我们的动画将保持它的初始状态，覆盖扬剧有的脚印直到第一步完成。

### css进度圆 ###

{% qnimg 170330_12.png title:"" alt:"" %}

在这个演示里我们使用"start"来动态改变不透明度。使用"start"制作一个百分比的可见度的变化。

{% qnimg 170330_13.png title:"" alt:"" %}


{% qnimg 170330_14.png title:"" alt:"" %}

所有的百分数在同一个`<div>`里，我们将这个`<div>`通过动画向上移动380px；初始值是"20%",我们需要通过四步移动`<div>`使数值为40%,60%,80%到最后的100%。

再次强调，使用"forwards"和"infinite"对步数的的作用是不同的。如果我们改变成"infinite"，将不会出现"100%",因为"forwards"命令使动画在我们设置的步数外添加了额外的一步。"forwards"使动画保持结束时的状态，所以在步数执行完毕后，动画会跳到最后一帧的状态并保持不变。

## 结束语 ##
steps() timing function确实难以理解，但是一旦你掌握它了，就会有很多便利。css function 允许我们将动画切割成清楚的步数，或者创建平滑的动画效果。
希望这些demos可以帮助你在动画里更好的理解使用`steps()`。
