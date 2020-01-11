---

title: 深入理解CSS布局之Grid
date: 2019-11-17 22:15
tags: CSS Grid
categories: CSS
---

# 一、 前言
一直听说`CSS`的`Grid`布局是一项非常强大的布局技术，不像`Flex`布局只是一维的系统，`Grid`布局是个二维的系统，也就是说其可以同时处理行和列。`Grid`布局分为两个部分，`Grid`容器（Container）和`Grid`项目（Items）。

# 二、 一些重要概念及术语

### Grid Container
应用了`display: grid`的元素，它是所有`grid items`的直接父元素，例如：
```
<div class="container">
  <div class="item item-1"></div>
  <div class="item item-2"></div>
  <div class="item item-3"></div>
</div>
```
### Grid Item
`Grid Container`的直接后代元素，所有`div.item`都是，但是`p.sun-item`不是。
```
<div class="container">
  <div class="item"></div> 
  <div class="item">
  	<p class="sub-item"></p>
  </div>
  <div class="item"></div>
</div>
```

<!--more-->

### Grid Line
组成`Grid `结构的分割线，可垂直（`column grid lines`）可水平（`row grid lines`）。

### Grid Track
两个相邻`Grid`行之间的距离，其实就是一整行或整列的距离，如第二行与第三行之间的`Grid Track`就只第二行一整行。

### Grid Cell
两个相邻行与相邻列之间的距离，就相当于田字格或九宫格当中的一小格。

### Grid Area
四`Grid`行包围的空间，一个`Grid Area`可能包含许多`grid cells`。

# 三、Grid 属性
### Grid Container相关

*   display
*   grid-template-columns
*   grid-template-rows
*   grid-template-areas
*   grid-template
*   grid-column-gap
*   grid-row-gap
*   grid-gap
*   justify-items
*   align-items
*   place-items
*   justify-content
*   align-content
*   place-content
*   grid-auto-columns
*   grid-auto-rows
*   grid-auto-flow
*   grid

### Grid Items相关
*   grid-column-start
*   grid-column-end
*   grid-row-start
*   grid-row-end
*   grid-column
*   grid-row
*   grid-area
*   justify-self
*   align-self
*   place-self

# 四、具体布局

## Grid Container

### display
```
.container {
  display: grid | inline-grid;
}
```
- `grid`表示生成一个块级`grid`
- `inline-grid`表示生成一个行内`grid`

### grid-template-columns & grid-template-rows
```
.container {
  grid-template-columns: <track-size> ... | <line-name> <track-size> ...;
  grid-template-rows: <track-size> ... | <line-name> <track-size> ...;
}
```
*   **<track-size>** - 可以是长度值、百分比或者`grid`中的可用空间的一部分单位`fr`
*   **<line-name>** - 一个任意的名字

