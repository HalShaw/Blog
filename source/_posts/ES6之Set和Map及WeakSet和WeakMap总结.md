---

title: ES6之Set和Map及WeakSet和WeakMap总结
date: 2019-08-19 20:44
tags: ES6 Set Map WeakSet WeakMap
categories: JavaScript ES6
---
![Set & Map](https://upload-images.jianshu.io/upload_images/1741029-96533cae7400a3a1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 一、Set
- 概念
`Set`类似于数组，本身是一个构造函数，用来生成 `Set` 数据结构，成员值都是唯一的，常用于数组去重。

- 属性
`Set.prototype.size`：返回Set实例的成员总数

- 方法
`add()` 添加值，返回 Set 结构本身。
`delete()` 删除值，返回一个布尔值，表示删除是否成功。
`has()` 返回一个布尔值，表示该值是否为`Set`的成员。
`clear()` 清除所有成员，没有返回值。

- 遍历
`Set.prototype.keys()`：返回键名的遍历器
`Set.prototype.values()`：返回键值的遍历器
`Set.prototype.entries()`：返回键值对的遍历器
`Set.prototype.forEach()`：使用回调函数遍历每个成员

<!--more-->

# 二、Map

- 概念
由于`Object`只能使用字符串作为`key`，在使用上面会有很大的限制，比如使用`DOM`节点作为`key`时，就会被自动转为字符串`[object HTMLDivElement]`。
为了解决上述问题，ES6 引入了 `Map` ，它类似于对象，也是键值对的集合，但是其key的范围不限于字符串，各种类型的值（包括对象）都可以当作键。
`Object` 结构提供了“字符串—值”的对应，`Map` 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。

- 与Object的区别
`Object`：
`k-v`(键-值对)，`key`只能是字符串，`value`可以为任意类型，无序，不可遍历，有默认属性
`Map`: 
`k-v`(键-值对)，`key`与`value`可以为任意类型，可使用`for...of`遍历，按插入顺序遍历，可存储大量数据，但是由于`.get` 函数，可能比`Object`慢

常用方法比较：
	
	    Object:
	       var o = {};
	       var o = Object.create(null);
	       o.key = 1;
	       o.key += 10;
	       for(let k in o) o[k]++;
	       var sum = 0;
	       for(let v of Object.values(m)) sum += v;
	       if('key' in o);
	       if(o.hasOwnProperty('key'));
	       delete(o.key);
	       Object.keys(o).length
	    Map:
	       var m = new Map();
	       m.set('key', 1);
	       m.set('key', m.get('key') + 10);
	       m.foreach((k, v) => m.set(k, m.get(k) + 1));
	       for(let k of m.keys()) m.set(k, m.get(k) + 1);
	       var sum = 0;
	       for(let v of m.values()) sum += v;
	       if(m.has('key'));
	       m.delete('key');
	       m.size();
	

- 属性与方法
`size`
`set(k,v)`
`get(K)`
`has(k)`
`delete(k)`
`clear()`

- 遍历
`Map.prototype.keys()`：返回键名的遍历器。
`Map.prototype.values()`：返回键值的遍历器。
`Map.prototype.entries()`：返回所有成员的遍历器。
`Map.prototype.forEach()`：遍历 Map 的所有成员。

- 遍历顺序就是插入顺序
- `Map` 转为数组最方便的方法，就是使用扩展运算符`（...）`

# 三、WeakSet
- `WeakSet` 的成员只能是对象，而不能是其他类型的值
- 对象都是弱引用，即垃圾回收机制不考虑 `WeakSet` 对该对象的引用。如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 `WeakSet` 之中。
- `WeakSet` 适合临时存放一组对象，以及存放跟对象绑定的信息。只要这些对象在外部消失，它在 `WeakSet` 里面的引用就会自动消失，所以`WeakSet` 的成员是不适合引用的。
- 由于 `WeakSet` 内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的，因此 ES6 规定 `WeakSet` 不可遍历。

- 方法
`WeakSet.prototype.add(value)`：向 WeakSet 实例添加一个新成员。
`WeakSet.prototype.delete(value)`：清除 WeakSet 实例的指定成员。
`WeakSet.prototype.has(value)`：返回一个布尔值，表示某个值是否在 `WeakSet` 实例之中。

# 四、WeakMap

- `WeakMap`只接受对象作为键名（null除外）
- `WeakMap`的键名所指向的对象，不计入垃圾回收机制
- `WeakMap`的键名所引用的对象都是弱引用，即垃圾回收机制不将该引用考虑在内
- 没有遍历方法，即没有`keys()`、`values()`和`entries()`方法
- 没有`size`属性、`clear()`方法
- `WeakMap`只有四个方法可用：`get()`、`set()`、`has()`、`delete()`

一个典型应用场景是，在网页的 `DOM` 元素上添加数据，就可以使用`WeakMap`结构。当该 `DOM` 元素被清除，其所对应的`WeakMap`记录就会自动被移除。

# 参考
[http://es6.ruanyifeng.com/#docs/set-map](http://es6.ruanyifeng.com/#docs/set-map)
[https://stackoverflow.com/questions/18541940/map-vs-object-in-javascript](https://stackoverflow.com/questions/18541940/map-vs-object-in-javascript)

