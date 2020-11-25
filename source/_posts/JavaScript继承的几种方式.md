---

title: JavaScript继承的几种方式
date: 2020-01-12 15:56
tags: JavaScript 继承
categories: JavaScript

---

# 一、前言

许多OO（面向对象） 语言都支持两种继承方式：**接口继承**和**实现继承**。接口继承只继承方法签名，而实现继承则继承实际的方法。由于函数没有签名，在ECMAScript 中无法实现接口继承。所以ECMAScript 只支持实现继承，而且其主要是依靠原型链来实现继承。下面是几种JavaScript的继承方式。

# 二、原型链实现继承

其基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。**具体操作就是让子类型的原型等于父类型的实例**，然后此时的原型对象将包含一个指向另一个原型的指针，另一个原型中也包含着一个指向另一个构造函数的指针。假如另一个原型又是另一个类型的实例，那么上述关系依然成立，如此层层递进，就构成了实例与原型的链条。这就是原型链的基本概念。具体实现如下：

<!--more-->

```
function SuperType(){
    this.property = true;
}
SuperType.prototype.getSuperValue = function(){
    return this.property;
};
function SubType(){
    this.subproperty = false;
}

//子类的原型等于父类的实例，继承了SuperType
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function (){
    return this.subproperty;
};
var instance = new SubType();
alert(instance.getSuperValue()); //true
```
在上面的代码中，搜索过程就是沿着原型链向上查找，调用`instance.getSuperValue()`会经历三个搜索步骤：
- 1.搜索实例
- 2.搜索`SubType.prototype`
- 3.搜索`SuperType.prototype`，最后一步才会找到该方法。在找不到属性或方法的情况下，搜索到原型链末端才会停下来。

### 确定原型和实例的关系

可以通过两种方式来确定原型和实例之间的关系：
- `instanceof`操作符
- `isPrototypeOf()`

第一种方式是使用`instanceof` 操作符，只要用这个操作符来测试实例与原型链中出现过的构造函数，结果就会返回`true`：

```
alert(instance instanceof Object); //true
alert(instance instanceof SuperType); //true
alert(instance instanceof SubType); //true
```

第二种方式是使用`isPrototypeOf()`方法。只要是原型链中出现过的原型，都可以说是该原型链所派生的实例的原型，因此`isPrototypeOf()`方法也会返回true：

```
alert(Object.prototype.isPrototypeOf(instance)); //true
alert(SuperType.prototype.isPrototypeOf(instance)); //true
alert(SubType.prototype.isPrototypeOf(instance)); //true
```

**！所有引用类型默认都继承了`Object`对象，而这个继承也是通过原型链实现的。所有函数的默认原型都是`Object` 的实例，因此默认原型都会包含一个内部指针，指向`Object.prototype`。这也正是所有自定义类型都会继承`toString()`、`valueOf()`等默认方法的根本原因。**

**！子类型有时候需要重写超类型中的某个方法，或者需要添加超类型中不存在的某个方法。但是原型添加方法的代码一定要放在替换原型的语句之后，不然会被覆盖。**

**！在通过原型链实现继承时，不能使用对象字面量创建原型方法，因为这样做就会重写原型链。**

如下：

```
function SuperType(){
    this.property = true;
}
SuperType.prototype.getSuperValue = function(){
    return this.property;
};
function SubType(){
    this.subproperty = false;
}

//继承了SuperType
SubType.prototype = new SuperType();

//使用字面量添加新方法，会导致上一行代码无效
SubType.prototype = {
    getSubValue : function (){
    return this.subproperty;
},
someOtherMethod : function (){
    return false;
}
};
var instance = new SubType();
alert(instance.getSuperValue()); //error!
```

### 存在的问题

- **包含引用类型值的原型**，包含引用类型值的原型属性会被所有实例共享，所以要在构造函数中定义属性，而不是在原型对象中
- 在创建子类型的实例时，**不能向超类型的构造函数中传递参数**，应该说是没有办法在不影响所有对象实例的情况下，给超类型的构造函数传递参数

**！由于上面两个原因的问题，实践中很少会单独使用原型链。**

# 三、通过构造函数实现继承

