---
title: this动态绑定
categories:
    - - Learning
      - JavaScript
date: 2020-09-08 21:05:00
tags:
---

> 《你所不知道的 JavaScript 上卷》 第二部分 this 和对象原型

JavaScript **不具有动态作用域**，只有静态的语法作用域。

动态作用域是在运行时基于调用栈的，语法作用域是代码中确定的作用域嵌套。

### this 的绑定规则

1.函数是否在 new 中调用（new 绑定）？如果是的话 this 绑定的是新创建的对象。

```js
var bar = new foo();
```

2.函数是否通过 call、apply（显性绑定）或硬绑定调用？如果是的话，this 绑定的是指定的对象。

```js
var bar = foo.call(obj2);
```

3.函数是否在某个上下文对象中调用（隐性绑定）？如果是的话，this 绑定的是那个上下文对象。

```js
var bar = obj1.foo();
```

4.如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到 undefined，否则绑定到全局对象。

```js
var bar = foo();
```

### 绑定例外

1.如果函数通过 call、apply 或 bind 调用时时传入对象为 null 或 undefined ，这些值会被忽略，实际应用的是默认绑定规则。

```js
function foo(a, b) {
	console.log("a:" + a + ",b:" + b);
}

foo.apply(null, [2, 3]); //a:2,b:3

var bar = foo.bind(null, 2);
bar(3); //a:2,b:3
```

2.函数的间接引用，调用这个函数会应用默认绑定规则。

```js
function foo() {
	console.log(this.a);
}

var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4 };

o.foo(); // 3
(p.foo = o.foo)(); //2

//p.foo = o.foo 的返回值是目标函数的引用，因此调用位置是 foo() 而不是 p.foo() 或者 o.foo()。因此会应用默认绑定。
```

3.箭头函数 ()=>{..} 不使用 this 的四种标准规则，而是**根据外层（函数或全局）作用域来决定 this**。

```js
function foo() {
	setTimeout(() => {
		//this继承自foo()
		console.log(this.a);
	}, 100);
}

function bar() {
	var self = this;
	setTimeout(function () {
		console.log(self.a);
	}, 100);
}

var obj = { a: 2 };
foo.call(obj); //2
bar.call(obj); //2
```
