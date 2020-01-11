---

title: JS之typeof和instanceof方法具体实现
date: 2019-10-27 23:10
tags: JavaScript typeof instanceof
categories: JavaScript
---

# 一、typeof方法的作用
- 判断变量类型


        typeof 1 //"number"
        typeof '1' //"string"
        typeof {} //"object"
        typeof true //"boolean"
        typeof undefined // "undefined"
        typeof null //"object" #bug
        typeof Symbol //"function"
        typeof Symbol(1) //"symbol"

<!--more-->

# 二、typeof方法的具体实现
    
        if (JSVAL_IS_VOID(v)) {  // (1)
            type = JSTYPE_VOID;
        } else if (JSVAL_IS_OBJECT(v)) {  // (2)
            obj = JSVAL_TO_OBJECT(v);
            if (obj &&
                (ops = obj->map->ops,
                    ops == &js_ObjectOps
                    ? (clasp = OBJ_GET_CLASS(cx, obj),
                    clasp->call || clasp == &js_FunctionClass) // (3,4)
                    : ops->call != 0)) {  // (3)
                type = JSTYPE_FUNCTION;
            } else {
                type = JSTYPE_OBJECT;
            }
        } else if (JSVAL_IS_NUMBER(v)) {
            type = JSTYPE_NUMBER;
        } else if (JSVAL_IS_STRING(v)) {
            type = JSTYPE_STRING;
        } else if (JSVAL_IS_BOOLEAN(v)) {
            type = JSTYPE_BOOLEAN;
        }
    
根据 `type tags` 的信息，低位是 `000`，因此 `null` 被判断成了一个对象。这就是为什么 `typeof null` 的返回值是 `object`，[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof#null) 有简单的描述解释了原因，而且以后也不会修复这个 `bug` ，因为修复这个 `bug` 会带来更多的问题。

# 三、更准确的类型判断方法
由于 `typeof`存在 `null` 的类型为 `object`，所以可以用另外一种更加准确的方法来判断基本类型，那就是`Object.prototype.toString` ：
    
        Object.prototype.toString.call(2) // "[object Number]"

        Object.prototype.toString.call('hello') // "[object String]"

        Object.prototype.toString.call({hello:'hello'}) // "[object Object]"

        Object.prototype.toString.call([2,'hello']) // "[object Array]"

        Object.prototype.toString.call(true) // "[object Boolean]"

        Object.prototype.toString.call(() => {}) // "[object Function]"

        Object.prototype.toString.call(null) // "[object Null]"

        Object.prototype.toString.call(undefined) // "[object Undefined]"

        Object.prototype.toString.call(Symbol(2)) // "[object Symbol]"

    
# 四、instanceof方法的作用
- 判断一个引用是否属于某个构造函数
- 判断继承关系中一个实例是否属于其父类
比如：


        const Animal = function(type) {
          this.type = type;
        }
        const cat= new Animal('cat');
        console.log(cat instanceof Animal);  //true
        console.log(cat instanceof Object); //true
    
# 五、如何实现一个instanceof方法
    
        function myInstanceof(leftVaule, rightVaule) { 
            let rightProto = rightVaule.prototype; // 取右边 prototype的值
            leftVaule = leftVaule.__proto__; // 取左边__proto__值
            while (true) {
            	if (leftVaule === null) { //如果左边的__proto__值为null，返回false
                    return false;	
                }
                if (leftVaule === rightProto) { //如果左边的__proto__值等于右边prototype的值，返回true
                    return true;	
                } 
                leftVaule = leftVaule.__proto__ ; //以上都不满足，取上一层原型继续循环，直到没取到为null
            }
        }
    
其实说白了就是在实例的原型链上面去找，看到底有没有找到右边的原型，找到了就返回 `true`，没找到或者左边不存在原型链，就返回 `false`。
例子：
    
        function Foo() {
        }

        myInstanceof(Object, Object) // true
        myInstanceof(Function , Function)  // true
        myInstanceof(Function , Object ) // true
        myInstanceof(Foo, Foo) // false
        myInstanceof(Foo, Object) // true
        myInstanceof(Foo, Function) // true
    
# 六、总结
`typeof` 和 `instanceof` 可以用来判断一些基本类型及原型链的实例与原型之间的关系，但是使用的时候也需要很小心，因为一些潜在的问题会导致意外的问题出现，比如数组的判断：
    
        [1,2] instanceof Array //true
        [1,2] instanceof Object //true
    
这是因为
    
        Array instanceof  Object //true
    
可能这并不是我们想要的结果，所以可以使用更加准确的判断方法 `Object.prototype.toString.call`：
    
        Object.prototype.toString.call( [1,2,3] ) //"[object Array]"
    
此外，`instanceof ` 方法的具体实现需熟练掌握。

# 七、参考
[https://juejin.im/post/5b0b9b9051882515773ae714](https://juejin.im/post/5b0b9b9051882515773ae714)
