---
title: NodeList和HTMLCollection的区别
layout: post
date: 2015/06/13
---
NodeList和HTMLCollection是Dom中两个非常相似的概念，今天研究了一小会会，总结一下下。

都是array-like object
<!--more-->
getElementsByName(),Node.childNodes,document.querySelectorAll返回NodeList对象，而类似getElementsByTagName(),document.images() 和 document.forms() 的属性为HTMLCollection对象。

都是只读的类数组对象。 有length属性。 

不能直接在NodeList和HTML集合上调用Array方法，但可以简介的使用。

	var content = Array.prototype.map.call(document.getElementsByTagName("p"),function(e){ return e.innerHTML; });
	
NodeList和HTMLCollection对象不是历史文档状态的一个静态快照，而通常是实时的，并且当文档变化时他们所包含的元素列表能随之改变。 

They differ in the methods they provide and in the type of nodes they can contain. While a NodeList can contain any noe type, an HTMLCollection is supposed to only contain Element nodes.

An HTMLCollection provides the same methods as a NodeList and additionally amethod called namedItem.

> [Difference between HTMLCollection, NodeLists, and arrays of objects](http://stackoverflow.com/questions/15763358/difference-between-htmlcollection-nodelists-and-arrays-of-objects)




