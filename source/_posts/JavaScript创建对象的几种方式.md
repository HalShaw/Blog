---

title: JavaScript创建对象的几种方式
date: 2020-01-13 00:02
tags: JavaScript 创建对象
categories: JavaScript 

---

# 一、前言  

虽然`Object` 构造函数或对象字面量都可以用来创建单个对象，但这些方式有个明显的缺点：使用同一个接口创建很多对象，会产生大量的重复代码。所以为解决这个问题，开始使用工厂模式的一种变体来创建对象。

# 二、工厂模式

用函数来封装以特定接口创建对象的细节，例如：

```
function createPerson(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
        alert(this.name);
    };
    return o;
}
var person1 = createPerson("Hal", 24, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```

工厂模式虽然解决了创建多个相似对象的问题，但却没有解决对象识别的问题（即怎样知道一个对象的类型）。


<!--more-->


# 三、构造函数模式

`ECMAScript` 中的构造函数可用来创建特定类型的对象，像`Object` 和`Array` 这样的原生构造函数，在运行时会自动出现在执行环境中。此外，也可以创建自定义的构造函数，从而定义自定义对象类型的属性和方法。上面的例子改写成构造函数模式为：

```
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
        alert(this.name);
    };
}

var person1 = new Person("Hal", 24, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```
`Person()`函数中的代码除了与`createPerson()`中相同的部分外，还存在以下不同之处：

- 没有显式地创建对象
- 直接将属性和方法赋给了`this` 对象
- 没有`return` 语句

`Person`函数的首字母是大写，按照惯例，构造函数始终都应该以一个大写字母开头，而非构造函数则应该以一个小写字母开头。要创建`Person` 的新实例，必须使用`new` 操作符，其过程如下：

- (1) 创建一个新对象
- (2) 将构造函数的作用域赋给新对象（因此this 就指向了这个新对象）
- (3) 执行构造函数中的代码（为这个新对象添加属性）
- (4) 返回新对象

上面的`person1` 和`person2` 分别保存着`Person` 的一个不同的实例。这两个对象都有一个`constructor`（构造函数）属性，该属性指向`Person`：

```
alert(person1.constructor == Person); //true
alert(person2.constructor == Person); //true
```
对象的`constructor` 属性最初是用来标识对象类型的。但是，提到检测对象类型，还是`instanceof`操作符要更可靠一些。这个例子中创建的所有对象既是`Object` 的实例，同时也是`Person`的实例，这一点通过`instanceof` 操作符可以得到验证。

```
alert(person1 instanceof Object); //true
alert(person1 instanceof Person); //true
alert(person2 instanceof Object); //true
alert(person2 instanceof Person); //true
```

### 将构造函数当作函数

构造函数与其他函数的唯一区别，就在于调用它们的方式不同。任何函数，只要通过`new` 操作符来调用，那它就可以作为构造函数；而任何函数，如果不通过`new` 操作符来调用，那它跟普通函数也不会有什么两样。
```
// 当作构造函数使用
var person = new Person("Hal", 24, "Software Engineer");
person.sayName(); //"Hal"

// 作为普通函数调用
Person("Greg", 27, "Doctor"); // 添加到window
window.sayName(); //"Greg"

// 在另一个对象的作用域中调用
var o = new Object();
Person.call(o, "Kristen", 25, "Nurse");
o.sayName(); //"Kristen"
```

### 构造函数的问题

使用构造函数的主要问题，就是每个方法都要在每个实例上重新创建一遍。即上面的构造函数等价于：
```
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = new Function("alert(this.name)"); // 与声明函数在逻辑上是等价的
}
```

```
alert(person1.sayName == person2.sayName); //false
```
上面代码可以说明两个实例上的同名函数是不相等的。

但是，创建两个一模一样的函数实例是没有必要的，况且还有`this`对象，根本不用 在执行代码前就把函数绑定到特定对象上，所以，可以像下面一样，通过把函数转移到构造函数外部来解决这个问题。
```
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = sayName;
}

function sayName(){
    alert(this.name);
}
var person1 = new Person("Hal", 24, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");

alert(person1.sayName == person2.sayName); //true
```
上面虽然解决了两个函数做同一件事的问题，但是新问题有出现了，在全局定义域中定义的函数实际上只能被某个对象调用，这让全局作用域有点名不副实，而且更糟的是，如果对象需要定义很多方法，就需要定义很多个全局函数，然后上面的自定义构造函数就没有任何封装性可言了。好在这些问题可以通过原型模式来解决。

