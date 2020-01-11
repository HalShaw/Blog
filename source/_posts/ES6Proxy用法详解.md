---

title: ES6 Proxy用法详解
date: 2019-07-11 22:02
tags: ES6 Proxy
categories: JavaScript ES6
---

![Proxy](https://upload-images.jianshu.io/upload_images/1741029-96fbd7ec39c2fcb4.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**前不久换工作的时候面试某大厂被问到关于Proxy的问题，脑子里有点印象但是又说不出具体使用方法，主要还是自己平时积累不够，所以赶紧来恶补一下。**
# Metaprogramming

正式开始之前，先一起来理解下什么是Metaprogramming（元编程），因为Proxy就是一种基于Metaprogramming概念的对象 ，维基百科上这样解释：
>Metaprogramming is a programming technique in which computer programs have the ability to treat other programs as their data.

翻译过来就是

>元编程就是可以使用其他程序来作为输入数据的一种编程技术

# Proxy
>Proxy wraps objects and intercepts their behavior through traps

>The Proxy object is used to define custom behavior for fundamental operations (e.g. property lookup, assignment, enumeration, function invocation, etc).

<!--more-->

简单说来就是Proxy可以用来改变对象的默认操作，比如自行定义set和get等，常用的有以下这些：
- apply
- construct
- defineProperty
- deleteProperty
- get
- getOwnPropertyDescriptor
- getPrototypeOf
- has
- isExtensible
- ownKeys
- preventExtensions
- set
- setPrototypeOf

Proxy中需要理解的三个属性：
- target: an Object which the proxy virtualizes.（目标对象）
- handler: a Placeholder Object which contains traps.（包含重写方法的对象）
- trap: the Methods that provide property access of the target object.（重写的方法，比如get和set）

# 例子

新建一个Proxy对象
    
        let p = new Proxy(target, handler);
    
新建一个employee对象然后输出其中的一些属性
    
        const employee = {
            firstName: 'Tapas',
            lastName: 'Adhikary'
        };

        console.group('employee');
        console.log(employee.firstName);
        console.log(employee.lastName);
        console.log(employee.org);
        console.log(employee.fullName);
        console.groupEnd()
    
上面的输出如下所示：
    
        employee
        Tapas
        Adhikary
        undefined
        undefined
    
![输出结果](https://upload-images.jianshu.io/upload_images/1741029-14ae06c68fdb9b67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来使用Proxy来更改一些默认行为：

- 第一步 新建一个使用trap的handler
以下是一个覆盖get方法的handler，当target中取不到相应的fieldName时，你可以自定义输出:
    
        let handler = {
            get: function(target, fieldName) {        

                if(fieldName === 'fullName' ) {
                    return `${target.firstName} ${target.lastName}`;
                }

                return fieldName in target ?
                    target[fieldName] :
                        `No such property as, '${fieldName}'!`

            }
        };
    
- 第二步 新建一个Proxy对象，并把之前定义的employee作为target传入
    
        let p = new Proxy(employee, handler);
    
- 第三步 输出p的属性
    
        console.group('proxy');
        console.log(p.firstName);
        console.log(p.lastName);
        console.log(p.org);
        console.log(p.fullName);
        console.groupEnd()
    
然后就可以看到输出结果和之前不一样了，期望结果与实际结果如下所示：
    
        proxy
          Tapas
          Adhikary
          No such property as, 'org'!
          Tapas Adhikary
    
![输出结果](https://upload-images.jianshu.io/upload_images/1741029-62701d1cce29ae4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
现在，清楚Proxy的作用了吧，其实就是用来更改对象默认方法的。

下面是改变set方法来实现一个验证的例子：
新建一个handler，重新命名为validator：
    
        const validator = {
            set: function(obj, prop, value) {
                if (prop === 'age') {
                    if(!Number.isInteger(value)) {
                        throw new TypeError('Age is always an Integer, Please Correct it!');
                    }
                    if(value < 0) {
                        throw new TypeError('This is insane, a negative age?');
                    }
                }
            }
        };
    
然后再新建一个Proxy：
    
        let pr = new Proxy(employee, validator);
    
然后可以尝试设置一个不合法的属性，比如：
    
        pr.age = "test"；
    
你将会得到如下报错：
    
        Uncaught TypeError: Age is always an Integer, Please Correct it!
            at Object.set (<anonymous>:5:23)
            at <anonymous>:1:7
        set @ VM2381:5
        (anonymous) @ VM2434:1
    
或者：
    
        pr.age = -1；
    
结果：
    
        Uncaught TypeError: This is insane, a negative age?
            at Object.set (<anonymous>:8:23)
            at <anonymous>:1:7
        set @ VM2381:8
        (anonymous) @ VM2531:1
    
由上可见，Proxy的功能还是很强大也非常实用，常用应用场景有：
- 保护ID字段不受删除（重写deleteProperty）
- 取值和写值（数据绑定）（重写get，set）
- 改变 in 操作的默认行为等

# this指向
需要注意的是，proxy会改变target中的this指向，一旦proxy代理了target，target内部的this则指向了proxy，而不是target。
    
        const target = {
          get: function () {
            console.log(this === proxy);
          }
        };
        const handler = {};
        const proxy = new Proxy(target, handler);

        target.get() // false
        proxy.get()  // true
    
而有些原生对象的内部属性，只有通过正确的this才能拿到，所以proxy也无法代理这些原生对象的属性。
    
        const target = new Date();
        const handler = {};
        const proxy = new Proxy(target, handler);

        proxy.getDate();
        // TypeError: this is not a Date object.
    
对于以上的代码，getDate方法只能在Date对象的实例上面拿到，如果this不是Date就会报错。可以通过this绑定原始对象来解决这个问题。
    
        const target = new Date('2019-07-11');
        const handler = {
          get(target, prop) {
            if (prop === 'getDate') {
              return target.getDate.bind(target);
            }
            return Reflect.get(target, prop);
          }
        };
        const proxy = new Proxy(target, handler);

        proxy.getDate() // 11
    
这里又引入了另一个ES6的新增的对象Reflect，Reflect对象就是用来获取对象中默认方法的，这个下篇文章再具体讨论。

# 总结

Proxy可以用于更改方法的默认输出，比如get方法取不到值时不返回undefined，以及在set方法上加验证及数据绑定等等，很强大也有用，需熟练掌握。

# 参考
[Tapas Adhikary](https://blog.greenroots.info/metaprogramming-an-introduction-to-javascriptes6-proxy-cjwkk64ly000gvds10db4l034)
[Proxy - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
[http://es6.ruanyifeng.com/#docs/proxy](http://es6.ruanyifeng.com/#docs/proxy)