在解决原型中包含引用类型值所带来问题的过程中，使用一种叫做借用构造函数**（constructor stealing）**的技术（有时候也叫做**伪造对象**或**经典继承**）。这种技术的基本思想相当简单，即在子类型构造函数的内部调用超类型构造函数，可以使用`apply()`和`call()`方法也可以在（将来）新创建的对象上执行构造函数。
示例如下：
```
function SuperType(){
    this.colors = ["red", "blue", "green"];
}
function SubType(){
    //继承了SuperType
    SuperType.call(this);
}

var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors); //"red,blue,green,black"

var instance2 = new SubType();
alert(instance2.colors); //"red,blue,green"
```
通过使用`call()`（或`apply()`），在新创建的`SubType` 实例的环境下调用了`SuperType` 构造函数。这样一来，就会在新`SubType` 对象上执行`SuperType()`函数中定义的所有对象初始化代码。结果，`SubType` 的每个实例就都会具有自己的`colors` 属性的副本了。

### 传递参数

相对于原型链而言，借用构造函数有一个很大的优势，即可以在子类型构造函数中向超类型构造函数传递参数。

```
function SuperType(name){
    this.name = name;
}
function SubType(){
    //继承了SuperType，同时还传递了参数
    SuperType.call(this, "Nicholas");
    //实例属性
    this.age = 29;
}

var instance = new SubType();
alert(instance.name); //"Nicholas";
alert(instance.age); //29
```
为了确保`SuperType` 构造函数不会重写子类型的属性，应该在调用超类型构造函数后，再添加应该在子类型中定义的属性。

### 存在的问题

- 无法避免构造函数模式存在的问题——方法都在构造函数中定义，因此函数复用就毫无意义了，在超类型的原型中定义的方法，对子类型而言也是不可见的，结果所有类型都只能使用构造函数模式，所以借用构造函数的技术也是很少单独使用的。

# 四、组合继承

**组合继承（combination inheritance）**，有时候也叫做**伪经典继承**，指的是将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。其背后的思路是使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。这样，既通过在原型上定义方法实现了函数复用，又能够保证每个实例都有它自己的属性。
例子：
```
function SuperType(name){
    this.name = name;
    this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function(){
    alert(this.name);
};
function SubType(name, age){
    //继承属性
    SuperType.call(this, name);
    this.age = age;
}
//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    alert(this.age);
};

var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
alert(instance1.colors); //"red,blue,green,black"
instance1.sayName(); //"Nicholas";
instance1.sayAge(); //29

var instance2 = new SubType("Greg", 27);
alert(instance2.colors); //"red,blue,green"
instance2.sayName(); //"Greg";
instance2.sayAge(); //27
```
在这个例子中，使用了构造函数继承了父类型的属性，原型链继承了父类型的方法，然后两个不同`SubType `实例既分别拥有自己属性——包括`colors` 属性，又可以使用相同的方法。
组合继承避免了原型链和借用构造函数的缺陷，融合了它们的优点，**成为JavaScript 中最常用的继承模式**。而且，`instanceof` 和`isPrototypeOf()`也能够用于识别基于组合继承创建的对象。

# 五、原型式继承

道格拉斯·克罗克福德在2006 年写了一篇文章，题为Prototypal Inheritance in JavaScript （JavaScript中的原型式继承）。在这篇文章中，他介绍了一种实现继承的方法，这种方法并没有使用严格意义上的构造函数。他的想法是借助原型可以基于已有的对象创建新对象，同时还不必因此创建自定义类型。为了达到这个目的，他给出了如下函数：

```
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}
```

在`object()`函数内部，先创建了一个临时性的构造函数，然后将传入的对象作为这个构造函数的原型，最后返回了这个临时类型的一个新实例。从本质上讲，`object()`对传入其中的对象执行了一次浅复制。来看下面的例子：

```
var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};

var anotherPerson = object(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");

var yetAnotherPerson = object(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");
alert(person.friends); //"Shelby,Court,Van,Rob,Barbie"
```

克罗克福德主张的这种原型式继承，要求你必须有一个对象可以作为另一个对象的基础。如果有这么一个对象的话，可以把它传递给object()函数，然后再根据具体需求对得到的对象加以修改即可。

ECMAScript 5 通过新增`Object.create()`方法规范化了原型式继承。这个方法接收两个参数：一个用作新对象原型的对象和（可选的）一个为新对象定义额外属性的对象。在传入一个参数的情况下，`Object.create()`与`object()`方法的行为相同。

```
var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};

var anotherPerson = Object.create(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");

var yetAnotherPerson = Object.create(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");
alert(person.friends); //"Shelby,Court,Van,Rob,Barbie"
```

`Object.create()`方法的第二个参数与`Object.defineProperties()`方法的第二个参数格式相同：每个属性都是通过自己的描述符定义的。以这种方式指定的任何属性都会覆盖原型对象上的同名属性。例如：
```
var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};
var anotherPerson = Object.create(person, {
        name: {
        value: "Greg"
    }
});
alert(anotherPerson.name); //"Greg"
```

# 六、寄生式继承

