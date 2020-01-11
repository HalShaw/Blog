---

title: 深入理解JS中的事件发射器（Event Emitters）
date: 2019-08-22 20:23
tags: JavaScript Event Emitters
categories: JavaScript
---

![Event Emitter](https://upload-images.jianshu.io/upload_images/1741029-84f3e55abfbaba7a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 一、背景
先看一个`DOM`事件:
  
        const button = document.querySelector("button");

        button.addEventListener("click", (event) => /* do something with the event */)
  
以上代码在`button`上添加了一个事件监听器，每当点击`button`的时候，点击事件被触发出去并且同时调用`callback`函数。

有很多时候可能会有需要触发自定义事件的需求，不单单只是一个点击事件，假设有这样一个需要触发一个基于其他触发器的事件，并且需要有一个事件响应的，可以自定义一个`event emitter`来实现。

<!--more-->

一个`event emitter`就是监听一个`event`，触发一个回调函数，然后`emit`一个带有`value`的事件的一种模式，有时候也称为`pub/sub`模型或者监听器。

在JavaScript中的一种实现如下：
  
        let n = 0;
        const event = new EventEmitter();

        event.subscribe("THUNDER_ON_THE_MOUNTAIN", value => (n = value));

        event.emit("THUNDER_ON_THE_MOUNTAIN", 18);

        // n: 18

        event.emit("THUNDER_ON_THE_MOUNTAIN", 5);

        // n: 5
  
在上面的代码中，我们订阅了一个叫做 `THUNDER_ON_THE_MOUNTAIN`的事件，并且当事件被 ` emitted ` 的时候，回调函数 `value => (n = value)` 也会被触发，可以调用 `emit()`来`emit`该事件。

这在与异步代码交互的时候，如果有不在当前模块下的值需要更新时十分有用。

一个真实的例子就是[React Redux](https://react-redux.js.org/)，
`Redux`需要一种通知外部其内部的值已经更新的机制，其允许`React`调用`setState()`并重新渲染UI来获取哪些值已经改变，这个地方也是使用`event emitter`来实现的。
`Redux store`有一个传入一个提供新的`store`的回调函数作为参数的订阅函数，在这个订阅函数中，调用了 `React Redux`的以新`store`的值调用了`setState()`方法的 `<Provider>` 组件，可以[在此](https://github.com/reduxjs/react-redux/blob/master/src/components/Provider.js#L36)查看。

现在我们的应用有了两个不同的部分，一部分是`React UI`，另一部分是`Redux store`，谁也说不清楚事件究竟是被那一部分触发的。

# 二、实现
先看一个简单的`event emitter`，其中使用了`class`，在这个`class`中跟踪事件。

        class EventEmitter {
            public events: Events;
            constructor(events?: Events) {
                this.events = events || {};
            }
        }
  
### 事件

定义一个事件接口，用来存储一个每一个`key`都是一个事件名并且各自的值都是回调函数组成的数组的空白对象。
  
        interface Events {
          [key: string]: Function[];
        }

        {
          "event": [fn],
          "event_two": [fn]
        }
  
使用数组的原因是因为每一个事件都可能有多个`subscriber`，因为`element.addEventLister("click") `可能会被多次调用。

### 订阅

现在需要处理订阅的事件，在上面的例子中，`subscribe()`函数接收两个参数：一个`name`和一个`callback`函数。
  
        event.subscribe("named event", value => value);
  
定义一个`subscribe`方法来接收这两个参数，只需把这两个参数添加到类内部的`this.events`。

        class EventEmitter {
            public events: Events;
            constructor(events?: Events) {
                this.events = events || {};
            }

            public subscribe(name: string, cb: Function) {
                (this.events[name] || (this.events[name] = [])).push(cb);
            }
        }
  
### 发射

到此可以订阅事件了，接下来，当一个新事件发射的时候需要触发回调函数，当触发的时候，将使用`(emit("event"))`中存储的事件名和需要传递到回调函数`(emit("event", value))`的任意值，我们可以简单地传递任意参数到回调函数在第一个参数后面。

        class EventEmitter {
            public events: Events;
            constructor(events?: Events) {
                this.events = events || {};
          }

            public subscribe(name: string, cb: Function) {
                (this.events[name] || (this.events[name] = [])).push(cb);
            }

            public emit(name: string, ...args: any[]): void {
                (this.events[name] || []).forEach(fn => fn(...args));
            }
        }
  
既然我们知道了我们希望发射的事件，可以使用`this.events[name]`来查看，返回的是一个回调函数的数组。

### 取消订阅

        subscribe(name: string, cb: Function) {
            (this.events[name] || (this.events[name] = [])).push(cb);

            return {
                unsubscribe: () =>
                this.events[name] && this.events[name].splice(this.events[name].indexOf(cb) >>> 0, 1)
            };
        }
  
上述代码返回一个带有`unsubscribe`方法的对象，可以使用箭头函数`() =>`来获取传递给父对象参数的作用域，在这个函数中，使用`>>>`[操作符](https://stackoverflow.com/questions/1822350/what-is-the-javascript-operator-and-how-do-you-use-it)可以找到传递给父级回调函数的索引，在这里使用可以保证我们每次在回调函数数组上调用`splice()` 的时候总是可以取到一个真正的数字，即使`indexOf()` 都不能返回数字也行。
可以这样使用：
  
        const subscription = event.subscribe("event", value => value);

        subscription.unsubscribe();
  
到此，我们就可以取消这一个特别的订阅了，而且不影响其他的订阅。

### 完整实现
  
        interface Events {
            [key: string]: Function[];
        }

        export class EventEmitter {
            public events: Events;
            constructor(events?: Events) {
                this.events = events || {};
            }

            public subscribe(name: string, cb: Function) {
                (this.events[name] || (this.events[name] = [])).push(cb);

            return {
                unsubscribe: () =>
                    this.events[name] && this.events[name].splice(this.events[name].indexOf(cb) >>> 0, 1)
            };
        }

            public emit(name: string, ...args: any[]): void {
                (this.events[name] || []).forEach(fn => fn(...args));
            }
        }
  
### 实例
[https://codepen.io/charliewilco/pen/gEoErR](https://codepen.io/charliewilco/pen/gEoErR)

上述代码中，首先在另外一个事件回调中使用了`event emitter`，在这种情况下，一个`event emitter`是用来清除一些逻辑，在GitHub上选择一个仓库，获取详情，缓存详情，并更新DOM去显示这些详情。在订阅回调函数中从网络或者缓存中获取结果并更新，可以这样做的原因是当我们发射时间的时候从列表中给了回调函数一个随机的仓库。

现在来考虑一些不太一样的东西，在一个应用中，可能会有许多状态需要登录之后才可以触发，并且可能会有多个订阅器来处理用户试图退出的操作。因为已经发射了一个带`false`值的事件，每一个订阅器都可以使用这个值，并且需要判断是否需要重定向页面，移除cookie或者禁用表单。
  
        const events = new EventEmitter();

        events.emit("authentication", false);

        events.subscribe("authentication", isLoggedIn => {
            buttonEl.setAttribute("disabled", !isLogged);
        });

        events.subscribe("authentication", isLoggedIn => {
            window.location.replace(!isLoggedIn ? "/login" : "");
        });

        events.subscribe("authentication", isLoggedIn => {
            !isLoggedIn && cookies.remove("auth_token");
        });
  
### 最后

要让`emitters`能工作，有几点需要考虑：
- 需要在`emit()`函数中使用`forEach`或者`map`来确保我们能创建新的订阅器或者取消订阅。
- 当一个`EventEmitter`类被实例化之后，可以传递一个预定义的事件到事件接口。
- 可以不需要使用`class`，来实现，个人喜好，但是使用`class`使事件存储在哪里会更加清晰。
可以在一个函数中实现，如下：
  
        function emitter(e?: Events) {
            let events: Events = e || {};

            return {
                events,
                subscribe: (name: string, cb: Function) => {
                (events[name] || (events[name] = [])).push(cb);

                return {
                    unsubscribe: () => {
                    events[name] && events[name].splice(events[name].indexOf(cb) >>> 0, 1);
                    }
                };
                },
                emit: (name: string, ...args: any[]) => {
                    (events[name] || []).forEach(fn => fn(...args));
                }
            };
        }
  

# 参考
[https://css-tricks.com/understanding-event-emitters/](https://css-tricks.com/understanding-event-emitters/)

