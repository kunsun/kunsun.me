---
title: Javascript编码风格
layout: post
date: 2015/06/09

---
> 参照github上的[文章](https://github.com/airbnb/javascript/tree/master/es5)。权当是学习笔记。
<!--more-->

Anonymous function expressions hoist their variable name,but not the function assignment.

	function example() {
  		console.log(anonymous); // => undefined
  		anonymous(); // => TypeError anonymous is not a function
  		var anonymous = function() {
    		console.log('anonymous function expression');
  		};
	}
	
Function declarations hoist their name and the function body.

	function example() {
  		superPower(); // => Flying
  		function superPower() {
    		console.log('Flying');
  		}
	}
	
条件判断中使用shortcuts。

	// bad
	if (name !== '') {
  		// ...stuff...
	}
	// good
	if (name) {
	  // ...stuff...
	}
	// bad
	if (collection.length > 0) {
	  // ...stuff...
	}
	// good
	if (collection.length) {
	  // ...stuff...
	}
	
####comments
---
Use /** ... */ for multi-line comments. Include a description,specify types and values for all parameters and return values.

Place single line comments on a newline above the subject of the comment.Put an empty line before the comment.

Prefixing you comments with FIXME or TODO helps other developers quickly understand if you're pointing out a problem that need to be revisited, or if you're suggesting a solution to the problem that needs to be implemented.

####whitespace
---
Place 1 space before the leading brace.

Place 1 space before the opening parenthesis in control statements. 

Place no space before the argument list in function calls and declarations. 

Set off operators with spaces.

Leave a blank line after blocks and before the next statement.

####naming
---

Use a leading underscore _ when naming private properties.

When saving a reference to this use _this.

Name your function.This is helpful for stack traces.

####accessors
---
If you do make accesspr functions use getVal() and setVal('hello').

#### constructors
---
Assign methods to the prototype object, instead of overwriting the prototype with a new object. 

Methods can return this to help with method chaining.

#### jQuery
---
Prefix jQuery object variables with a $.

Cache jQuery lookups.

