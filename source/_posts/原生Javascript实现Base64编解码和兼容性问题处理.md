---

title: 原生Javascript实现Base64编解码和兼容性问题处理
date: 2019-12-21 12:21
tags: Javascript Base64 window.btoa window.atob
categories: Javascript

---


# 一、前言

前几天因工作需要，需要在`URL`上面传递一段参数，参数内容有点多，所以需要在前端实现`Base64`编码参数，然后进行传递，在下一页面再进行解码获取参数。之前也没有相关经验，之前参数多的时候都是放在`Parameters`里面的，所以一开始也不知道如何下手，网上一番捣鼓之后，找到了如下两个函数可以实现相关功能：
```
window.btoa() // 实现base64编码
window.atob() // 实现base64解码
```
下面来记录下这两个函数相关的用法及其中的坑。
# 二、具体用法与坑

### window.btoa()

```
window.btoa("test encode")
// "dGVzdCBlbmNvZGU="
```

### window.atob()
```
window.atob("dGVzdCBlbmNvZGU=")
// "test encode"
```
<!--more-->

以上结果简单明了，就是使用`window.btoa()`函数先进行编码，然后使用`window.atob()`进行解码，貌似一切正常，万事大吉，完事了。
但是，真要这么想就完蛋了，`too young too naive`，坑还在后面呢，来看下面的例子：

```
window.btoa("测试编码")
/* VM898:1 Uncaught DOMException: Failed to execute 'btoa' on 'Window': 
The string to be encoded contains characters outside of the Latin1 range. */
```

```
window.atob("测试编码")
/* Uncaught DOMException: Failed to execute 'atob' on 'Window': 
The string to be decoded contains characters outside of the Latin1 range.*/
```

二者当中只要出现了中文，就会抛错，因为其不支持中文进行`base64`编码，小白的我一脸懵逼，但是，这怎么能难倒我呢，接着又是一顿猛搜，最终找到了解决方案，方法也很简单，就是在编码处理中文之前使用`encodeURIComponent`函数先套一层，然后在解码的时候外面套一层`decodeURIComponent`即可：

```
window.btoa(encodeURIComponent("测试编码"))
// "JUU2JUI1JThCJUU4JUFGJTk1JUU3JUJDJTk2JUU3JUEwJTgx"
```
window.btoa("测试编码")

```
decodeURIComponent(window.atob("JUU2JUI1JThCJUU4JUFGJTk1JUU3JUJDJTk2JUU3JUEwJTgx"))
// "测试编码"
```
# 三、延伸，任意文件转Base64

使用`FileReader`对象和`readAsDataURL`方法，可以把任意的文件（SVG、小尺寸图片、Woff字体等等）转换为`Base64 Data-URI`，转换的代码如下：

```
var reader = new FileReader();
reader.onload = function(e) {
  console.log(e.target.result); // e.target.result就是该文件的完整Base64 Data-URI
};
reader.readAsDataURL(file);

```

# 四、参考

[https://www.zhangxinxu.com/wordpress/2018/08/js-base64-atob-btoa-encode-decode/?from=groupmessage](https://www.zhangxinxu.com/wordpress/2018/08/js-base64-atob-btoa-encode-decode/?from=groupmessage)