例如：
```
.container {
  grid-template-columns: 40px 50px auto 50px 40px;
  grid-template-rows: 25px 25px 30px;
}
```
其布局效果如图一所示，
![图一](https://upload-images.jianshu.io/upload_images/1741029-1348aa9354dbe2ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

还可以为每一行或列的距离起个名字，比如：

```
.container {
  grid-template-columns: [first] 40px [line2] 50px [line3] auto [col4-start] 50px [five] 40px [end];
  grid-template-rows: [row1-start] 25% [row1-end] 100px [third-line] auto [last-line];
}
```
![图二](https://upload-images.jianshu.io/upload_images/1741029-94476deb2dcede4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

还可以有多个名字，主要是为了在`Items`相关的地方方便使用：

```
.container {
  grid-template-rows: [row1-start] 25% [row1-end row2-start] 25% [row2-end];
}
```

### repeat()
多个值一致的话可以使用`repeat()`函数来代替多次重复赋值：
```
.container {
  grid-template-columns: repeat(3, 20px [col-start]);
}
```
上面就相当于：
```
.container {
  grid-template-columns: 20px [col-start] 20px [col-start] 20px [col-start];
}
```
如果多行拥有相同的名字，可以使用名字和数字的语法简化：
```
.item {
  grid-column-start: col-start 2;
}
```

### fr 单位
`fr`单位允许将`track`的尺寸作为一个`fraction`设置容器剩余可用空间，例如，如下代码会设置每一个项目的宽为容器三分之一的宽度：
```
.container {
  grid-template-columns: 1fr 1fr 1fr;
}
```
`fr`所占宽度不包括已分配固定值的宽度，如下，分配给三个`fr`单位的宽度不包括`50px`：
```
.container {
  grid-template-columns: 1fr 50px 1fr 1fr;
}
```

### grid-template-areas

使用`grid-area`属性首先定义一个`grid`模板，重复这些`grid`区域使其变成一个个`cells`，`. `代表一个空的`cell`，语法自身提供了`grid`结构的可视化。

```
.container {
  grid-template-areas: 
    "<grid-area-name> | . | none | ..."
    "...";
}
```
- `<grid-area-name>` 使用`grid-area`指定区域的名字
- `. ` 一个空的`grid cell`
- `none` 没有`grid areas`被定义

例如：
```
.item-a {
  grid-area: header;
}
.item-b {
  grid-area: main;
}
.item-c {
  grid-area: sidebar;
}
.item-d {
  grid-area: footer;
}

.container {
  display: grid;
  grid-template-columns: 50px 50px 50px 50px;
  grid-template-rows: auto;
  grid-template-areas: 
    "header header header header"
    "main main . sidebar"
    "footer footer footer footer";
}
```
上面代码生成了一个三行四列的`grid `容器，第一行为头部区域组成，中间由三部分组成，一部分是`main`区域，一部分是空`cell`，另一部分是`sidebar `，最后一行全是`footer`，如图：
![图三](https://upload-images.jianshu.io/upload_images/1741029-54a2979e02ee63b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
每一行都需要有相同数量的`cell`，只要空`cell`之间没有空间，就可以设置任意个`. ` 空`cell`。
注意，如果你的`grid `区域的名字是`foo `，其起始行和最终行会被自动命名为`foo-start `和 `foo-end `，也就是说一些行可以有多个名字。

### grid-template
 `grid-template-rows`、 `grid-template-columns`和 `grid-template-areas `在同一个声明里的缩写。
- `none ` 设置三个属性为其原始值
-  `<grid-template-rows> / <grid-template-columns> ` 设置`grid-template-rows`和`grid-template-columns`为指定值，并设置`grid-template-areas `为`none`。
例如：

```
.container {
  grid-template:
    [row1-start] "header header header" 25px [row1-end]
    [row2-start] "footer footer footer" 25px [row2-end]
    / auto 50px auto;
}
```

以上等价于：

```
.container {
  grid-template-rows: [row1-start] 25px [row1-end row2-start] 25px [row2-end];
  grid-template-columns: auto 50px auto;
  grid-template-areas: 
    "header header header" 
    "footer footer footer";
}
```

- ！因为`grid-template`不会重置隐式`grid `属性（`grid-auto-columns`，`grid-auto-rows`， `grid-auto-flow`），也许正是大部分情况下我们所不想要的，所以建议使用`grid `属性而不是`grid-template `属性。

### grid-column-gap和grid-row-gap
可以认为就是设置行和列之间的间距：
```
.container {
  grid-column-gap: <line-size>;
  grid-row-gap: <line-size>;
}
```   

例如：
``` 
.container {
  grid-template-columns: 100px 50px 100px;
  grid-template-rows: 80px auto 80px; 
  grid-column-gap: 10px;
  grid-row-gap: 15px;
}
``` 
！只能设置`item `之间的间距。不能设置外部边距。
![图四](https://upload-images.jianshu.io/upload_images/1741029-b46294b5451c2c09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
！注意：`grid- `将会被移除，即`grid-column-gap`和`grid-row-gap`将会被重命名为`column-gap`和`row-gap`，目前已经支持的浏览器有`Chrome 68+`，`Safari 11.2 Release 50+` 和`Opera 54+`。

### grid-gap
`grid-column-gap`和`grid-row-gap`的简写版。

``` 
.container {
  grid-gap: <grid-row-gap> <grid-column-gap>;
}
``` 
例如：
``` 
.container {
  grid-template-columns: 100px 50px 100px;
  grid-template-rows: 80px auto 80px; 
  grid-gap: 15px 10px;
}
``` 
如果`grid-row-gap`或者`grid-column-gap`值没有被指定，即只给了一个值，二者皆使用该值。同样，`grid-`前缀也会被移除并重命名为`gap`。

### justify-items 水平对齐
``` 
.container {
  justify-items: start | end | center | stretch;
}
``` 
- `start ` 相当于左对齐
- `end ` 相当于右对齐
- `center ` 相当于居中对齐
- `stretch` 相当于占满整个`cell`
例子：
``` 
.container {
  justify-items: start;
}
``` 
![左对齐](https://upload-images.jianshu.io/upload_images/1741029-33118e7b74756863.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.container {
  justify-items: end;
}
``` 
![右对其](https://upload-images.jianshu.io/upload_images/1741029-11eeaaa01e4c9f75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.container {
  justify-items: center ;
}
``` 
![居中对其](https://upload-images.jianshu.io/upload_images/1741029-c8c54173e304129e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.container {
  justify-items: stretch;
}
``` 
![占满空间](https://upload-images.jianshu.io/upload_images/1741029-0cc3dceb3fdf1039.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### align-items 垂直对齐，与justify-items对应
``` 
.container {
  align-items: start | end | center | stretch;
}
``` 

### place-items
`place-items`同时设置`align-items`和`justify-items`的值，类似地，如果只给了一个值，二者皆使用该值。

- `<align-items> / <justify-items>` 第一个值为`align-items`，第二个为`justify-items`，`/ `隔开。

### justify-content，整体内容水平分布
``` 
.container {
  justify-content: start | end | center | stretch | space-around | space-between | space-evenly;  
}
``` 
- `start ` 容器整体内容左对齐
- `end  ` 容器整体内容右对齐
- `center ` 容器整体内容居中对齐
- `stretch ` 容器整体内容占满
- `space-around` 内容左右均分空间对齐
- `space-between` 除了最左和最右两个最外侧没有空间，其余均分剩余空间对齐
- `space-evenly` 所有项目均分对齐

``` 
.container {
  justify-content: start;
}
``` 
![整体左对齐](https://upload-images.jianshu.io/upload_images/1741029-9d0d51d66134570c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.container {
  justify-content: end; 
}
``` 
![整体右对齐](https://upload-images.jianshu.io/upload_images/1741029-8b1e2723140638cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.container {
  justify-content: center;  
}
``` 
![整体居中对齐](https://upload-images.jianshu.io/upload_images/1741029-42217e1fee81d0e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.container {
  justify-content: stretch; 
}
``` 
![整体占满容器空间](https://upload-images.jianshu.io/upload_images/1741029-ad675954b395bee1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.container {
  justify-content: space-around;  
}
``` 
![左右皆有间距](https://upload-images.jianshu.io/upload_images/1741029-b8563bfa9bf07c3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.container {
  justify-content: space-between; 
}
``` 
![最外侧没有间距](https://upload-images.jianshu.io/upload_images/1741029-118d5c8cffd9ebab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.container {
  justify-content: space-evenly;  
}
``` 
![整体等分间距](https://upload-images.jianshu.io/upload_images/1741029-e58d57a51c421b8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### align-content，整体内容垂直分布
与`justify-content`类似
``` 
.container {
  align-content: start | end | center | stretch | space-around | space-between | space-evenly;  
}
``` 

### place-content

`place-content`同时设置`align-content`和`justify-content`的值，类似地，如果只给了一个值，二者皆使用该值。

- `<align-content> / <justify-content>` 第一个值为`align-content`，第二个为`justify-content`，`/ `隔开。

除了`Edge`浏览器，其他主流浏览器都支持`place-content`缩写属性。

### grid-auto-columns和grid-auto-rows
设置任意自动生成的`grid tracks`（又被叫做`implicit grid tracks`）的尺寸，`Implicit tracks`在`grid items`的数量多于`cells `数量的时候被创建出来，或者当一个`grid item`被放在一个`explicit grid`外部的时候。
``` 
.container {
  grid-auto-columns: <track-size> ...;
  grid-auto-rows: <track-size> ...;
}
``` 

- **<track-size>** 长度、百分比或者`fr`

为了描述`implicit grid tracks`怎样被创建，考虑以下代码：

``` 
.container {
  grid-template-columns: 60px 60px;
  grid-template-rows: 90px 90px;
}
``` 
![](https://upload-images.jianshu.io/upload_images/1741029-67c6d9e668314e9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如图所示，一个`2x2`的`grid`布局被创建出来，但是想象一下使用`grid-column `和`grid-row ` 来进行如下布局：
``` 
.item-a {
  grid-column: 1 / 2;
  grid-row: 2 / 3;
}
.item-b {
  grid-column: 5 / 6;
  grid-row: 2 / 3;
}
``` 
![](https://upload-images.jianshu.io/upload_images/1741029-470d5386ca060153.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我们设置了`.item-b`从第五列开始到第六列结束，但是我们并没有定义第五列和第六列，只有两列，这个时候，宽度为零的`implicit tracks`就会被创建出来填满这些`gap`，此时就可以使用`grid-auto-columns`和`grid-auto-rows`来设置这些`implicit tracks`的宽度，如：
``` 
.container {
  grid-auto-columns: 60px;
}
``` 
![](https://upload-images.jianshu.io/upload_images/1741029-5caea9963bf657ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### grid-auto-flow
如果你有没有显示地布局在`grid`上的`grid items`，自动布局算法就会自动地将这`grid items`放进去，这个属性控制着自动布局算法怎样工作。
``` 
.container {
  grid-auto-flow: row | column | row dense | column dense;
}
``` 
- `row ` 告诉自动布局算法依次填满每一行，有必要的话可以添加新的行
- `column ` 告诉自动布局算法依次填满每一列，有必要的话可以添加新的列
- `dense  ` 告诉自动布局算法如果出现比较小的`item`的话尝试填满之前剩余空间

例子：

``` 
    <section class="container">
      <div class="item-a">item-a</div>
      <div class="item-b">item-b</div>
      <div class="item-c">item-c</div>
      <div class="item-d">item-d</div>
      <div class="item-e">item-e</div>
    </section>

    .container {
      display: grid;
      grid-template-columns: 60px 60px 60px 60px 60px;
      grid-template-rows: 30px 30px;
      grid-auto-flow: row; // 优先排满行
    }

    //指定item-a和item-e占两行
    .item-a {
      grid-column: 1;
      grid-row: 1 / 3;
    }
    .item-e {
      grid-column: 5;
      grid-row: 1 / 3;
    }
``` 
![优先排满行](https://upload-images.jianshu.io/upload_images/1741029-8a5e076c0310b0f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以把`grid-auto-flow`的值设置成`column`：
``` 
.container {
  display: grid;
  grid-template-columns: 60px 60px 60px 60px 60px;
  grid-template-rows: 30px 30px;
  grid-auto-flow: column;
}
``` 
![优先排满列](https://upload-images.jianshu.io/upload_images/1741029-c5c9f71733a8437c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### grid

`grid-template-rows`+ `grid-template-columns`+ `grid-template-areas`,+`grid-auto-rows`+`grid-auto-columns`+`grid-auto-flow`属性的缩写，可以在同一个`grid `属性里设置显示（`explicit `）或者隐式（`implicit `）`grid`。

- `none ` 设置其所有子属性为原始值
- `<grid-template>` 相当于`grid-template`缩写
- `<grid-template-rows> / [ auto-flow && dense? ] <grid-auto-columns>?` 设置`<grid-template-rows>`为指定值，如果`auto-flow`被设置了，`grid-auto-flow`的值被设置成`column`，如果设置成`dense `，自动布局算法使用`dense `填充算法，如果`grid-auto-columns`被省略了，将会被自动设置成`auto`。
- `[ auto-flow && dense? ] <grid-auto-rows>? / <grid-template-columns>` 与上一条类似`auto-flow`对应`grid-auto-flow`的值被设置成`row`。

例子：

以下两块代码是等价的：

``` 
.container {
    grid: 100px 300px / 3fr 1fr;
  }
``` 

``` 
.container {
    grid-template-rows: 100px 300px;
    grid-template-columns: 3fr 1fr;
  }
``` 

以下也是：

``` 
.container {
    grid: auto-flow / 200px 1fr;
  }
``` 

``` 
.container {
    grid-auto-flow: row;
    grid-template-columns: 200px 1fr;
  }
``` 

以下也是：

``` 
.container {
    grid: auto-flow dense 100px / 1fr 2fr;
  }
``` 

``` 
.container {
    grid-auto-flow: row dense;
    grid-auto-rows: 100px;
    grid-template-columns: 1fr 2fr;
  }
``` 

以下还是：

``` 
.container {
    grid: 100px 300px / auto-flow 200px;
  }
``` 

``` 
.container {
    grid-template-rows: 100px 300px;
    grid-auto-flow: column;
    grid-auto-columns: 200px;
  }
``` 

还可以使用更复杂的语法来一次性设置多个值，使用起来十分方便，但是十分复杂：

``` 
.container {
    grid: [row1-start] "header header header" 1fr [row1-end]
          [row2-start] "footer footer footer" 25px [row2-end]
          / auto 50px auto;
  }
``` 

上述代码等价于：

``` 
.container {
    grid-template-areas: 
      "header header header"
      "footer footer footer";
    grid-template-rows: [row1-start] 1fr [row1-end row2-start] 25px [row2-end];
    grid-template-columns: auto 50px auto;    
  }
``` 


## Grid Items
！注意，`float`，`display: inline-block`，`display: table-cell`， `vertical-align` 和`column-*` 属性对`grid item`没有影响。
### grid-column-start、grid-column-end、grid-row-start、grid-row-end

``` 
.item {
  grid-column-start: <number> | <name> | span <number> | span <name> | auto;
  grid-column-end: <number> | <name> | span <number> | span <name> | auto;
  grid-row-start: <number> | <name> | span <number> | span <name> | auto;
  grid-row-end: <number> | <name> | span <number> | span <name> | auto;
}
``` 

- `<line>` 可以是一个`grid line`的数字序号或者名字
- `span <number>` 项目会占满所提供的`grid tracks`的序号
- `span <name>` 项目会占满所提供的`grid tracks`的名字
- `auto` 自动填充

例如：
``` 
.item-a {
  grid-column-start: 2;
  grid-column-end: five;
  grid-row-start: row1-start;
  grid-row-end: 3;
}
``` 
其布局效果如下所示：
![图五](https://upload-images.jianshu.io/upload_images/1741029-d030c44eddd9aa43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.item-b {
  grid-column-start: 1;
  grid-column-end: span col4-start;
  grid-row-start: 2;
  grid-row-end: span 2;
}
``` 
其布局效果如下所示：
![图六](https://upload-images.jianshu.io/upload_images/1741029-7c0afc52db9432d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

！如果没有指定`grid-column-end`或者`grid-row-end`，项目会默认自动跨越一个`track `的距离。项目之间可能会相互重叠，可以使用`z-index`来控制显示的顺序。

### grid-column和grid-row
分别是 `grid-column-start` + `grid-column-end`和`grid-row-start` + `grid-row-end`的缩写。
``` 
.item {
  grid-column: <start-line> / <end-line> | <start-line> / span <value>;
  grid-row: <start-line> / <end-line> | <start-line> / span <value>;
}
``` 

- `<start-line> / <end-line>` 所接收的值和前面一致，`start`和`end`的值用`/ `隔开。

例子：

``` 
.item-c {
  grid-column: 3 / span 2;
  grid-row: third-line / 4;
}
``` 

![图七](https://upload-images.jianshu.io/upload_images/1741029-f6fae30a7b487b9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
！如果没有` <end-line>`值，会默认跨越一个`track`。

### grid-area
给项目起个名字让使用`grid-template-areas`属性创建的模板可以调用，另外，这个属性还可以用作`grid-row-start` + `grid-column-start` + `grid-row-end`+`grid-column-end`的缩写。
``` 
.item {
  grid-area: <name> | <row-start> / <column-start> / <row-end> / <column-end>;
}
``` 

- `<name>` 任意名字
- `<row-start> / <column-start> / <row-end> / <column-end>` 可以是数字或者名字

取个名：

``` 
.item-d {
  grid-area: header;
}
``` 

`grid-row-start` + `grid-column-start` + `grid-row-end`+`grid-column-end`的缩写：

``` 
.item-d {
  grid-area: 1 / col4-start / last-line / 6;
}
``` 

![图八](https://upload-images.jianshu.io/upload_images/1741029-90596d745fc470c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### justify-self，水平方向
在一个`cell`当中对齐一个`grid item `

``` 
.item {
  justify-self: start | end | center | stretch;
}
``` 

``` 
.item-a {
  justify-self: start;
}
``` 
![左对齐](https://upload-images.jianshu.io/upload_images/1741029-554879618c2a6d3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.item-a {
  justify-self: end;
}
``` 
![右对齐](https://upload-images.jianshu.io/upload_images/1741029-e11e9f933088bb44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.item-a {
  justify-self: center ;
}
``` 
![居中对齐](https://upload-images.jianshu.io/upload_images/1741029-305ada4fe264ac18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` 
.item-a {
  justify-self: stretch;
}
``` 
![填满整个内容](https://upload-images.jianshu.io/upload_images/1741029-f08359120f1fbe5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### align-self，垂直方向
与` justify-self`类似。
``` 
.item {
  align-self: start | end | center | stretch;
}
``` 

### place-self

`align-self ` 和`justify-self `的缩写。

- `auto ` 默认对齐方式
- `<align-self> / <justify-self>` 垂直/水平

除了`Edge`浏览器，其他主流浏览器都支持`place-self `缩写属性。


# 五、参考（原文）

[https://css-tricks.com/snippets/css/complete-guide-grid](https://css-tricks.com/snippets/css/complete-guide-grid)