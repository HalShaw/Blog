---

title: 深入理解JavaScript中的Web Worker
date: 2019-08-04 17:07
tags: JavaScript Web Worker
categories: JavaScript
---

# 一、背景
由于JavaScript是单线程模型，即所有任务只能在同一个线程上面完成，前面的任务没有做完，后面的就只能等待，这对于日益增强的计算能力来说不是一件好事。所以在HTML5中引入了Web Worker的概念，来为JavaScript创建多线程环境，将其中一些任务分配给Web Worker运行，二者可以同时运行，互不干扰，如图：
![With or without worker](https://upload-images.jianshu.io/upload_images/1741029-d566f191add913c3?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



Web Worker是运行在后台的 JavaScript，独立于其他脚本，不会影响页面的性能。您可以继续做任何愿意做的事情：点击、选取内容等等，引入Web Worker的好处是一些计算密集型或高延迟的任务，被 Web Worker 线程所处理，主线程就会很流畅，不会被阻塞或拖慢，而此时 Web Worker 在后台运行。但是这也正是 Web Worker 比较耗费资源的原因，不应该过度使用，而且一旦使用完毕，就应该关闭。

# 二、浏览器支持
所有主流浏览器均支持 Worker，除了 Internet Explorer。
可以使用如下代码检测 Worker 支持
在创建 Worker 之前，请检测用户的浏览器是否支持它：


        if(typeof(Worker)!=="undefined")
          {
          // Yes! Web worker support!
          // Some code.....
          }
        else
          {
          // Sorry! No Web Worker support..
          }
    
<!--more-->

# 三、使用
Web Worker 在独立线程中运行。因此，它们执行的代码需要保存在一个单独的文件中。但在保存代码前，我们要先在主线程上创建新的 Worker 对象。构造函数采用 Worker 脚本的名称：
    
        var worker = new Worker('worker.js');
    
如果指定的异步下载文件存在，浏览器就会生成新的 Worker 线程。在完全下载并执行文件之前，系统不会生成 Worker。如果指向您 Worker 的路径返回 404，Worker 就会在不显示任何提示的情况下失败。

创建 Worker 之后，通过调用 postMessage() 方法启动：
    
        worker.postMessage(); // Start the worker.
    

- 传递变量，也可以给Worker传递一个变量
    

        // Create a new worker object
        var worker = new Worker('worker.js');

        // Send a message to start the worker and pass a variable to it
        var info = 'Web Workers';
        worker.postMessage(info);
    
在Worker中，例如，在`worker.js`内部，可以使用`onmessage`事件来接收来自主线程的消息并且实现一些操作，可以通过`event.data`来获取到传递的变量：
    
        // Receive the message from the main thread
        onmessage = function(event) {
          // Do something
          var info = event.data;
        };
    
从Worker 发送消息到主线程也是同样的方法(`worker.js`)：
    
        // Receive the message from the main thread
        onmessage = function(event) {
          // Do something
          var info = event.data;
          var result = info + ' rise up!';
          postMessage(result);
        };
    
主线程：
    
        // Create a new worker object
        var worker = new Worker('worker.js');

        // Send a message to start the worker and pass a variable to it
        var info = 'Web Workers';
        worker.postMessage(info);

        // Receive a message from the worker
        worker.onmessage = function (event) {
          // Do something
          alert(event.data);
        };
    
也可以使用`addEventListener`来替换`onmessage `（JavaScript 高手们总是会推荐 `addEventListener`），例如：
主线程：
    
        var worker = new Worker('doWork.js');

        worker.addEventListener('message', function(e) {
          console.log('Worker said: ', e.data);
        }, false);

        worker.postMessage('Hello World'); // Send data to our worker.
    
`doWork.js` (Worker)：
    
        self.addEventListener('message', function(e) {
          self.postMessage(e.data);
        }, false);
    

在主线程和 Worker 之间传递的消息是复制而不是共享的。例如，下一示例中 JSON 消息的“msg”属性在两个位置中均可访问。即使对象运行在单独的专用空间中，系统似乎也会将其直接传递给 Worker。实际发生的情况是，系统将对象传递给 Worker 后，会将其序列化，随后在另一端解取消序列化。由于网页和 Worker 并不共享同一实例，因此每次传递时都要进行复制。大部分浏览器通过在任一端上对值进行自动 JSON 编码/解码来实施此功能。

主线程：
    
        <button onclick="sayHI()">Say HI</button>
        <button onclick="unknownCmd()">Send unknown command</button>
        <button onclick="stop()">Stop worker</button>
        <output id="result"></output>

        <script>
          function sayHI() {
            worker.postMessage({'cmd': 'start', 'msg': 'Hi'});
          }

          function stop() {
            // Calling worker.terminate() from this script would also stop the worker.
            worker.postMessage({'cmd': 'stop', 'msg': 'Bye'});
          }

          function unknownCmd() {
            worker.postMessage({'cmd': 'foobard', 'msg': '???'});
          }

          var worker = new Worker('doWork2.js');

          worker.addEventListener('message', function(e) {
            document.getElementById('result').textContent = e.data;
          }, false);
        </script>
    
`doWork2.js`：
    
        self.addEventListener('message', function(e) {
          var data = e.data;
          switch (data.cmd) {
            case 'start':
              self.postMessage('WORKER STARTED: ' + data.msg);
              break;
            case 'stop':
              self.postMessage('WORKER STOPPED: ' + data.msg + '. (buttons will no longer work)');
              self.close(); // Terminates the worker.
              break;
            default:
              self.postMessage('Unknown command: ' + data.msg);
          };
        }, false);
    
- 停止 Worker
停止 Worker 的方法有两种：在主线程中调用 `worker.terminate()`，或在 Worker 本身内部调用 `self.close()`。
- Worker 作用域

就 Worker 来说，self 和 this 指的都是 Worker 的全局作用域。因此，上一示例也可以写成：
    

        addEventListener('message', function(e) {
          var data = e.data;
          switch (data.cmd) {
            case 'start':
              postMessage('WORKER STARTED: ' + data.msg);
              break;
            case 'stop':
          ...
        }, false);
    
# 四、加载外部脚本
可以通过 `importScripts()` 函数将外部脚本文件或库加载到 Worker 中。该方法采用零个或多个字符串表示要导入的资源的文件名。

此示例将 script1.js 和 script2.js 加载到了 Worker 中：
    
        importScripts('script1.js');
        importScripts('script2.js');
    
也可以写成单个导入语句：
    
        importScripts('script1.js', 'script2.js');
    
# 五、子 Worker
Worker 可以生成子 Worker。这对于在运行时进一步拆分大任务来说非常重要。但是，子 Worker 还有几点注意事项：

*   子 Worker 必须托管在与父线程相同的origin中。
*   子 Worker 中的 URI 应相对于父 Worker 的位置进行解析（与主线程不同）。

注意，大部分浏览器会为每个 Worker 生成单独的进程。在开始生成 Worker 场之前，请注意不要占用太多的用户系统资源。这样做的一个原因是，在主线程和 Worker 之间传递的消息是复制而不是共享的。

# 六、处理错误
与任何 JavaScript 逻辑一样，您需要处理 Web Worker 中出现的任何错误。如果在执行 Worker 时出现错误，就会触发 ErrorEvent。相关界面中包含用于找出错误内容的三个实用属性：filename - 导致错误的 Worker 脚本的名称；lineno - 出现错误的行号；以及 message - 有关错误的实用说明。以下示例设置了 onerror 事件处理程序以便打印错误内容：
    
        <output id="error" style="color: red;"></output>
        <output id="result"></output>

        <script>
          function onError(e) {
            document.getElementById('error').textContent = [
              'ERROR: Line ', e.lineno, ' in ', e.filename, ': ', e.message].join('');
          }

          function onMsg(e) {
            document.getElementById('result').textContent = e.data;
          }

          var worker = new Worker('workerWithError.js');
          worker.addEventListener('message', onMsg, false);
          worker.addEventListener('error', onError, false);
          worker.postMessage(); // Start worker without a message.
        </script>
    
`workerWithError.js`：
    
        self.addEventListener('message', function(e) {
          postMessage(1/x); // Intentional error.
        };
    

# 七、适用和不适用 Worker 的功能

由于 Web Worker 的多线程行为，所以它们只能使用 JavaScript 功能的子集：

*   `navigator` 对象
*   `location` 对象（只读）
*   `XMLHttpRequest`
*   `setTimeout()/clearTimeout()` 和 `setInterval()/clearInterval()`
*   应用缓存
*   使用 `importScripts()` 方法导入外部脚本
*   生成其他 Web Worker

Worker 无法使用：

*   DOM（非线程安全）
*   `window` 对象
*   `document` 对象
*   `parent` 对象
*   `alert()`
*   `confirm()`
*   `document.getElementById()`

# 八、更多使用方向
*  预先抓取或缓存数据以便稍后使用
*  突出显示代码语法或其他实时文本格式
*  拼写检查
*  分析视频或音频数据
*  背景 I/O 或网络服务轮询
*  处理较大数组或超大 JSON 响应
*  `<canvas>` 中的图片过滤
*  更新本地网络数据库中的多行内容

# 九、一些示例
*   [Example from HTML5Rocks slides](http://slides.html5rocks.com/#web-workers)
*   [Motion tracker](http://htmlfive.appspot.com/static/tracker1.html)
*   [Simulated Annealing](http://people.mozilla.com/~prouget/demos/worker_and_simulatedannealing/index.xhtml)
*   [HTML5demos sample](http://html5demos.com/worker)

# 十、参考
[http://www.ruanyifeng.com/blog/2018/07/web-worker.html](http://www.ruanyifeng.com/blog/2018/07/web-worker.html)
[https://dev.opera.com/articles/web-workers-rise-up/](https://dev.opera.com/articles/web-workers-rise-up/)
[https://www.html5rocks.com/en/tutorials/workers/basics/](https://www.html5rocks.com/en/tutorials/workers/basics/)


