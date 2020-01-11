---

title: JS实现JQuery中的链式操作具体实现
date: 2019-07-15 22:41
tags: JavaScript JQuery
categories: JavaScript JQuery
---

- 前不久面试某大厂被问到关于JQuery中的链式操作的问题，才疏学浅的我一脸懵逼，这东西只是用过，怎么还要求自己动手实现呢，究其原因还是自己修为不够，达不到大厂的水平，平时“拿来主义”习惯了，也不会去深究其底层原理，可见学习思维与方式还需改进，今天就来好好学习一番。
- 网上查了一下，其原理其实很简单，就是在每次方法执行完后返回this对象，然后后面的方法就可以继续在this环境下执行。
- 然后自己动手实现一下：
  
        function Employee(){};
        // 在原型上定义对应的方法
        Employee.prototype ={
          setName:function(name){
            this.name = name;
            return this;
          },
          setAge:function(age){
            this.age = age;
            return this;
          },
          setSex:function(sex){
            this.sex = sex;
            return this;
          }
        }

        var employee= new Employee();
        employee.setName("Hal").setAge(24).setSex("male");
        // Employee {name: "Hal", age: 24, sex: "male"}
  
- 因为每次返回的结果都是*this*，所以 '.' 后面的方法就相当于每次都在调用*this*，如下：
    
        employee.setName("Hal") === employee.setName("Hal").setAge(24) // true
        employee.setName("Hal") === employee.setName("Hal").setAge(24).setSex("male") // true
    

- 优点
链式操作使得异步编程的流程更加清晰，不会像回调函数一样相互耦合，难以分辨函数的执行顺序且维护困难，实现了水平扩展。在ES6中，Promise也正是沿用了这一思想，每一个异步任务返回一个Promise对象，通过then方法指定回调函数。

- 缺点
只能应用在不需要返回值的情况下，或者说只能最后一步才能设置返回值，因为要支持链式调用返回的只能是*this*，但jQuery主要是对DOM元素进行操作，只需要改变DOM元素的表现而不需要返回值，所以适合链式操作。

- 参考
[https://www.cnblogs.com/youhong/p/6899466.html](https://www.cnblogs.com/youhong/p/6899466.html)

