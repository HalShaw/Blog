---

title: 你不知道的JavaScript中卷-读书笔记
date: 2019-10-07 21:47
comments: true
toc: true
tags: JavaScript Notes

---

![JavaScript](https://upload-images.jianshu.io/upload_images/1741029-8c92a4538cc134eb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 基本类型值

- 有一个坑常常被提到，[] + {}，{} +[]，它们返回不同的结果，分别是"[object Object]"，0。

- if (a) { foo(); }可以写成a && foo()a = b || "something" 和a && b() 用到了“短路”机制。

- == 允许在相等比较中进行强制类型转换，===不允许。
- NaN 不等于NaN
- +0 等于-0


- 我个人建议无论什么情况下都不要使== true 和 == false。
- (1) 如果x 为null，y 为undefined，则结果为true。
- (2) 如果x 为undefined，y 为null，则结果为true。


	    "0" == null; // false
    	"0" == undefined; // false
	    "0" == false; // true -- 晕！
	    "0" == NaN; // false
	    "0" == 0; // true
	    "0" == ""; // false
	    false == null; // false
	    false == undefined; // false
	    false == NaN; // false
	    false == 0; // true -- 晕！
	    false == ""; // true -- 晕！
	    false == []; // true -- 晕！
	    false == {}; // false
    
	    "" == null; // false
	    "" == undefined; // false
	    "" == NaN; // false
	    "" == 0; // true -- 晕！
	    "" == []; // true -- 晕！
	    "" == {}; // false
	    0 == null; // false
	    0 == undefined; // false
	    0 == NaN; // false
	    0 == []; // true -- 晕！
	    0 == {}; // false
	    
	    "0" == false; // true -- 晕！
	    false == 0; // true -- 晕！
	    false == ""; // true -- 晕！
	    false == []; // true -- 晕！
	    "" == 0; // true -- 晕！
	    "" == []; // true -- 晕！
	    0 == []; // true -- 晕！


- [] == ![] // true
- 根据ToBoolean 规则，它会进行布尔值的显式强制类型转换（同时反转奇偶校验位）。所以[] == ![] 变成了[] == false。前面我们讲过false == []，最后的结果就顺理成章了。

    2 == [2]; // true
    "" == [null]; // true

- 如果两边的值中有true 或者false，千万不要使用==。
- 如果两边的值中有[]、"" 或者0，尽量不要使用==。

- 字符串常量中允许的最大字符数（并非只是针对字符串值）；
- 可以作为参数传递到函数中的数据大小（也称为栈大小，以字节为单位）；
- 函数声明中的参数个数；
- 未经优化的调用栈（例如递归）的最大层数，即函数调用链的最大长度；
- JavaScript 程序以阻塞方式在浏览器中运行的最长时间（秒）；
- 变量名的最大长度。

- 对多数开发者来说，错误处理最自然的形式就是同步的try..catch 结构。遗憾的是，它只能是同步的，无法用于异步代码模式。

# Promise

- 传给Promise.all([ .. ]) 的数组中的值可以是Promise、thenable，甚至是立即值。就本质而言，列表中的每个值都会通过Promise.resolve(..) 过滤，以确保要等待的是一个真正的Promise，所以立即值会被规范化为为这个值构建的Promise。

- 与Promise.all([ .. ]) 类似，一旦有任何一个Promise 决议为完成，Promise.race([ .. ])就会完成；一旦有任何一个Promise 决议为拒绝，它就会拒绝。
- Promise.race([ .. ]) 也接受单个数组参数。这个数组由一个或多个Promise、thenable 或立即值组成。要注意，永远不要递送空数组。

    p.then( fulfilled );
    p.then( fulfilled, rejected );
    p.catch( rejected ); // 或者p.then( null, rejected )

- 对Promise.all([ .. ]) 来说，只有传入的所有promise 都完成，返回promise 才能完成。如果有任何promise被拒绝，返回的主promise就立即会被拒绝（抛弃任何其他promise 的结果）。

- 当心！若向Promise.all([ .. ]) 传入空数组，它会立即完成，但Promise.race([ .. ]) 会挂住，且永远不会决议。
    
	    var p1 = Promise.resolve( 42 );
	    var p2 = Promise.resolve( "Hello World" );
	    var p3 = Promise.reject( "Oops" );
	    Promise.race( [p1,p2,p3] )
	    .then( function(msg){
	    console.log( msg ); // 42
	    } );
	    Promise.all( [p1,p2,p3] )
	    .catch( function(err){
	    console.error( err ); // "Oops"
	    } );
	    Promise.all( [p1,p2] )
	    .then( function(msgs){
	    console.log( msgs ); // [42,"Hello World"]
	    } );

- Promise 局限性:
- 顺序错误处理，如果没有错误处理，错误会一直往下传递
- 单一值
- 单决议，Promise 只能被决议一次（完成或拒绝）
- 惯性，运动状态（使用回调的）的代码库会一直保持运动状态（使用回调的），直到受到一位聪明的、理解Promise 的开发者的作用。
- 无法取消，一旦创建了一个Promise 并为其注册了完成和/ 或拒绝处理函数，如果出现某种情况使得这个任务悬而未决的话，你也没有办法从外部停止它的进程。

# 生成器

- 生成器就是一类特殊的函数，可以一次或多次启动和停止，并不一定非得要完成。
yield .. 和next(..) 这一对组合起来，在生成器的执行过程中构成了一个双向消息传递系统。
使用生成器的话，交替执行（甚至在语句当中！）显然是可能的。

- 生成器实现标准的迭代器接口:


	    var something = (function(){
	    var nextVal;
	    return {
	    // for..of循环需要
	    [Symbol.iterator]: function(){ return this; },
	    // 标准迭代器接口方法
	    next: function(){
	    if (nextVal === undefined) {
	    nextVal = 1;
	    }
	    else {
	    nextVal = (3 * nextVal) + 6;
	    }
	    return { done:false, value:nextVal };
	    }
	    };
	    })();
	    
	    something.next().value; // 1
	    something.next().value; // 9
	    something.next().value; // 33
	    something.next().value; // 105

- yield 委托的主要目的是代码组织，以达到与普通函数调用的对称。

- 生成器是ES6 的一个新的函数类型，它并不像普通函数那样总是运行到结束。取而代之的是，生成器可以在运行当中（完全保持其状态）暂停，并且将来再从暂停的地方恢复运行。
- 这种交替的暂停和恢复是合作性的而不是抢占式的，这意味着生成器具有独一无二的能力来暂停自身，这是通过关键字yield 实现的。不过，只有控制生成器的迭代器具有恢复生成器的能力（通过next(..)）。
- yield/next(..) 这一对不只是一种控制机制，实际上也是一种双向消息传递机制。yield .. 表达式本质上是暂停下来等待某个值，接下来的next(..) 调用会向被暂停的yield 表达式传回一个值（或者是隐式的undefined）。
- 在异步控制流程方面，生成器的关键优点是：生成器内部的代码是以自然的同步/ 顺序方式表达任务的一系列步骤。其技巧在于，我们把可能的异步隐藏在了关键字yield 的后面，把异步移动到控制生成器的迭代器的代码部分。
- 换句话说，生成器为异步代码保持了顺序、同步、阻塞的代码模式，这使得大脑可以更自然地追踪代码，解决了基于回调的异步的两个关键缺陷之一。