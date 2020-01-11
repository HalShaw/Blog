---

title: 深入理解Javascript中的Prototype原型
date: 2019-07-17 22:25
tags: JavaScript Prototype
categories: JavaScript
---

- 刚开始学Javascript的时候觉得很简单，其中的东西也就是那是基本的，但是还是太年轻，最近因为面试中常被问到关于原型和原型链的问题，然后自己也是模棱两可的，含含糊糊说不清楚，所以痛下思定，决定好好下一番功夫来搞懂吃透原型与原型链究竟是什么。

# 关于`new`

`new`的过程

>- 新生成了一个对象
>- 链接到原型
>- 绑定 this
>- 返回新对象

知道了`new`的原理后，也可以自己实现一个`new`函数：

	
		function myNew() {
			// 创建一个空的对象
			let obj = new Object()
			// 获得构造函数
			let Con = [].shift.call(arguments)
			// 链接到原型
			obj.__proto__ = Con.prototype
			// 绑定 this，执行构造函数
			let result = Con.apply(obj, arguments)
			// 确保 new 出来的是个对象
			return typeof result === 'object' ? result : obj
		}
	
<!--more-->

先来看几个例子：
- 普通函数
	

		function test(name){
		    console.log(name);  
		}
		typeof(test) // function
	
- 使用*new*新建对象
	

		function test(name){
		    console.log(name);  
		}

		var te = new test("Hal");
		typeof(te) // object
		console.log(JSON.stringify(test.prototype)) //{}，是个空对象
		console.log(JSON.stringify(te.prototype)) //undefined
	
- 直接声明的函数拥有`prototype`这个属性，而`new`构造出来的函数不存在`prototype`。

# 关于`prototype`

每一个JavaScript对象(null除外)在创建的时候就会与之关联另一个对象  `prototype`，这个对象就是我们所说的`prototype`，每一个对象都会从`prototype`"继承"属性。除了`new`出来的函数，每个函数都有一个`prototype `属性，如下，在`prototype`上可以设置属性，然后打印出一些东西：
	

		function Employee() {}

		// prototype是函数才会有的属性
		Employee.prototype.name = 'Hal';
		var employee1 = new Employee();
		var employee2 = new Employee();
		console.log(employee1.name) // Hal
		console.log(employee2.name) // Hal
		console.log(employee1.constructor) // Employee() {}
		console.log(employee1.constructor.name) // Employee
		console.log(employee1.prototype) // undefined
		console.log(employee1.__proto__) // {name: "Hal", constructor: ƒ}
		console.log(Employee.prototype) // {name: "Hal", constructor: ƒ}
		console.log(Employee.prototype === employee1.__proto__) // true
		console.log(Employee.prototype.constructor === Employee) // true
	
# 关于`__proto__`

从刚才的例子中已经可以很清楚地看出了，employee1的`__proto__`和Employee.prototype是相等的，也就是指向同一个对象，也就是说，实例中有一个`__proto__`指针指向构造函数的`prototype`。使用`Object.getPrototypeOf`与`__proto__`是同样的效果。
	

		Object.getPrototypeOf(employee1) === Employee.prototype //true
	

# 关于`constructor `

从上面可以看到，原型`prototype`里面还有一个`constructor `的属性，然后这个`constructor `又指向了函数本身。
好像有一点绕，但是看一下下面的图就一目了然了：
![原型链](https://upload-images.jianshu.io/upload_images/1741029-f6343d487e11fe0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 总结
- `Object` 是所有对象的祖先，所有对象都可以通过 `__proto__` 找到它
- `Function` 是所有函数的祖先，所有函数都可以通过 `__proto__ `找到它
- `Function.prototype` 和 `Object.prototype` 是两个特殊的对象，由引擎创建，而且`Function.__proto__.__proto__`是指向`Object.prototype`的
即：
```Function.__proto__.__proto__ === Object.prototype // true```
- 除了以上两个特殊对象，其他对象都是通过构造器 new 出来的
函数的 `prototype` 是一个对象，也就是原型
- 对象的 `__proto__` 指向原型， `__proto__` 将对象和原型连接起来组成了原型链

# 参考
- http://blog.ifyouseewendy.com/blog/2017/07/03/review-you-dont-know-js-this-and-object-prototypes/#what-is-the-prototype-
- https://github.com/KieSun/Dream/issues/2
- https://github.com/mqyqingfeng/Blog/issues/2