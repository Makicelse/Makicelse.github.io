---
title: 原生JavaScript各种知识点
date: 2024-10-18 09:29:00 +0800
categories: [前端, 基础]
tags: [前端]
---

### Symbol类型

在Js中有7中原始数据类型（包括undefined和null），Symbol类型就是其中一种。它们的值不可变。它们不是对象，也没有方法和属性。

调用Symbol()能够创建&返回一个symbol类型的值，返回的symbol值唯一。

这种“唯一”的用途在哪里呢？我每次取名用心点不就能保证变量值互相唯一了吗，就是麻烦了点而已？

——你不用再为了想一个“独一无二的变量名”而烦恼了！你随便取名，至于如何区分它们？交给ES6吧！（大概是这样？）

案例如下：

```js
const _counter = Symbol('counter');
		const _action  = Symbol('action');
		class Countdown {
   			 constructor(counter, action) {
        			this[_counter] = counter;
        			this[_action] = action;
    			}
   			 dec() {
       				let counter = this[_counter];
        			if (counter < 1) return;
			        counter--;
			        this[_counter] = counter;
			        if (counter === 0) {
			        	this[_action]();
        			}
    			}
		}

```

——这样，变量与变量之间的关系就更加简介明显，而不用为一串长长的变量名而绕花了眼。

  

------

  

### 事件对象Event

- 包含了用户输入信息的Js对象，在用户端有新输入时被创建，创建后不会被立即使用。

  “创建后不会被立即使用”大概说明的就是：事件对象在事件流中排队，等候被浏览器处理。

  比如你噼里啪啦敲下一串键盘按键时，即使延迟间隔再微小，所有输入被执行也总有先后之分。

  

#### 事件对象生命周期

事件对象从创建、传播到被销毁的大概过程：

1. 事件对象在创建后，会被传播到相应的DOM元素，传播期间分事件捕获、目标、冒泡阶段。

2. 浏览器首先从根节点到目标元素，然后从目标元素开始冒泡。

   - 在每一个传播的节点上，浏览器会检查是否有注册在该阶段的监听器（捕获阶段监听和冒泡阶段监听是不同的）。若该节点有监听器，浏览器会调用监听器并传递事件对象。

   所以意味着用户端每有一次新输入，所有的事件监听器都会触发一遍（一般情况下）。

   - 浏览器会将事件对象作为第一个参数，自动传递给监听器函数，监听器可以直接接受并处理这个对象。

   这也意味着：识别何是事件对象的是浏览器，浏览器内核干的活和OS干的其实差不多。（既然是OS干的活，就不需要多操心了，除非想要继续深入底层）

3. 当事件冒泡到最顶层、完成所有监听器的处理后，事件对象的生命周期结束，该事件对象会被销毁、内存回收（一般情况下）。   

<br>

教教我时间！~~希耶尔老师~~：

一、在事件传播过程中，浏览器只要检测到有注册了的事件监听器，就会将事件对象传递给监听器。那这是否意味着：一般情况下，每有一个新的事件对象被传递，所有的事件监听器都会被触发一遍？

——不会。只有注册了同一事件类型的监听器才会被触发。

比如都是“click”、都是“mouseout”等等。而当你鼠标click时，mouseout类型的监听器则不会触发。    

  <br>

二、用户和浏览器之间的交互频繁地更新的话，过多的事件监听器会影响浏览器性能吗？还是浏览器内部已经准备了优化方法？

——有影响性能的可能性。并且对此，现代浏览器也有多种优化性能的方法。如：
	1. 使用事件委托：将监听器绑定在父元素上，从而能够只用一个监听器统一处理子元素中多个按钮的点击。
	2. 惰性事件监听器
	3. 防抖、节流：减少高频率事件的触发次数。
	  并且，我们要及时移除不再需要的事件监听器，避免内存泄露和不必要的计算。



------



### 箭头函数