# 四、原型模式

我们创建的每一个函数都有一个`prototype`原型属性，这个属性是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。`prototype` 就是通过调用构造函数而创建的那个对象实例的原型对象，使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法，也就是说，可以不用在构造函数中定义对象实例的信息，而是将这些信息直接添加到原型对象上面，如下所示：

```
function Person(){
}
Person.prototype.name = "Hal";
Person.prototype.age = 24;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};
var person1 = new Person();
person1.sayName(); //"Hal"
var person2 = new Person();

person2.sayName(); //"Hal"
alert(person1.sayName == person2.sayName); //true
```
新对象的这些属性和方法是由所有实例共享的，也就是说`person1` 和`person2` 访问的都是同一组属性和同一个`sayName()`函数。

### 原型对象的问题

它省略了为构造函数传递初始化参数这一环节，结果所有实例在默认情况下都将取得相同的属性值，但还不是原型的最大问题，其最大的问题还是由其共享的本性所导致的。对于那些包含基本值的属性倒也说得过去，通过在实例上添加一个同名属性可以隐藏原型中的对应属性。但是对于包含了引用类型值的属性来说，问题就比较明显了，看下面的例子：

```
function Person(){
}

Person.prototype = {
    constructor: Person,
    name : "Hal",
    age : 24,
    job : "Software Engineer",
    friends : ["Shelby", "Court"],
    sayName : function () {
        alert(this.name);
    }
};

var person1 = new Person();
var person2 = new Person();
person1.friends.push("Van");
alert(person1.friends); //"Shelby,Court,Van"
alert(person2.friends); //"Shelby,Court,Van"
alert(person1.friends === person2.friends); //true

```
上面代码中，我们本意是给`person1`的`friends`属性添加一个`Van`，而并不想给`person2`也添加，但是由于共享属性的缘故，`person2`的属性也跟着变了。这也就是很少有人单独使用原型模式的原因所在。

# 五、组合使用构造函数模式和原型模式

创建自定义类型的最常见方式，就是组合使用构造函数模式与原型模式。构造函数模式用于定义实例属性，而原型模式用于定义方法和共享的属性。结果，每个实例都会有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度地节省了内存。另外，这种混成模式还支持向构造函数传递参数；可谓是集两种模式之长。

```
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.friends = ["Shelby", "Court"];
}
Person.prototype = {
    constructor : Person,
    sayName : function(){
        alert(this.name);
    }
}

var person1 = new Person("Hal", 24, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");

person1.friends.push("Van");
alert(person1.friends); //"Shelby,Count,Van"
alert(person2.friends); //"Shelby,Count"
alert(person1.friends === person2.friends); //false
alert(person1.sayName === person2.sayName); //true
```
上面的代码中，实例属性都是在构造函数中定义的，而由所有实例共享的属性`constructor` 和方法`sayName()`则是在原型中定义的。而修改了`person1.friends`，并不会影响到`person2.friends`，因为它们分别引用了不同的数组。

**！这种构造函数与原型混成的模式，是目前在ECMAScript 中使用最广泛、认同度最高的一种创建自定义类型的方法。可以说，这是用来定义引用类型的一种默认模式。**

# 六、动态原型模式

看到前面独立的构造函数和原型时，有其他OO语言开发经验的人很可能会感到非常困惑。**动态原型模式**正是致力于解决这个问题的一个方案，它把所有信息都封装在了构造函数中，而通过在构造函数中初始化原型（仅在必要的情况下），又保持了同时使用构造函数和原型的优点。换句话说，**可以通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型。**

```
function Person(name, age, job){
    //属性
    this.name = name;
    this.age = age;
    this.job = job;
    //方法
    if (typeof this.sayName != "function"){
        Person.prototype.sayName = function(){
            alert(this.name);
        };
    }
}   
```
这里只在`sayName()`方法不存在的情况下，才会将它添加到原型中。这段代码只会在初次调用构造函数时才会执行。此后，原型已经完成初始化，不需要再做什么修改了。对于采用这种模式创建的对象，还可以使用`instanceof` 操作符确定它的类型。

**！使用动态原型模式时，不能使用对象字面量重写原型。前面已经解释过了，如果在已经创建了实例的情况下重写原型，那么就会切断现有实例与新原型之间的联系。**

# 七、寄生构造函数模式

在前述的几种模式都不适用的情况下，可以使用寄生（parasitic）构造函数模式。这种模式的基本思想是创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后再返回新创建的对象。但从表面上看，这个函数又很像是典型的构造函数。下面是一个例子：

