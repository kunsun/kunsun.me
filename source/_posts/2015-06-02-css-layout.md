---
title: CSS布局
layout: post
date: 2015/06/02
---

今天中午睡了一小会儿， 醒来到现在1个小时多点，读了这篇[CSS布局的文章](http://zh.learnlayout.com/)，感觉有所收获，顾文之。
<!--more-->
每个元素都有一个默认的display类型， 不过你可以随时的重写它。

设置块级元素左右边距为auto，可以使得水平居中。

使用max-width  代替width可以适应小窗口的情况。IE7+支持max-width.

当设置一个元素的 box-sizing: border-box; 时，此元素的内边距和边框，不会增加它的宽度了。IE8+支持max-width.

一个元素的默认Position值是static， 如果没有设置为其他的值， 那么它不会被定位的。
relative表现和static本来一样，在元素上设置上下左右可以改变它位置，其他元素不会受它影响调整位置。
fixed在移动浏览器中的支持很差，但是有相应的[解决方案](http://bradfrostweb.com/blog/mobile/fixed-position/)。
absolute是相对于最近的没有“positioned”元素定位的。

### 布局可以用好几种方法实现：

* #### position：
可以使用.container设置relative， 然后nav设置absolute, 然后content设置margin-left.
* #### float：
使用float属性。 使用float很爽，但是要清楚之后的垃圾却有点费劲。请看做好的[clearfix方法](http://stackoverflow.com/questions/211383/which-method-of-clearfix-is-best)。
* #### 百分比：
使用这个方法的好处，就是在做响应式设计的时候会省事。
* #### inline-block
这个属性可以代替掉float。 但是IE8+支持。需要和vertical-align一起使用。
* #### column
这是非常新的属性。 IE10+。
* #### flex
介绍最后一个牛逼的方法，就是flex。 IE10+。