- 箭头函数中的this指向

  [【JavaScript箭头函数与普通函数的区别 - Web前端工程师面试题讲解】 ](https://www.bilibili.com/video/BV1Sp4y1U7FG/?share_source=copy_web&vd_source=e51e11077d736ca87fca4ff295f52dc2)

  <br>

  - 普通this的值：指向调用该方法的对象。**通过谁调用它**，它就指向谁。

  为什么说“通过谁调用它”？

  ——假如在JS中直接调用一个函数的话，那函数中的this值则直接指向全局对象（window对象）。如下：

  ```js
  function foo() {
      console.log(this);
  }
  
  foo();  // 输出: window (在浏览器中)
  
  ```

  <br>

  - 箭头函数中的this值：从外部函数中获取的，一直指向定义该函数的位置。也即：从箭头函数方法被定义的那刻起，this的值就确定了，一般情况下就永远不会变。

  看看Stack Overflow上的解释：

  <https://stackoverflow.com/questions/28371982/what-does-this-refer-to-in-arrow-functions-in-es6>

  > Arrow functions **capture** the "this" value from the enclosing context.
  >
  > ...`this` inside your arrow function would **have the same value as** it did right before the arrow function was assigned.  

  ——大概即：箭头函数的this值，和“定义该箭头函数的区域所拥有的this值”是一样的。

  可见千古教程中的例子&解说：[箭头函数的 this 的指向](https://web.qianguyihao.com/05-JavaScript%E5%9F%BA%E7%A1%80%EF%BC%9AES6%E8%AF%AD%E6%B3%95/06-ES6%EF%BC%9A%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0.html#%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0%E7%9A%84-this-%E7%9A%84%E6%8C%87%E5%90%91)

  this永远不变有什么好处吗？？

  ——可以确保函数在任何情况下都能正确的引用该对象，而不会由于上下文的变化导致错误。



------



### call、apply和bind函数

- 功能：在调用函数本身的同时，显式绑定this的值。

```js
function greet(greeting, punctuation) {
    console.log(greeting + ', ' + this.name + punctuation);
}

const person = { name: 'Alice' };

greet.call(person, 'Hello', '!');	/*  */

```

apply和call的区别只在于：接受的参数为单个or数组。

bind()：并非调用，而是返回一个新函数。该新函数的this值永远固定为指定的对象。  



-------



### 链式调用

有一个有趣的点：链式调用。

```js
ladder.up().up().down().showStep().down().showStep(); // 展示 1，然后 0
```

鉴于之前听过作用域链等类似的概念，这种调用链是基于什么原理？

——return this：每次执行完成后返回自己/新的自己，这样可以确保后续的方法仍然在当前环境下执行。



------



### 回调函数

[回调函数- MDN Web 文档术语表](https://developer.mozilla.org/zh-CN/docs/Glossary/Callback_function)

> **回调函数**是作为参数传递到另一个函数中，然后在外部函数内调用以完成某种例行程序或操作的函数。

[回调函数- 维基百科，自由的百科全书](https://zh.wikipedia.org/zh-hans/回调函数)

> 回调函数或简称回调（callback），**是计算机编程中对某一段可执行代码的引用，它被作为参数传递给另一段代码；预期这段代码将回调（执行）这个回调函数作为自己工作的一部分**。
>
>  这种执行可以是即时的，如在同步回调之中；也可以在后来的时间点上发生，如在异步回调之中。

在JavaScript中，函数也是对象，同样也可作为参数传递。

举例：当函数A被传入函数B后，会根据上下文或某个事件，在适当的时机调用被传入的函数A。我们可以说“过一段时间，回头再调用函数A”，即“回调函数”。



------



### Promise

[【异步编程: 一次性搞懂 Promise, async, await (#js #javascript)】](https://www.bilibili.com/video/BV1WP4y187Tu/?share_source=copy_web&vd_source=e51e11077d736ca87fca4ff295f52dc2)  

- 为什么要有Promise？

因为回调函数的嵌套。

那么，什么原因导致的回调函数嵌套过深呢？

[回调地狱的今生前世@JavaScript #17 - rccoder/blog](https://github.com/rccoder/blog/issues/17)

> JavaScript 由于某种原因是被设计为单线程的，同时由于 JavaScript 在设计之初是用于浏览器的 GUI 编程，这也就需要线程不能进行阻塞。
>
> 所以在后续的发展过程中基本都采用异步非阻塞的编程模式。

[什么是回调地狱？ - Hi! Tmiracle](https://blog.namichong.com/learn/web/javascript/what_is_callback_hell.html)  

> 多个异步操作需要顺序执行的情况下，每个异步操作都需要在上一个异步操作完成后才能执行， 而这些操作又需要通过回调函数来进行处理。

每个步骤我们虽然知道它们之间的执行逻辑，但不知道它们何时执行。所以它们要分开来写，不能写一块。

且每个步骤通常都要依赖前一步的结果，因此需要嵌套调用。

```js
function firstTask(callback) {
    setTimeout(() => {
        console.log("Task 1 done");
        callback();
    }, 1000);
}  // Task 1 done

function secondTask(callback) { ... }  // Task 2 done

function thirdTask(callback) { ... }  // Task 3 done

// 嵌套回调
firstTask(() => {
    secondTask(() => {
        thirdTask(() => {
            console.log("All tasks completed");
        });
    });
});
                              
// Promise 链式调用
firstTask()
    .then(() => secondTask())
    .then(() => thirdTask())
    .then(() => {
        console.log("All tasks completed");
    });

// 输出：
// Task 1 done
// Task 2 done 
// Task 3 done                              
// All tasks completed                              
```

这样任务一多的话，套娃再套娃，就会形成所谓的“回调地狱”，代码维护难度太大。

而Promise用链式结构将每个异步回调函数的结果串联起来，大大提高了代码的阅读性和可维护性。

<br>

- how to use it？

[Using promises - JavaScript - MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)



------



### Fetch API

[Fetch API - JavaScript前端Web工程师](https://www.bilibili.com/video/BV1434y1o74Q/)



### DOM

#### shadow DOM

#### 虚拟DOM



### 通过JS操作样式
