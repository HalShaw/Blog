---

title: HTTP缓存机制之强缓存和协商缓存区别与联系
date: 2019-12-22 22:12
tags: HTTP 强缓存 协商缓存
categories: HTTP缓存机制

---

# 一、前言

HTTP缓存在前端开发过程中有着举足轻重的地位，因为其在性能优化的过程中必不可少，许多不太常变的资源可以缓存在浏览器端，无需每次都发起请求重新获取，好的缓存策略可以大大提高网站加载及渲染效率。

# 二、强缓存

强缓存有`Expires`和`Cache-Control`两种响应头方式实现。

### Expires

`Expires`是`HTTP1.0`提出的一个表示资源过期时间的请求头，它描述的是一个绝对时间，由服务器返回。
Expires 受限于本地时间，如果修改了本地时间，可能会造成缓存失效，而且由于本地时间与服务器时间可能存在误差，可能会存在问题，其值如下：

```
Expires: Thu, 14 Mar 2019 17:29:17 GMT
```

<!--more-->

### Cache-Control

- public：指示响应可被任何缓存区缓存，如果响应被标记为public，即使有关联的 HTTP 认证，甚至响应状态码无法正常缓存，响应也可以被缓存。
- private：指示对于单个用户的整个或部分响应消息，不能被共享缓存处理。这允许服务器仅仅描述当用户的部分响应消息，此响应消息对于其他用户的请求无效。
- no-cache：表示必须先与服务器确认返回的响应是否被更改，然后才能使用该响应来满足后续对同一个网址的请求。因此，如果存在合适的验证令牌 (ETag)，no-cache 会发起往返通信来验证缓存的响应，如果资源未被更改，可以避免下载。
- no-store：用于防止重要的信息被无意的发布。在请求消息中发送将使得请求和响应消息都不使用缓存。禁止缓存任何响应，也就是说每次用户请求资源时，都会向服务器发送一个请求，每次都会下载完整的响应。
- max-age：用来设置资源被缓存的最长时间(单位是秒)。指示客户机可以接收生存期不大于指定时间（以秒为单位）的响应。
- min-fresh：指示客户机可以接收响应时间小于当前时间加上指定时间的响应。
- max-stale：指示客户机可以接收超出超时期间的响应消息。如果指定max-stale消息的值，那么客户机可以接收超出超时期指定值之内的响应消息。

#### `no-cache` 和 `no-store`的区别：
`no-cache`标记的资源是可以存储在本地缓存中的，但是在与原始服务器进行新鲜度验证之前，缓存不能将其提供给客户端使用。`no-store`是不允许缓存任何响应。

### `Cache-Control`优先级高于`Expires`。

# 三、协商缓存

当某个请求没有命中强缓存时，就会发起一个请求到服务器端验证协商缓存是否命中，如果命中，由服务器端来决定是否使用该缓存，若该缓存未过期且未被修改，请求响应就会返回一个304状态码为然后显示`Not Modified`。

协商缓存使用的是`Last-Modified & If-Modified-Since`和`ETag & If-None-Match`这两对Header。

### Last-Modified和If-Modified-Since

`Last-Modified`是指服务器端文件的最后修改时间，需要和 `cache-control` 配合使用，是检查服务器端资源是否更新的一种方式。当浏览器再次进行请求时，会向服务器传送`If-Modified-Since`请求头，询问 `Last-Modified` 时间点之后资源是否被修改过。如果没有修改，则返回码为 304，使用浏览器端缓存；如果修改过，则再次去服务器请求资源，状态码和首次请求相同为 200，使用服务器最新资源。

### ETag和If-None-Match

`Etag & If-None-Match` 与 `Last-Modified & If-Modified-Since` 的机制类似，不同的是，`Etag`是通过一个校验码来对比资源是否更改过的，而不是通过资源的修改时间。当一个资源修改时，其校验码也会更改。`Etag`就像一个唯一指纹，资源变化都会导致·ETag·变化，跟最后修改时间没有关系，`ETag`可以保证每一个资源是唯一的。当浏览器请求资源时，服务器会返回一个`Etag`字段，然后浏览器下一次请求时，会带上 `If-None-Match` ，值为上次服务器返回的`Etag`的值，服务器经过校验码的对比后决定返回200或304。

### 优先级

`Etag`可以解决 `Last-Modified` 不太好处理的问题，`Etag`能更准确地控制缓存，因此，`Etag`的优先级是高于 `Last-Modified` 。
`Last-Modified` 可能会有以下一些问题，但是`Etag`可以很好地解决的：

- 一些文件也许会周期性的更改，但是他的内容并不改变(仅仅改变的修改时间)，所以这个时候会被识别为修改过了而重新发起请求。
- 某些文件修改非常频繁，比如在秒以下的时间内进行修改，(比方说1s内修改了N次)，`If-Modified-Since`能检查到的粒度是s级的，这种修改无法判断(或者说UNIX记录MTIME只能精确到秒)。
- 某些服务器不能精确的得到文件的最后修改时间。

# 四、完整的缓存策略过程

![缓存过程](https://upload-images.jianshu.io/upload_images/1741029-4fc0e06fc0f1f4e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 五、参考

[https://github.com/amandakelake/blog/issues/41](https://github.com/amandakelake/blog/issues/41)
[http://www.alloyteam.com/2016/03/discussion-on-web-caching/#prettyPhoto](http://www.alloyteam.com/2016/03/discussion-on-web-caching/#prettyPhoto)
[https://www.jianshu.com/p/c78b5de7a889](https://www.jianshu.com/p/c78b5de7a889)



