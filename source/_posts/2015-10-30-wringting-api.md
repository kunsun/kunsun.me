---
Title: 连笔字书写 API文档
date: 2015-11-04 18:27
status: public
title: '连笔字书写 API文档'
---

连笔字书写目前做的是汉字的展示和练习。 展示主要指标准字。练习指自己也可以在画布上练习写字。写好一个字之后，需要把多个字放入一个页面中。页面是多个字的组合，可以是对联，也可以是成语。 
<!--more-->
### 面向对象思路
将字看做一个对象，命名为Character,单个字的操作都交给它处理。 例如，字的书写、字大小变化、字位置平移。 将页面看做一个对象，这里以成语为例（因为够简单），字与字之间的关系都交给它处理。例如，添加字、删除字、获取当前操作的是哪个字。 
### Character.js 介绍
Character.js中只有Character一个对象， 使用立即执行函数封装所有变量，只通过window['Character'] = Character，使Character可以被访问。因为需要用到Lodash这个库的extend和cloneDeep方法，通过(function(/_){})(/_)引入（和引入JQuery类似）。然后Character对象的创建使用最常用的对象创建方法：构造函数模式和原型模式混成的模式（参见：《Javascript高级程序设计第三版》159页）。
写一个字需要两部分，数据和控制参数。 数据就是strokeArray（笔画数组）,参数就是opts（控制参数）。
* Options：
默认的opts：

        Character.defaultOpts = {
	      gaussian: 1.3,
	      wmax: 13.0,
		  wmin: 2.0,
		  timeScale: 15,
		  radius: 5,
		  lineWidth: 1.2,
		  displayMode: {
			 brush: 1,
			 skeleton: 0,
			strokeColor: 0,
			animation: 0,
			uniformWidth: 0
		  },
		  strokeColors: ['black', 'gray3', 'gray2', 'gray1'],
		  isXML: false,
		  background: "#fff",
          charRatio: 0.85,
	       aspectRatio: 4/3,
	       aspectWidth: 500,
	       charBoxWidth: 500,
	       startPosition: {
  	        x: 0,
  	        y: 0
	       },
	       };
各个参数的含义：
    * gaussian: 高斯系数
    * wmax: 笔画最大宽度
    * wmin: 笔画最小宽度
    * timeScale: xml字的时间缩放，时间->速度->宽度。
    * radius: 骨架字中小圆圈的半径
    * lineWidth: 骨架字的笔画的宽度
    * displayMode: 显示状态：标准字状态，骨架字状态，显示动画状态，标准宽度状态，笔画分颜色状态。
    * strokeColors: 笔画的可选颜色
    * isXML: xml文件标记
    * aspectWidth: canvas视窗的宽度
    * aspectRatio: canvas视窗的宽高比
    * charBoxWidth: 写单字区域的宽度
    * charRatio: 字占写字框的比例
    * startPosition: 写单字的开始位置
        
* 核心method：
    * changeXY(strokeArray, opts): 根据参数opts，更新strokeArray，目前可以改变单个点的x,y,w数值，因为t,p,v不影响字的绘制，所以没有更改。
    * drawAllPoint（canvas, strokeArray）: 依据字的坐标(x,y)和宽度w数据，绘制到指定canvas上。
外部只需要调用两个方法就OK。

### Idiom.js介绍
每个页面需要三部分， 改变参数之前的字的数组charArrayOrigin， 改变参数后的字的数组charArray和页面的控制参数opts。
有三个数据很关键， charArray是当前页中的所有字的数据，charArrayOrigin是当前页中的所有字的原始数据（参数未做改变）,charPointer当前操作的字的指针。
* Options:
默认的Opts:   
 
            aspectWidth:600,
            aspectRatio: 1/3,
            charBoxWidth: 150,
            charBoxRatio: 4/3   
各个参数的含义：
    * aspectWidth: canvas的宽度
    * aspectRatio: canvas的宽高比
    * charBoxWidth: 写单字区域的宽高比， 和Character的同名参数对应，需要传递过去。
    * charBoxRatio: 宽高比
* 核心method: 
    * addChar(strokeArray,opts): 先将传入的数据添加到charArrayOrigin,改变字的startPosition和charBoxWidth后，将数据添加到charArray中。
    * removeChar: charPointer向后移动一个单位
    * clearChar: 清除当前charPointer上的数据
    * getChar: 获取当前操作区域的字数据
    * getChar: 获取当前操作区域的字原始数据

### 如何使用和Demo

首先需要引入这三个js文件：
        
            <script src="js/lodash.min.js"></script>
            <script src = "js/character.js"></script>
            <script src = "js/idiom.js"></script>        

1 默认写字画布：

            html: <canvas id="canvas" >不支持</canvas>
            js: var canvas = document.getElementById('canvas');
                var char = new Character(canvas);      

2 canvas宽度为300和高斯系数为2.0的画布：
            
            html: <canvas id="canvas" >不支持</canvas>
            js: var canvas = document.getElementById('canvas');
                var char = new Character(canvas,{gaussian:2.0, aspectWidth:     300});            
图示：


3 宽度为400，可显示xml标准字的画布：

                html: <canvas id="canvas" >不支持</canvas>
                      <input type="file" id="xmlfile" name="xml" accept="application/xml" title="选择xml格式文字" />	 
                js: var canvas = document.getElementById('canvas');
                    var xmlFile = document.getElementById('xmlfile');
                    var char = new Character(canvas,{isXML:true, gaussian:2.0, },xmlFile );
图示：



          
4 默认成语画布：

                html: <canvas id="canvas" >不支持</canvas>
                js: var canvas = document.getElementById('canvas');
                    var char = new Idiom(canvas);
5 宽度aspectWidth变为400，单字charBoxWidth变为100的画布：
        
                html: <canvas id="canvas" >不支持</canvas>
                js: var canvas = document.getElementById('canvas');
                    var char = new Idiom(canvas,{aspectWidth:400,charWidth:100});
6 把写好的字添加到页面中：
                
                示意代码：
                idiom.addChar(char.strokeArray, char.opts);
				 char.changeXY(
					idiom.charArray[idiom.charPointer-1].strokeArray
					,
					idiom01.charArray[idiom.charPointer-1].opts
				);
				char.drawAllPoints(canvas, idiom.charArray[idiom01.charPointer-1].strokeArray);
				
OK~ 以上就是目前两个对象的介绍。 基本框架已经ok，后续可能会继续封装一些内容进去。 如字对象可以尝试把参数调节UI控件和显示状态UI控件都封装进去。完。