---

title: 深入理解CSS布局之Flexbox
date: 2019-11-26 21:18
tags: CSS Flexbox
categories: CSS
---

# 一、 前言

`Flexbox `布局提供了一种高效的在容器中布局、对齐、分配空间的方式，即使其`item `的大小是未知的或者动态的。

# 二、属性

### flex container相关
- display
- flex-direction
- flex-wrap
- flex-flow
- justify-content
- align-items
- align-content

### flex items相关
- order
- flex-grow
- flex-shrink
- flex-basis
- flex
- align-self

<!--more-->

# 三、具体语法

### display

```
.container {
  display: flex; /* 或者inline-flex */
}
```
定义一个`flex container `，行内或者块级取决于所给的值，`flex`表示容器内元素以块级元素展示，`inline-flex`表示容器内元素行内元素展示。

### flex-direction

```
.container {
  flex-direction: row | row-reverse | column | column-reverse;
}
```
这个属性定义了一个主轴，决定了`flex items `在`flex container `中如何排列，从左到右还是从上到下。
- `row ` 默认，在`ltr`中从左到右，`rtl`中从右到左
- `row-reverse ` 与上面正好相反，在`ltr`中从右到左，`rtl`中从左到右
- `column ` 与`row ` 类似，从上到下
- `column-reverse  ` 与`row-reverse ` 类似，从下到上

### flex-wrap

