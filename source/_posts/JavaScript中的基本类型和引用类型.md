---

title: JavaScript中的基本类型和引用类型
date: 2020-01-14 00:01
tags: JavaScript 基本类型 引用类型
categories: JavaScript 

---

# 一、前言

`ECMAScript`变量包含两种不同数据类型的值，一种是**基本类型**，另一种是**引用类型**。**基本类型**是简单是数据段，不能再有其他属性值；**引用类型**是指可以有多个值的对象。

`ECMAScript`里面有六种基本数据类型：**Undefined、Null、Boolean、Number 、String、Symbol**。这几种类型是按值访问的，因为可以操作保存在**栈**中变量的实际的值。

引用类型的值是保存在**堆**中的对象，和其他语言不一样的是，`ECMAScript`不允许直接访问内存中的位置，即不能直接操作对象内存空间，所以在操作对象时，其实是在操作对象的引用，而不是实际的对象，所以引用类型的值是按引用访问的。

# 二、复制变量值

基本类型会在变量对象上创建一个新值，然后把该值复制到为新变量分配的位置上。

引用类型同样也会将存储在变量对象中的值复制一份放到为新变量分配的空间中，但是这个值的副本实际上是一个指针，指向原来的对象。

<!--more-->

# 三、传递参数

`ECMAScript` 中所有函数的参数都是按值传递的。也就是说，把函数外部的值复制给函数内部的参数，就和把值从一个变量复制到另一个变量一样。基本类型值的传递如同基本类型变量的复制一样，而引用类型值的传递，则如同引用类型变量的复制一样。
看下面的例子：

```
function setName(obj) {
    obj.name = "Hal";
    obj = new Object();
    obj.name = "Greg";
}

var person = new Object();
setName(person);
alert(person.name); //"Hal"
```
上面的代码中在把`person` 传递给`setName()`后，其`name` 属性被设置为`"Hal"`。然后，又将一个新对象赋给变量`obj`，同时将其`name`属性设置为`"Greg"`。如果是按引用传递的，那么`person` 就会自动被修改为指向其`name` 属性值为 `"Greg"`的新对象。但是并没有，这说明即使在函数内部改变了参数的值，但其原始引用依然保持不变。
实际上，当在函数内部重写`obj` 时，这个变量引用的就是一个局部对象了，而这个局部对象会在函数执行完毕后立即被销毁。

# 四、检测类型

基本类型的检测可以使用`typeof`·操作符，引用类型的检测可以使用`instanceof`操作符。

```
var s = "H";
var b = true;
var i = 24;
var u;
var n = null;
var o = new Object();

alert(typeof s); //string
alert(typeof i); //number
alert(typeof b); //boolean
alert(typeof u); //undefined
alert(typeof n); //object
alert(typeof o); //object
DeterminingTypeExample01
```

`typeof null`返回的是`object`。

```
var person = {};
var colors = [];
var pattern = new RegExp();

alert(person instanceof Object); // true
alert(colors instanceof Array); // true
alert(pattern instanceof RegExp); // true
```

**所有引用类型的值都是Object 的实例**，也就是说所有引用类型加` instanceof Object`都会返回`true`，所有基本类型使用` instanceof`都会返回`false`，因为基本类型不是对象。