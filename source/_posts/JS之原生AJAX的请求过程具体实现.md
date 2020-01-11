---

title: JS之原生AJAX的请求过程具体实现
date: 2019-10-28 22:48
tags: JavaScript AJAX
categories: JavaScript
---

# 一、AJAX的请求大致过程

- 创建一个异步调用`XMLHttpRequest`对象 
- 打开一个新的`HTTP`请求，并指定该`HTTP`请求的方法、`URL`及验证信息，设置响应`HTTP`请求状态变化的函数
- 发送`HTTP`请求
- 获取异步调用返回的数据
- 使用JavaScript和DOM实现局部刷新

# 二、具体实现

先创建一个兼容各个平台的`XMLHttpRequest`对象 

      
        function getXHR(){
          var xhr = null;
          if(window.XMLHttpRequest) {// 兼容 IE7+, Firefox, Chrome, Opera, Safari
            xhr = new XMLHttpRequest();
          } else if (window.ActiveXObject) {
            try {
              xhr = new ActiveXObject("Msxml2.XMLHTTP");// 即MSXML3
            } catch (e) {
              try {
                xhr = new ActiveXObject("Microsoft.XMLHTTP");// // 兼容 IE6, IE5，API很老，不建议使用
              } catch (e) {
                alert("您的浏览器不支持AJAX.");
              }
            }
          }
          return xhr;
        }
  
<!--more-->

一个完整的AJAX实现过程：
    

        var xhr = getXHR(); // 创建异步调用对象 
        xhr.open('GET/POST', url/file, true);  //设置请求方式，url，以及是否异步
        xhr.onreadystatechange = function() {   //设置回调监听函数
           if(xhr.readyState==4){ //表示后台处理完成
                if(xhr.status==200){ //表示处理结果是成功的
                    var data = xhr.responseText; //获取成功的返回值
                     console.log(data);
           }
        };
        xhr.onerror = function() {
          console.log("Something is wrong.");
        };
         xhr.send(); 
    
# 三、状态码
`readyState`一共有5中请求状态，从0 到 4 分别表示：
    
        0: 请求未初始化
        1: 服务器连接已建立
        2: 请求已接收
        3: 请求处理中
        4: 请求已完成，且响应已就绪
    

`HTTP`响应状态码，常见的表示：
    
        200:  成功
        304：未修改，该资源在上次请求之后没有任何修改（这通常用于浏览器的缓存机制，使用GET请求时尤其需要注意）。
        403：（禁止）服务器拒绝请求。
        404：（未找到）服务器找不到请求的网页。
        500：（服务器内部错误）服务器遇到错误，无法完成请求。
    
# 四、注意点

### 1. `xhr.open`的参数需熟记：
- method：请求的方法类型，GET 或 POST
- url：文件在服务器上的位置，相对位置或绝对位置
- async：true 或 false

### 2. post请求与get请求的不同之处

`send(string)`方法`post`请求时才需要使用字符串参数，否则不用带参数。

### 3. post请求一定要设置请求头的格式内容

```
xhr.open("POST", "ajax_test.html", true);  
xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded");  
xhr.send("name=Shaw&age=24");  
```

### 4. 服务器响应数据格式
- responseText 字符串形式的响应数据
- responseXML XML形式的响应数据

# 参考
[https://juejin.im/post/5bb470295188255c5e66f88f#heading-0](https://juejin.im/post/5bb470295188255c5e66f88f#heading-0)
[https://www.cnblogs.com/jianxian/p/9069804.html](https://www.cnblogs.com/jianxian/p/9069804.html)
