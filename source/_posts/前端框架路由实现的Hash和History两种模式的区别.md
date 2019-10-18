---

title: 前端框架路由实现的Hash和History两种模式的区别
date: 2019-10-18 22:32
tags: JavaScript 前端 路由 Angular Vue

---
![Hash vs History](https://upload-images.jianshu.io/upload_images/1741029-9e5d1063264044c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 前言
- 之前面试的时候就有准备过前端框架中两种路由实现方式及区别，但是当时没专门下功夫去深入了解，就在网上搜了下别人总结的临时抱了下佛脚。但是事实证明，出来混，总是要还的，在后来的面试中又遇到了，而且这次问得更加深入，仅仅靠死记硬背来的知识总是遗忘得很快，网上别人总结的东西终归还是别人的，鉴于网上别人总结的内容有点杂乱无章，还是决定自己在此总结记录一番，以加深印象。

# 一、何为前端路由
- 路由的概念来自于服务器端，在SPA（单页应用）中，路由描述的是URL到函数的映射关系，即在浏览器中输入一个URL，相应的控制器会对提交的请求进行解析，然后进行路由匹配，找到对应的模块和函数进行执行。

# 二、如何实现
- 实现的两个核心问题是如何检测路由变化和如何改变URL而不刷新页面，通常有两种实现模式，一种是Hash模式，一种是History模式。

# 三、Hash模式
- 早期的前端路由的实现就是基于`location.hash`来实现的，`location.hash`的值就是URL中#后面的内容
其实现原理就是监听#后面的内容来发起Ajax请求来进行局部更新，而不需要刷新整个页面。
- 使用`hashchange`事件来监听 URL 的变化，以下这几种情况改变 URL 都会触发 `hashchange` 事件：浏览器前进后退改变 URL、`<a>`标签改变 URL、window.location改变URL。

### 优缺点
- 兼容低版本浏览器，Angular1.x和Vue默认使用的就是hash路由
- 只有#符号之前的内容才会包含在请求中被发送到后端，也就是说就算后端没有对路由全覆盖，但是不会返回404错误
- hash值的改变，都会在浏览器的访问历史中增加一个记录，所以可以通过浏览器的回退、前进按钮控制hash的切换
- 会覆盖锚点定位元素的功能
- 不太美观，#后面传输的数据复杂的话会出现问题


# 四、History模式
- history 提供了 `pushState` 和 `replaceState` 两个方法来记录路由状态，这两个方法改变 URL 不会引起页面刷新
- history 提供类似 hashchange 事件的 popstate 事件，但 popstate 事件有些不同：通过浏览器前进后退改变 URL 时会触发 popstate 事件，通过pushState/replaceState或`<a>`标签改变 URL 不会触发 popstate 事件。好在我们可以拦截 pushState/replaceState的调用和`<a>`标签的点击事件来检测 URL 变化，所以监听 URL 变化可以实现，只是没有 hashchange 那么方便。
- `pushState(state, title, url)` 和 `replaceState(state, title, url)`都可以接受三个相同的参数：
- state：需要保存的数据，这个数据在触发popstate事件时，可以在event.state里获取
 - title：标题，基本没用，一般传 null
- url：设定新的历史记录的 url，新的 url 与当前 url 的 origin 必须是一样的，否则会抛错，url可以是绝对路径，也可以是相对路径。

### 优缺点
- 使用简单，比较美观
- `pushState()`设置新的URL可以是任意与当前URL同源的URL，而hash只能改变#后面的内容，因此只能设置与当前URL同文档的URL
- `pushState()`设置的URL与当前URL一模一样时也会被添加到历史记录栈中，而hash#后面的内容必须被修改才会被添加到新的记录栈中
- `pushState()`可以通过`stateObject`参数添加任意类型的数据到记录中，而hash只能添加短字符串
- `pushState()`可额外设置title属性供后续使用
- 前端的URL必须和向发送请求后端URL保持一致，否则会报404错误
- 由于History API的缘故，低版本浏览器有兼容行问题

# 参考
- [https://www.jianshu.com/p/d2aa8fb951e4](https://www.jianshu.com/p/d2aa8fb951e4)
- [https://blog.csdn.net/Benjamin920813/article/details/82764664](https://blog.csdn.net/Benjamin920813/article/details/82764664)
- [https://www.cnblogs.com/lguow/p/10921564.html](https://www.cnblogs.com/lguow/p/10921564.html)
- [https://www.cnblogs.com/funny-code123/p/9599793.html](https://www.cnblogs.com/funny-code123/p/9599793.html)

