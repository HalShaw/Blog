---

title: RxJS之switchMap, concatMap, mergeMap, exhaustMap的区别
date: 2020-11-25 10:27
comments: true
tags: RxJS, Angular
categories: Angular

---



# 前言
在`Angular`里面，经常会用到`RxJS`的`switchMap, concatMap, mergeMap, exhaustMap`，用的比较多的是`switchMap`，之前都是糊里糊涂搞不清楚其中的用法及原理，以及其与另外三者的区别与联系。谷歌上面找到一篇说的比较清晰明了的文章，在此稍微记录一下下。

# 一、switchMap
**最常用**，当发起多次`HTTP`请求时，只关心最近一次请求，会取消先前的请求，保证返回值是最新的。

# 二、concatMap
相当于**同步策略**，当发起多次`HTTP`请求时，依次排队等待，后一次请求需等待前一次请求有返回值之后才能发起请求，返回值需要有序列性时可以使用。

# 三、mergeMap
相当于**异步策略**，并发执行，不关心返回值的顺序。

# 四、exhaustMap
在内部流执行时忽略外部流，也就是在一次请求执行过程中，无法再触发另外的请求。

# 参考
https://angular-academy.com/rxjs-switchmap-concatmap-mergemap-exhaustmap/#switchmap