寄生式（parasitic）继承是与原型式继承紧密相关的一种思路，寄生式继承的思路与寄生构造函数和工厂模式类似，即创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后再像真地是它做了所有工作一样返回对象。例如：
```
function createAnother(original){
    var clone = object(original); //通过调用函数创建一个新对象
    clone.sayHi = function(){ //以某种方式来增强这个对象
        alert("hi");
    };
    return clone; //返回这个对象
}

var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};
var anotherPerson = createAnother(person);
anotherPerson.sayHi(); //"hi"
```
`createAnother()`函数接收了一个参数，也就是将要作为新对象基础的对象。然后，把这个对象`original`传递给`object()`函数，将返回的结果赋值给`clone`。再为`clone` 对象添加一个新方法`sayHi()`，最后返回`clone` 对象。在主要考虑对象而不是自定义类型和构造函数的情况下，寄生式继承也是一种有用的模式。前面示范继承模式时使用的`object()`函数不是必需的；任何能够返回新对象的函数都适用于此模式。

# 七、寄生组合式继承
前面说组合继承是JavaScript 最常用的继承模式，但是其也有不足之处，组合继承最大的问题就是其在任何情况下都会调用两次超类型构造函数，一次是在创建子类型原型的时候，另一次是在子类型构造函数内部。
```
function SuperType(name){
    this.name = name;
    this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function(){
    alert(this.name);
};
function SubType(name, age){
    SuperType.call(this, name);//第二次调用SuperType()
    this.age = age;
}

SubType.prototype = new SuperType();//第一次调用SuperType()
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    alert(this.age);
};

```
会出现两组`name` 和`colors` 属性：一组在实例上，一组在`SubType` 原型中。这就是调用两次`SuperType` 构造函数的结果，解决这个问题方法就是使用寄生组合式继承。

所谓**寄生组合式继承**，即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。其背后的基本思路是：不必为了指定子类型的原型而调用超类型的构造函数，我们所需要的无非就是超类型原型的一个副本而已。本质上，就是使用寄生式继承来继承超类型的原型，然后再将结果指定给子类型的原型。寄生组合式继承的基本模式如下所示：

```
function inheritPrototype(subType, superType){
    var prototype = object(superType.prototype); //创建对象
    prototype.constructor = subType; //增强对象
    subType.prototype = prototype; //指定对象
}
```

这个示例中的`inheritPrototype()`函数实现了寄生组合式继承的最简单形式。这个函数接收两个参数：子类型构造函数和超类型构造函数。在函数内部，第一步是创建超类型原型的一个副本。第二步是为创建的副本添加`constructor` 属性，从而弥补因重写原型而失去的默认的`constructor` 属性。最后一步，将新创建的对象（即副本）赋值给子类型的原型。这样，我们就可以用调用`inheritPrototype()`函数的语句，去替换前面例子中为子类型原型赋值的语句了，例如：
```
function SuperType(name){
    this.name = name;
    this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function(){
    alert(this.name);
};
function SubType(name, age){
    SuperType.call(this, name);
    this.age = age;
}

inheritPrototype(SubType, SuperType);
SubType.prototype.sayAge = function(){
    alert(this.age);
};
```
这个例子的高效率体现在它只调用了一次`SuperType` 构造函数，并且因此避免了在`SubType.prototype`上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用`instanceof` 和`isPrototypeOf()`。开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。

# 八、总结

`JavaScript` 主要通过原型链实现继承。原型链的构建是通过将一个类型的实例赋值给另一个构造函数的原型实现的。这样，子类型就能够访问超类型的所有属性和方法，这一点与基于类的继承很相似。原型链的问题是**对象实例共享所有继承的属性和方法，因此不适宜单独使用**。解决这个问题的技术是借用构造函数，即在子类型构造函数的内部调用超类型构造函数。这样就可以做到每个实例都具有自己的属性，同时还能保证只使用构造函数模式来定义类型。使用最多的继承模式是组合继承，这种模式使用原型链继承共享的属性和方法，而通过借用构造函数继承实例属性。

此外，还存在下列可供选择的继承模式：

- 原型式继承，可以在不必预先定义构造函数的情况下实现继承，其本质是执行对给定对象的浅复制。而复制得到的副本还可以得到进一步改造。
- 寄生式继承，与原型式继承非常相似，也是基于某个对象或某些信息创建一个对象，然后增强对象，最后返回对象。为了解决组合继承模式由于多次调用超类型构造函数而导致的低效率问题，可以将这个模式与组合继承一起使用。
- 寄生组合式继承，集寄生式继承和组合继承的优点与一身，是实现基于类型继承的最有效方式。