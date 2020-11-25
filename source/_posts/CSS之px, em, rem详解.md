---

title: CSS之px，em，rem详解
date: 2020-03-09 21:35
tags: CSS px em rem
categories: CSS

---

# 一、 背景

CSS的`font-size`属性对应的三个单位 px，em，rem 用了好久，发现对
rem的理解还不是很透彻，网上查了查资料，打算彻底弄明白这三个单位之间的区别。

# 二、px

px是最常用以及最简单的基本单位，缺点是当浏览器默认字体改变时，其对应的值也不会变，可能会影响用户体验。

# 三、em

一个em是其对应父元素的相对字体大小，如果一个元素字体设置为`font-size: 16px;`，其子元素的`1em = 16px`，如果其父元素没有指定字体，会从下到上继承祖先元素，如果都没有的话会继承浏览器提供的默认根元素尺寸。

# 四、rem

rem用的是` root element’s font-size`，即根元素的尺寸的相对值，若浏览器默认字体大小为`16px`，则`1rem = 16px`。通常为了方便计算，可以把根元素的`font-size`设置为`10px`，这样`1rem = 10px`。

但是这样就会出现上面说的关于px的问题，即浏览器默认字体改变时，网站对应的字体也不会发生变化，可能会影响用户体验。

所以又有了相应的解决方案，使用百分百，浏览器默认字体通常是`16px`，可以设置根元素`font-size: 62.5%;`，因为`16px`的62.5%是`10px`，这样既使用了相对单位且方便了计算，又解决了px带来的问题。


```
html { font-size: 62.5%; } 
body { font-size: 1.4rem; } /* =14px */
h1   { font-size: 2.4rem; } /* =24px */
```

但是，老版本浏览器可能不支持rem，可以使用px来解决：

```
html { font-size: 62.5%; } 
body { font-size: 14px; font-size: 1.4rem; } /* =14px */
h1   { font-size: 24px; font-size: 2.4rem; } /* =24px */
```

# 五、参考

[https://snook.ca/archives/html_and_css/font-size-with-rem](https://snook.ca/archives/html_and_css/font-size-with-rem)
[https://www.sitepoint.com/understanding-and-using-rem-units-in-css/](https://www.sitepoint.com/understanding-and-using-rem-units-in-css/)
[https://medium.com/code-better/css-units-for-font-size-px-em-rem-79f7e592bb97](https://medium.com/code-better/css-units-for-font-size-px-em-rem-79f7e592bb97)
