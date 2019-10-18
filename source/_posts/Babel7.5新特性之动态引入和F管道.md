---

title: Babel 7.5新特性之动态引入和F#管道
date: 2019-08-26 23:19
comments: true
toc: true
tags: JavaScript Babel

---
![Babel](https://upload-images.jianshu.io/upload_images/1741029-16b74fc4346ad169.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 一、目录
- F#管道操作符
- 动态导入

# 二、F#管道操作符
管道操作符提议还在第一阶段的开发中，同时也意味着其规范还在定义中。Babel 从`7.3.0` 版本已经支持了管道的变形`Smart`，以及`7.0.0-beta`版本以来的`minimal`变形，并且现在又有了新的`F#`变形。

`F#`的变形不同于已经存在的`smart `变形，为何呢?与之前的话题引用（`topic references`）的概念相比，我们将使用箭头函数来代替。最明显的一个优势就是其更像当今的JavaScript ，并且有着更简洁的语法。换而言之，管道操作符是用在单个参数函数调用时非常有用的且必不可少的语法糖。
也就意味着：


		sqrt(64)
		
		// 等于
		
		64 |> sqrt


以上语法的好处就是当链接多个函数的时候可以使代码更具可读性，这就是管道操作符的最明显的作用。

### 考虑以下几行代码：


		function doubleSay (str) {
		  return str + ", " + str;
		}
		function capitalize (str) {
		  return str[0].toUpperCase() + str.substring(1);
		}
		function exclaim (str) {
		  return str + '!';
		}


以下是上面这些函数的调用：


		let result = exclaim(capitalize(doubleSay("hello")));
		console.log(result) //=> "Hello, hello!"


可以使用`F#`变形来优化：

		let result = "hello"
		  |> doubleSay
		  |> capitalize
		  |> exclaim;
		console.log(result) //=> "Hello, hello!"


### 多参数的`F#`
这种特殊解决方法的好处就是管道操作符不需对多参数函数做任何处理，只管扔给JavaScript就好了：


	    function double (x) { return x + x; }
		function add (x, y) { return x + y; }
		
		function boundScore (min, max, score) {
		  return Math.max(min, Math.min(max, score));
		}


可以使用` smart` 变形来调用：

		let person = { score: 25 };
		let newScore = boundScore( 0, 100, add(7, double(person.score)))
		console.log(newScore) //=> 57


管道操作符提供的新的或者说更好的与之前的` smart` 变形截然相反的方法是因为我们可以使用箭头函数，可以用来处理多参数函数，比如： `add()` 和 `boundScore()`：

		let person = { score: 25 };
		let newScore = person.score
		  |> double
		  |> (n => add(7, n))
		  |> (n => boundScore(0, 100, n));
		console.log(newScore) //=> 57

尽管可以用来处理多个参数的函数，但是通常推荐用来处理单参数函数，因为管道操作符每次只返回一个结果。

### 与部分应用语法（Partial Application Syntax）一起使用
部分应用语法提出（目前还在第一阶段），在一个通过参数占位符来实现允许部分应用参数列表来调用表达式的参数列表中使用了`?`操作符来引入的一种新的语法。
如果部分语法提议通过审核，也就意味着管道操作符就将会是一种更好更准确的处理之前提到过的函数的方法，即：

不用这么写：

		let person = { score: 25 };
		let newScore = person.score
		  |> double
		  |> (n => add(7, n))
		  |> (n => boundScore(0, 100, n));
		console.log(newScore) //=> 57

可以直接使用`?`操作符来替换任意参数来这样写：

		let person = { score: 25 };
		let newScore = person.score
		  |> double
		  |> add(7, ?)
		  |> boundScore(0, 100, ?);
		console.log(newScore) //=> 57

后者更加简单明了，但是先别高兴得太早，目前只是提议阶段，还未完全确定，拭目以待。

### 与Await一起使用
每一个提议对于管道中的`await`都有不同的解决方案，`F#`变形也不例外，它处理`await`的方式和`smart `有点类似但是也有不同之处：

		// Smart Pipeline
		let newScore = fetch(url)
		  |> await #
		  |> #.json()
		  |> await #
		  |> #.ID;

F# 管道引入箭头函数来处理这些案例：

		// F# pipe line
		let newScore = fetch(url)
		  |> await
		  |> r => r.json()
		  |> await
		  |> obj => obj.ID;

### 开始尝试 F# 管道操作符

可以简单修改`babel.config `文件然后加入管道操作符插件来使用：

		module.exports = {
		  plugins: [
		    ["@babel/proposal-pipeline-operator", { proposal: "fsharp" }]
		  ]
		};

### 更多关于此提议/特性
[https://babeljs.io/blog/2019/07/03/7.5.0#f-pipeline-operator-9450-https-githubcom-babel-babel-pull-9450-and-9984-https-githubcom-babel-babel-pull-9984]()

# 三、动态引入
在新提议之前：
1.如果你使用`webpack`或者`rollup`来实现动态引入，需要包含`@babel/plugin-syntax-dynamic-import `并且不能用`babel`转化
2.如果你使用`Node`，可以使用`babel-plugin-dynamic-import-node`插件来转化

在这里讨论的重点时，对于每一个模块来说，可以有不同的方法来转化。通常，是使用唯一的转化插件来转化模块的。大意是，新的动态导入的提议提供了一种结合上述几种用例的方法，一个单一的`entry point ` `@babel/plugin-proposal-dynamic-import`。

这样，我们就有了一个可以转化所有模块(Webpack, Roll up, Node 等等)可重用的插件。这个插件必须和模块转化插件一起使用，因为`Babel `需要识别你需要转化的是哪一种模块加载系统。

一个关于AMD有效配置的例子：

		module.exports = {
		  plugins: [
		    "@babel/plugin-proposal-dynamic-import",
		    "@babel/plugin-transform-modules-amd"
		  ]
		};

 `"@babel/plugin-transform-modules-amd"`指明了目标模块是AMD类型的，如果不指定的话，`Babel `将无法识别目标模块是 `Node` 还是 `Rollup`，如果你只是解析` import()`表达式，而不是转化的话，可以只指定` @babel/plugin-syntax-dynamic-import `。

动态引入在使用`@babel/preset-env`时默认开启，也无需考虑是否支持`webpack`或者`rollup `，`babel-loader` 和 `rollup-plugin-babel `都会为了使其他`bundler `正确处理`Babel`而自动关闭其转化功能。

# 四、参考
[原文](https://scotch.io/bar-talk/dynamic-imports-and-f-pipes-officially-land-in-babel-75)
