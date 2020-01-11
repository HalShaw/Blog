---

title: CSS3之Transform，Transition，Animation的用法
date: 2019-12-01 22:09
tags: CSS3 Transform Transition Animation
categories: CSS
---

# 一、前言

之前由于写CSS动画比较少，也很少遇到上面这几个属性的具体用法，但是在后来的开发过程中，经常碰到这几个属性但却不明白起用法和意义，这就是该下功夫补课的地方了，下面整理并记录一下这几个属性的具体用法和区别。

# 二、具体用法

## Transform，转换

#### rotate，旋转

- rotate(angle) 定义 2D 旋转
- rotate3d(x,y,z,angle) 定义 3D 旋转
- rotateX(angle) 定义沿着 X 轴的 3D 旋转
- rotateY(angle) 定义沿着 Y 轴的 3D 旋转
- rotateZ(angle) 定义沿着 Z 轴的 3D 旋转

<!--more-->

该属性用来旋转元素，下面是一个例子。

```
transform:rotate(9deg);
-ms-transform:rotate(9deg); /* Internet Explorer */
-moz-transform:rotate(9deg); /* Firefox */
-webkit-transform:rotate(9deg); /* Safari 和 Chrome */
-o-transform:rotate(9deg); /* Opera */
```
[在线实例](https://www.w3school.com.cn/tiy/t.asp?f=css3_transform)


#### translate，移动

- translate(x,y) 定义2D移动
- translate3d(x,y,z) 定义3D移动
- translateX(x) 只用 X 轴的值来移动
- translateY(y) 只用 Y 轴的值来移动
- translateZ(z) 只用 Z 轴的值来移动

`Transform `属性的一个方法，使用`translate() `方法，可以设置该元素当前的 left（x 坐标） 和 top（y 坐标） 位置，来进行移动到坐标点对应的位置。

```
transform: translate(50px, 100px);
-ms-transform: translate(50px,100px); /* Internet Explorer */
-webkit-transform: translate(50px,100px); /* Safari 和 Chrome */
 -o-transform: translate(50px,100px); /* Opera */
-moz-transform: translate(50px,100px); /* Firefox */
```

#### scale，缩放

- scale(x,y) 定义 2D 缩放
- scale3d(x,y,z) 定义 3D 缩放
- scaleX(x) 只用 X 轴的值来缩放
- scaleY(y) 只用 Y 轴的值来缩放
- scaleZ(z) 只用 Z 轴的值来缩放

下面代码表示X轴和Y轴都放大为原来的二倍:

```
transform: scale(2, 2);
```

#### skew，倾斜

- skew(x-angle,y-angle) 定义沿着 X 和 Y 轴的 2D 倾斜
- skewX(angle) 定义沿着 X 轴的 2D 倾斜
- skewY(angle) 定义沿着 Y 轴的 2D 倾斜

下面代码表示X 轴和Y 轴方向各倾斜20度：

```
transform: skew(20deg, 20deg);
```


## Transition，过渡

- transition 简写属性，用于在一个属性中设置四个过渡属性
- transition-property 设置应用过渡的 CSS 属性的名称
- transition-duration 设置过渡效果花费的时间。默认是 0
- transition-timing-function 设置过渡效果的时间曲线。默认是 "ease"
- transition-delay 设置过渡效果何时开始。默认是 0

#### transition 

```
transition: property duration timing-function delay;
```

#### transition-property

```
transition-property: none|all|property;
```
- none 没有属性会获得过渡效果
- all 所有属性都将获得过渡效果
- property 定义应用过渡效果的 CSS 属性名称列表，列表以逗号分隔

#### transition-duration

```
transition-duration: time;
```

#### transition-timing-function

```
transition-timing-function: linear|ease|ease-in|ease-out|ease-in-out|cubic-
bezier(n,n,n,n);
```

- linear	设置以相同速度开始至结束的过渡效果（等于 cubic-bezier(0,0,1,1)）
- ease	设置慢速开始，然后变快，然后慢速结束的过渡效果（cubic-bezier(0.25,0.1,0.25,1)）
- ease-in	设置以慢速开始的过渡效果（等于 cubic-bezier(0.42,0,1,1)）
- ease-out	设置以慢速结束的过渡效果（等于 cubic-bezier(0,0,0.58,1)）
- ease-in-out	设置以慢速开始和结束的过渡效果（等于 cubic-bezier(0.42,0,0.58,1)）
- cubic-bezier(n,n,n,n)	在 cubic-bezier 函数中定义自己的值，可能的值是 0 至 1 之间的数值

#### transition-delay

```
transition-delay: time;
```
实例：
```
div
{
transition-property: width;
transition-duration: 1s;
transition-timing-function: linear;
transition-delay: 2s;
/* Firefox 4 */
-moz-transition-property:width;
-moz-transition-duration:1s;
-moz-transition-timing-function:linear;
-moz-transition-delay:2s;
/* Safari 和 Chrome */
-webkit-transition-property:width;
-webkit-transition-duration:1s;
-webkit-transition-timing-function:linear;
-webkit-transition-delay:2s;
/* Opera */
-o-transition-property:width;
-o-transition-duration:1s;
-o-transition-timing-function:linear;
-o-transition-delay:2s;
}
```
[在线实例](https://www.w3school.com.cn/tiy/t.asp?f=css3_transition3)

上面实例的简写版：

```
div
{
transition: width 1s linear 2s;
/* Firefox 4 */
-moz-transition:width 1s linear 2s;
/* Safari and Chrome */
-webkit-transition:width 1s linear 2s;
/* Opera */
-o-transition:width 1s linear 2s;
}
```
[在线实例](https://www.w3school.com.cn/tiy/t.asp?f=css3_transition4)

## Animation，动画

- @keyframes 设置动画
- animation 所有动画属性的简写属性，除了 animation-play-state 属性
- animation-name 设置@keyframes 动画的名称
- animation-duration 设置动画完成一个周期所花费的秒或毫秒。默认是 0
- animation-timing-function 设置动画的速度曲线。默认是 "ease"
- animation-delay 设置动画何时开始。默认是 0
- animation-iteration-count 设置动画被播放的次数。默认是 1
- animation-direction 设置动画是否在下一周期逆向地播放。默认是 "normal"
- animation-play-state 设置动画是否正在运行或暂停。默认是 "running"
- animation-fill-mode 设置对象动画时间之外的状态

#### @keyframes 

```
@keyframes animationname {keyframes-selector {css-styles;}}
```

- animationname 必需，定义动画的名称
- keyframes-selector 必需，动画时长的百分比
合法的值：0-100%
from（与 0% 相同）
to（与 100% 相同）
- css-styles 必需，一个或多个合法的 CSS 样式属性

```
@keyframes mymove
{
from {top:0px;}
to {top:200px;}
}

@-moz-keyframes mymove /* Firefox */
{
from {top:0px;}
to {top:200px;}
}

@-webkit-keyframes mymove /* Safari 和 Chrome */
{
from {top:0px;}
to {top:200px;}
}

@-o-keyframes mymove /* Opera */
{
from {top:0px;}
to {top:200px;}
}
```
目前浏览器都不支持 `@keyframes `规则。
Firefox 支持替代的 `@-moz-keyframes `规则。
Opera 支持替代的 `@-o-keyframes `规则。
Safari 和 Chrome 支持替代的 `@-webkit-keyframes `规则。

#### animation 

```
animation: name duration timing-function delay iteration-count direction;
```
- animation-name 设置需要绑定到选择器的 keyframe 名称
- animation-duration 设置完成动画所花费的时间，以秒或毫秒计
- animation-timing-function 设置动画的速度曲线
- animation-delay 设置在动画开始之前的延迟
- animation-iteration-count 设置动画应该播放的次数
- animation-direction 设置是否应该轮流反向播放动画

#### animation-name

```
animation-name: keyframename|none;
```
#### animation-duration

```
animation-duration: time;
```

#### animation-timing-function

```
animation-timing-function: value;
```

- linear 动画从头到尾的速度是相同的
- ease 默认，动画以低速开始，然后加快，在结束前变慢
- ease-in	动画以低速开始
- ease-out 动画以低速结束
- ease-in-out 动画以低速开始和结束
- cubic-bezier(n,n,n,n) 在 cubic-bezier 函数中自己的值，可能的值是从 0 到 1 的数值

一些具体用法：

```
/* W3C 和 Opera: */
#div1 {animation-timing-function: linear;}
#div2 {animation-timing-function: ease;}
#div3 {animation-timing-function: ease-in;}
#div4 {animation-timing-function: ease-out;}
#div5 {animation-timing-function: ease-in-out;}
/* Firefox: */
#div1 {-moz-animation-timing-function: linear;}
#div2 {-moz-animation-timing-function: ease;}
#div3 {-moz-animation-timing-function: ease-in;}
#div4 {-moz-animation-timing-function: ease-out;}
#div5 {-moz-animation-timing-function: ease-in-out;}
/* Safari 和 Chrome: */
#div1 {-webkit-animation-timing-function: linear;}
#div2 {-webkit-animation-timing-function: ease;}
#div3 {-webkit-animation-timing-function: ease-in;}
#div4 {-webkit-animation-timing-function: ease-out;}
#div5 {-webkit-animation-timing-function: ease-in-out;}
```
[实例](https://www.w3school.com.cn/tiy/t.asp?f=css3_animation-timing-function2)

通过 cubic-bezier 函数来定义速度曲线：
```
/* W3C 和 Opera: */
#div1 {animation-timing-function: cubic-bezier(0,0,1,1);}
#div2 {animation-timing-function: cubic-bezier(0.25,0.1,0.25,1);}
#div3 {animation-timing-function: cubic-bezier(0.42,0,1,1);}
#div4 {animation-timing-function: cubic-bezier(0,0,0.58,1);}
#div5 {animation-timing-function: cubic-bezier(0.42,0,0.58,1);}
/* Firefox: */
#div1 {-moz-animation-timing-function: cubic-bezier(0,0,1,1);}
#div2 {-moz-animation-timing-function: cubic-bezier(0.25,0.1,0.25,1);}
#div3 {-moz-animation-timing-function: cubic-bezier(0.42,0,1,1);}
#div4 {-moz-animation-timing-function: cubic-bezier(0,0,0.58,1);}
#div5 {-moz-animation-timing-function: cubic-bezier(0.42,0,0.58,1);}
/* Safari 和 Chrome: */
#div1 {-webkit-animation-timing-function: cubic-bezier(0,0,1,1);}
#div2 {-webkit-animation-timing-function: cubic-bezier(0.25,0.1,0.25,1);}
#div3 {-webkit-animation-timing-function: cubic-bezier(0.42,0,1,1);}
#div4 {-webkit-animation-timing-function: cubic-bezier(0,0,0.58,1);}
#div5 {-webkit-animation-timing-function: cubic-bezier(0.42,0,0.58,1);}
```
[实例](https://www.w3school.com.cn/tiy/t.asp?f=css3_animation-timing-function3)

#### animation-delay

```
animation-delay: time;
```

#### animation-iteration-count

```
animation-iteration-count: n|infinite;
```
- n 设置动画播放次数的数值
- infinite 设置动画应该无限次播放

#### animation-direction

```
animation-direction: normal|alternate;
```
- normal 默认值，动画应该正常播放
- alternate 动画应该轮流反向播放

#### animation-play-state

```
animation-play-state: paused|running;
```

- paused	设置动画已暂停 
- running	设置动画正在播放

#### animation-fill-mode
```
animation-fill-mode : none | forwards | backwards | both;
```
- none 不改变默认行为
- forwards 当动画完成后，保持最后一个属性值（在最后一个关键帧中定义）
- backwards 在 animation-delay 所指定的一段时间内，在动画显示之前，应用开始属性值（在第一个关键帧中定义）
- both 向前和向后填充模式都被应用

## 一些实例

#### 一个改变背景色动画

```
@keyframes myfirst
{
from {background: red;}
to {background: yellow;}
}

@-moz-keyframes myfirst /* Firefox */
{
from {background: red;}
to {background: yellow;}
}

@-webkit-keyframes myfirst /* Safari 和 Chrome */
{
from {background: red;}
to {background: yellow;}
}

@-o-keyframes myfirst /* Opera */
{
from {background: red;}
to {background: yellow;}
}
```

把 `myfirst` 动画捆绑到 `div` 元素，时长：5 秒：

```
div
{
animation: myfirst 5s;
-moz-animation: myfirst 5s;	/* Firefox */
-webkit-animation: myfirst 5s;	/* Safari 和 Chrome */
-o-animation: myfirst 5s;	/* Opera */
}
```
[在线查看](https://www.w3school.com.cn/tiy/t.asp?f=css3_animation1)

当动画为 25% 及 50% 时改变背景色，然后当动画 100% 完成时再次改变：

```
@keyframes myfirst
{
0%   {background: red;}
25%  {background: yellow;}
50%  {background: blue;}
100% {background: green;}
}

@-moz-keyframes myfirst /* Firefox */
{
0%   {background: red;}
25%  {background: yellow;}
50%  {background: blue;}
100% {background: green;}
}

@-webkit-keyframes myfirst /* Safari 和 Chrome */
{
0%   {background: red;}
25%  {background: yellow;}
50%  {background: blue;}
100% {background: green;}
}

@-o-keyframes myfirst /* Opera */
{
0%   {background: red;}
25%  {background: yellow;}
50%  {background: blue;}
100% {background: green;}
}
```
[实例在线地址](https://www.w3school.com.cn/tiy/t.asp?f=css3_animation2)

#### 改变背景色和位置：
```
@keyframes myfirst
{
0%   {background: red; left:0px; top:0px;}
25%  {background: yellow; left:200px; top:0px;}
50%  {background: blue; left:200px; top:200px;}
75%  {background: green; left:0px; top:200px;}
100% {background: red; left:0px; top:0px;}
}

@-moz-keyframes myfirst /* Firefox */
{
0%   {background: red; left:0px; top:0px;}
25%  {background: yellow; left:200px; top:0px;}
50%  {background: blue; left:200px; top:200px;}
75%  {background: green; left:0px; top:200px;}
100% {background: red; left:0px; top:0px;}
}

@-webkit-keyframes myfirst /* Safari 和 Chrome */
{
0%   {background: red; left:0px; top:0px;}
25%  {background: yellow; left:200px; top:0px;}
50%  {background: blue; left:200px; top:200px;}
75%  {background: green; left:0px; top:200px;}
100% {background: red; left:0px; top:0px;}
}

@-o-keyframes myfirst /* Opera */
{
0%   {background: red; left:0px; top:0px;}
25%  {background: yellow; left:200px; top:0px;}
50%  {background: blue; left:200px; top:200px;}
75%  {background: green; left:0px; top:200px;}
100% {background: red; left:0px; top:0px;}
}
```
[实例在线地址](https://www.w3school.com.cn/tiy/t.asp?f=css3_animation3)

#### 运行名为 myfirst 的动画，其中设置了所有动画属性：

```
div
{
animation-name: myfirst;
animation-duration: 5s;
animation-timing-function: linear;
animation-delay: 2s;
animation-iteration-count: infinite;
animation-direction: alternate;
animation-play-state: running;
/* Firefox: */
-moz-animation-name: myfirst;
-moz-animation-duration: 5s;
-moz-animation-timing-function: linear;
-moz-animation-delay: 2s;
-moz-animation-iteration-count: infinite;
-moz-animation-direction: alternate;
-moz-animation-play-state: running;
/* Safari 和 Chrome: */
-webkit-animation-name: myfirst;
-webkit-animation-duration: 5s;
-webkit-animation-timing-function: linear;
-webkit-animation-delay: 2s;
-webkit-animation-iteration-count: infinite;
-webkit-animation-direction: alternate;
-webkit-animation-play-state: running;
/* Opera: */
-o-animation-name: myfirst;
-o-animation-duration: 5s;
-o-animation-timing-function: linear;
-o-animation-delay: 2s;
-o-animation-iteration-count: infinite;
-o-animation-direction: alternate;
-o-animation-play-state: running;
}
```

上面的简写版：

```
div
{
animation: myfirst 5s linear 2s infinite alternate;
/* Firefox: */
-moz-animation: myfirst 5s linear 2s infinite alternate;
/* Safari 和 Chrome: */
-webkit-animation: myfirst 5s linear 2s infinite alternate;
/* Opera: */
-o-animation: myfirst 5s linear 2s infinite alternate;
}
```

# 三、参考
[https://www.w3school.com.cn/css3/css3_animation.asp](https://www.w3school.com.cn/css3/css3_animation.asp)