```
.container{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```
默认情况下，`flex items`会尽可能占满一行，可以用这个属性来控制换行。
- `nowrap ` 默认值，所有`items `都会在同一行
- `wrap ` 从上到下换到多行显示
- `wrap-reverse` 从下到上换到多行显示
[一个`flex-wrap `的例子](https://css-tricks.com/almanac/properties/f/flex-wrap/)

### flex-flow（用于`flex container `元素）

```
flex-flow: <‘flex-direction’> || <‘flex-wrap’>
```
`flex-direction` 和 `flex-wrap` 属性的缩写，共同定义了`flex container `横轴和纵轴，默认值是`row nowrap `。

### justify-content，内容水平对齐

```
.container {
  justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly | start | end | left | right ... + safe | unsafe;
}
```
- `flex-start ` 朝着`flex-direction `开始的方向对齐
- `flex-end ` 朝着`flex-direction `结束的方向对齐
- `center  ` 居中对齐
- `space-between ` 最外侧两个`item `与外边距没有空间，其余任意两个之间等距
- `space-around ` 每一个`item `都有左右间距，`item `与`item `之间是两倍于`item `与外边距之间的距离
- `space-evenly ` 每一个`item `之间都左右等距，包括与外边距
- `start ` 朝着`writing-mode `开始的方向对齐
- `end ` 朝着`writing-mode `结束的方向对齐
- `left ` 除非与`flex-direction `有冲突，不然的话与`start `一样
- `right ` 除非与`flex-direction `有冲突，不然的话与`end `一样

![水平对齐](https://upload-images.jianshu.io/upload_images/1741029-0b1eaa9a32436a3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### align-items，item垂直对齐

```
.container {
  align-items: stretch | flex-start | flex-end | center | baseline | first baseline | last baseline | start | end | self-start | self-end + ... safe | unsafe;
}
```

- `stretch  ` 默认值，占满整个容器
- `flex-start / start  /  self-start` 顶部对齐，差别很小，与`flex-direction`  和 `writing-mode`有一定联系
- `flex-end / end /  self-end ` 底部对齐，差别很小，与`flex-direction`  和 `writing-mode`有一定联系
- `center  ` 垂直居中
- `baseline ` 基准线对齐
- `first baseline ` 第一条基准线对齐
- `last baseline ` 最后一条基准线对齐

![垂直对齐](https://upload-images.jianshu.io/upload_images/1741029-200c1344348b699a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### align-content，整体内容对齐

```
.container {
  align-content: flex-start | flex-end | center | space-between | space-around | space-evenly | stretch | start | end | baseline | first baseline | last baseline + ... safe | unsafe;
}
```

- `flex-start / start `  `items `从`container `的左上方开始陈列，`flex-start `对应`flex-direction `，`start `对应`writing-mode ` 
- `flex-end / end ` `items `从`container `的左下方开始陈列，`flex-end `对应`flex-direction `，`end `对应`writing-mode ` 
- `center ` 垂直居中
- `space-between ` 头部和底部没有空间，其余等距均分
- `space-around ` 头部和底部有一倍间距，其余`items `  之间为二倍间距
- `space-evenly ` 所有`items `  等距均分
- `stretch ` 占满垂直空间距离

![整体内容对齐](https://upload-images.jianshu.io/upload_images/1741029-9d564590c1de115e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## flex items相关

### order

```
.item {
  order: <integer>; /* 默认是 0 */
}
```

`flex items `默认是按照原始顺序排列，`order `属性可以控制`flex items `在`flex container `的排列顺序。

### flex-grow

```
.item {
  flex-grow: <number>; /* 默认是 0，负数无效 */
}
```

如果`flex-grow: 1;` 其所有`flex items `等分剩余空间，如果其中有一个的值为2，其占两倍于值为1的空间，以此类推。

![flex-grow值为1和2对比](https://upload-images.jianshu.io/upload_images/1741029-b614cd266f0481d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### flex-shrink

```
.item {
  flex-shrink: <number>; /* 默认是 1，负数无效 */
}
```

可以使`flex item `缩放。

### flex-basis

```
.item {
  flex-basis: <length> | auto; /* 默认是 auto */
}
```

这个属性在剩余空间被分配之前定义一个元素的默认尺寸，其值可以是长度（`20% `或`5rem `）或者关键词，`auto `意味着查看`width `或`height `属性，可以暂时使用`main-size `关键字来实现在其被弃用之前。`content `关键字意味着根据其内容的尺寸来给定默认值，但是这个属性没有被很好支持，所以很难测试并且更难弄清楚他的类似属性`max-content `、`min-content `和`fit-content `是如何作用的。
如果设置为`0 `，内容周围的额外空间将不会被计入。如果设置为`auto `，额外空间将基于`flex-grow `属性来分发。

### flex

```
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

`flex-grow`、`flex-shrink `和 `flex-basis `的缩写，第二三个值`flex-shrink `和 `flex-basis `是可选的，默认值是`0 1 auto `，因为其会自动设置其他属性值，所以推荐使用此属性而不是分别设置上面的属性值。

### align-self

```
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

这个属性可以覆盖父级元素设置的属性，比如`align-items `设置过的属性。

![align-self覆盖flex-start属性](https://upload-images.jianshu.io/upload_images/1741029-2b57ec9c25ed3ddb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####  ！注意：`float` 、`clear` 和 `vertical-align `属性对`flex item `没有影响。

#四、一些例子

### 简单居中
```
.parent {
  display: flex;
  height: 200px; 
}

.child {
  width: 200px;  
  height: 200px; 
  margin: auto;  
}
```

### 自适应，不使用`@media`
```
.flex-container {
  display: flex;
  
  flex-flow: row wrap;
  /* 上面缩写相当于
   * flex-direction: row;
   * flex-wrap: wrap;
   * 定义了主轴是从左到右的，而且允许换行
   */

  
  /* 定义如何分配剩余空间，即每个item周围都有空间*/
  justify-content: space-around;
}
```
[在线实例](https://codepen.io/team/css-tricks/pen/EKEYob?__cf_chl_captcha_tk__=5c642ec60a6cc239608b6bfe3da1a4d0e9745280-1575119677-0-AQQfEvvWj6h7QzJQah8yDu2l8Urr_CS3lTPa4wKUH7prJ8B1m2Z5d-rJqo3D4oXAGVUr2wUQ_MXemw1FMtDy-SwVMDo3mjFc18JXFWwl2-3QiL7o5w8Zcfcj0PAOzUQ7hoBcM2Y79aJPjYzX7OtREk74DxU2Alyop0EB8uxwYVXZUGrNBsru7E8h68RTGDoBjj26ejwbuSTBezEKPWKtA5M490MrYjD5kvh8juq0f-9Tt1GnhHGAqKhRqvpbx0EFFSfB0mHN8xiJL3k36p8VY24XVnpGk4FeJ2YTgfOfyu3ItgE2Us-suN8i0N1rOScAIypzJk5-JnAYMIiQpODFwxc)

### 一个导航栏，在小屏幕上自动换行显示
```
/* 大屏幕正常显示 */
.navigation {
  display: flex;
  flex-flow: row wrap;
  /* 在主轴上从后往前对齐，即从右到左 */
  justify-content: flex-end;
}

/* 中等屏幕 */
@media all and (max-width: 800px) {
  .navigation {
    /* 相当于居中对齐，左右两边两个与外侧间距少一半 */
    justify-content: space-around;
  }
}

/* 小屏幕 */
@media all and (max-width: 500px) {
  .navigation {
    /* 纵向对齐，即换行显示 */
    flex-direction: column;
  }
}
```
[在线实例](https://codepen.io/team/css-tricks/pen/YqaKYR)

### 一个有头部底部的三列布局

```
.wrapper {
  display: flex;
  flex-flow: row wrap; // 横向排列，允许换行
}

/* 通过 flex-basis，设置所有item的宽度为百分之百 */
.wrapper > * {
  flex: 1 100%;
}

/* 在这个例子中采用移动端优先的源顺序的方式
 * 1. header
 * 2. article
 * 3. aside 1
 * 4. aside 2
 * 5. footer
 */

/* 中等屏幕 */
@media all and (min-width: 600px) {
  /* 设置 sidebars 在同一行显示 */
  .aside { flex: 1 auto; }
}

/* 大屏幕 */
@media all and (min-width: 800px) {
  /* 调换第一个sidebar 和 main 的顺序
   * 设置main 的宽度为其余两个sidebar 总宽度的两倍
   */
  .main { flex: 2 0px; }
  .aside-1 { order: 1; }
  .main    { order: 2; }
  .aside-2 { order: 3; }
  .footer  { order: 4; }
}
```
[在线实例](https://codepen.io/halshaw/pen/qBEWpBw)

# 五、前缀
Flexbox 可能会需要一些前缀以用来支持不同的浏览器，下面是一些参考。

```
@mixin flexbox() {
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
}

@mixin flex($values) {
  -webkit-box-flex: $values;
  -moz-box-flex:  $values;
  -webkit-flex:  $values;
  -ms-flex:  $values;
  flex:  $values;
}

@mixin order($val) {
  -webkit-box-ordinal-group: $val;  
  -moz-box-ordinal-group: $val;     
  -ms-flex-order: $val;     
  -webkit-order: $val;  
  order: $val;
}

.wrapper {
  @include flexbox();
}

.item {
  @include flex(1 200px);
  @include order(2);
}
```

# 六、浏览器支持

![Flex目前支持的浏览器](https://upload-images.jianshu.io/upload_images/1741029-fb80488607713961.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 七、参考（翻译）

[https://css-tricks.com/snippets/css/a-guide-to-flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox)