```
function Person(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
        alert(this.name);
    };
    return o;
}

var friend = new Person("Hal", 24, "Software Engineer");
friend.sayName(); //"Hal"
```
上面的代码中，`Person` 函数创建了一个新对象，并以相应的属性和方法初始化该对象，然后又返回了这个对象。除了使用`new` 操作符并把使用的包装函数叫做构造函数之外，这个模式跟工厂模式其实是一模一样的。构造函数在不返回值的情况下，默认会返回新对象实例。而通过在构造函数的末尾添加一个`return` 语句，可以重写调用构造函数时返回的值。

这个模式可以在特殊的情况下用来为对象创建构造函数。假设我们想创建一个具有额外方法的特殊数组。由于不能直接修改`Array` 构造函数，因此可以使用这个模式。
```
function SpecialArray(){
    //创建数组
    var values = new Array();
    //添加值
    values.push.apply(values, arguments);
    //添加方法
    values.toPipedString = function(){
        return this.join("|");
    };
    //返回数组
    return values;
}

var colors = new SpecialArray("red", "blue", "green");
alert(colors.toPipedString()); //"red|blue|green"
```
在这个例子中，我们创建了一个名叫`SpecialArray` 的构造函数。在这个函数内部，首先创建了一个数组，然后使用`push()`方法（用构造函数接收到的所有参数）初始化了数组的值。随后，又给数组实例添加了一个`toPipedString()`方法，该方法返回以竖线分割的数组值。最后，将数组以函数值的形式返回。接着，我们调用了`SpecialArray` 构造函数，向其中传入了用于初始化数组的值，此后又调用了`toPipedString()`方法。

**！关于寄生构造函数模式，首先，返回的对象与构造函数或者与构造函数的原型属性之间没有关系；也就是说，构造函数返回的对象与在构造函数外部创建的对象没有什么不同。为此，不能依赖`instanceof` 操作符来确定对象类型。由于存在上述问题，我们建议在可以使用其他模式的情况下，不要使用这种模式。**

# 八、稳妥构造函数模式

所谓**稳妥对象（durable objects）**，指的是没有公共属性，而且其方法也不引用`this` 的对象。稳妥对象最适合在一些安全的环境中（这些环境中会禁止使用`this` 和`new`），或者在防止数据被其他应用程序（如`Mashup`程序）改动时使用。
稳妥构造函数遵循与寄生构造函数类似的模式，但有两点不同：一是新创建对象的实例方法不引用`this`；二是不使用`new` 操作符调用构造函数。
按照稳妥构造函数的要求，可以将前面的  Person   构造函数重写如下：

```
function Person(name, age, job){
    //创建要返回的对象
    var o = new Object();

    //可以在这里定义私有变量和函数
    //添加方法
    o.sayName = function(){
        alert(name);
    };
    //返回对象
    return o;
}

var friend = Person("Hal", 24, "Software Engineer");
friend.sayName(); //"Hal"
```
在以这种模式创建的对象中，除了使用`sayName()`方法之外，没有其他办法访问`name` 的值。变量`friend` 中保存的是一个稳妥对象，而除了调用`sayName()`方法外，没有别的方式可以访问其数据成员。即使有其他代码会给这个对象添加方法或数据成员，但也不可能有别的办法访问传入到构造函数中的原始数据。

**！与寄生构造函数模式类似，使用稳妥构造函数模式创建的对象与构造函数之间也没有什么关系，因此instanceof 操作符对这种对象也没有意义。**

# 九、总结

`ECMAScript`支持面向对象（OO）编程，但不使用类或者接口。对象可以在代码执行过程中创建和增强，因此具有动态性而非严格定义的实体。在没有类的情况下，可以采用下列模式创建对象：
- 工厂模式，使用简单的函数创建对象，为对象添加属性和方法，然后返回对象。这个模式后来被构造函数模式所取代。

-  构造函数模式，可以创建自定义引用类型，可以像创建内置对象实例一样使用new 操作符。不过，构造函数模式也有缺点，即它的每个成员都无法得到复用，包括函数。由于函数可以不局限于任何对象（即与对象具有松散耦合的特点），因此没有理由不在多个对象间共享函数。

- 原型模式，使用构造函数的prototype 属性来指定那些应该共享的属性和方法。组合使用构造函数模式和原型模式时，使用构造函数定义实例属性，而使用原型定义共享的属性和方法。