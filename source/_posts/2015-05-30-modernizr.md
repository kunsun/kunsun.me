---
title: Modernizr分析
layout: post
date: 2015/05/30
---

一直认为学习一个工具或者一门语言做好的方法就是看原生文档， 这次[Modernizr](http://modernizr.com/)的学习再次确认了这个想法。 因为之前几天偷懒，三三两两的看关于Modernizr的中文文档，远没有今天专注花两个小时阅读[官方文档](http://modernizr.com/docs/)管用。
<!--more-->
Modernizr会告诉你当前使用的浏览器是否原生支持了这些HTML5和CSS3特性。

### 实现原理

Javascript客户端检测有三种方法：

* 能力检测，也称为特征检测feature detection
* 怪癖检测quirks detection
* 还有用户代理检测,使用navigator.userAgent

Modernizr使用的就是最为人们广泛接受的能力检测，代替用户代理检测，这种方式不必顾及特定的浏览器如何如何，只要确定浏览器支持特定的能力，就可以给出解决方案。

Modernizr并不是使用JavScript动态地加载不同的样式表，而是使用非常简单的技术，将类添加到页面的<html>标签。 如果页面支持box-shadow属性， 那么Modernizr会添加boxshadow类。 如果不支持，那么它会用no-boxshadow类作为替代进行添加。

### Modernizr与html5shiv的区别

Stackexchange上的一条[回答](http://webmasters.stackexchange.com/questions/11729/whats-the-difference-between-modernizr-and-html5shiv)说明了这个问题。
> 顺便提一个非常有意思的话题，前端经常提到的[shim与polyfill](http://www.cnblogs.com/ziyunfei/archive/2012/09/17/2688829.html)到底是什么？stackoverflow也有类似的[讨论](http://stackoverflow.com/questions/6599815/what-is-the-difference-between-a-shim-and-a-polyfill)。

### Modernizr与respondjs的关系

respondjs也是一个polyshiv。 如果生成Modernizr时包含了Media Query。那么两者都可以在旧版浏览器中实现媒体查询。下面这种写法是我认为的很好的实现方式。
	
	<script>Modernizr.mq('(min-width:0)') || document.write('<script src="js/libs/respond.min.js"><\/script>')</script>

也可以使用[Yepnope的方式](http://yepnopejs.com/)。因为Modernizr的load方法自带有Yepnode。

* 这篇文章讲了如何使用respondjs实现responsive。[How to Build a Responsive Website That Supports Older Browsers](http://www.zell-weekeat.com/support-for-older-browsers/)
* 这篇[文章](http://stackoverflow.com/questions/8273845/modernizr-with-respond-js)也讲了modernizr和respondjs的取舍问题。
* [官方推介](https://www.w3.org/community/webed/wiki/Optimizing_content_for_different_browsers:_the_RIGHT_way#Polyfills.2Fshims)的方法。

### Modernizr的方法
modernizr提供了一些方法，在做CSS3操作和响应式设计的时候会很有用。

* modernizr.prefixed():  会返回带有浏览器厂商前缀(browser vendor tag)的CSS属性。可以配合正则表达式处理字符串以及DOM Events事件判断。
* modernizr.addTest(): 用来测试目前modernizr不支持的特征，返回一个布尔值。如果true，则html标签中出现测试的属性，反之，出现no-属性。

这是Modernizr最重要的两个方法。

Github上的[这段代码]()是学习prefixed()与addTest()很好的示例。

	 Modernizr.addTest('csstransformspreserve3d', function () {
 
        var prop = Modernizr.prefixed('transformStyle');
        var val = 'preserve-3d';
        var computedStyle;
        if(!prop) return false;
 
        prop = prop.replace(/([A-Z])/g, function(str,m1){ return '-' + m1.toLowerCase(); }).replace(/^ms-/,'-ms-');
 
        Modernizr.testStyles('#modernizr{' + prop + ':' + val + ';}', function (el, rule) {
            computedStyle = win.getComputedStyle ? getComputedStyle(el, null).getPropertyValue(prop) : '';
        });
 
        return (computedStyle === val);


### 总结
Modernizr功能很多，所以在使用的时候也可以自己取舍使用polyfill还是Modernizr。

---
之后的博文，尽量以一周三篇的频率更新。 主要还是前端的内容。 

