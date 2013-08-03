# [译] [PJA] [502] 接口

> * Original: [Interfaces - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#interfaces)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Interfaces

## 接口

> "Program to an interface, not an implementation."

> \--The Gang of Four, "Design Patterns"

> 

> ——“四人帮”，《设计模式》

_Interfaces_ are one of the primary tools of modular software design. Interfaces define a contract that an implementing module will fulfill. For instance, a common problem in JavaScript applications is that the application stops functioning if the Internet connection is lost. In order to solve that problem, you could use local storage and sync changes periodically with the server. Unfortunately, some browsers don't support local storage, so you may have to fall back to cookies or even flash (depending on how much data you need to store).

*接口* 是模块化软件设计的一个主要工具。接口定义了一个约定，约定一个完整模块所能实现的功能。我们以实例来说明，JavaScript 应用程序有一个常见问题，一旦互联网连接断开之后，应用程序就停止工作了。为了解决这个问题，你可能会使用本地存储（localStorage）特性，并且定期向服务器同步数据更新。不幸的是，部分浏览器并不支持本地存储，所你可能不得不降级到 Cookie 或 Flash 的存储功能（这取决于你需要保存的数据量）。

That will be difficult if your business logic depends directly on ~~your~~ the localStorage features. For example, in *Figure 5-1*:

如果你的业务逻辑直接依赖于本地存储特性，这就比较困难。以 *图 5-1* 为例：

![Direct Dependency][6]

Figure 5-1. Direct Dependency

图 5-1. 直接依赖

A better alternative is to create a standard interface to provide data access for the post module. That way, the module can retrieve data using the same interface, regardless of where the data is being stored. See *Figure 5-2*:

一个更好的替代方法是创建一个标准的接口，来为数据发送（post）模块提供数据访问渠道。这样，模块可以使用相同的接口获取数据，而不用操心数据实际上保存在哪里。参见 *图 5-2*：

![Interface][8]

Figure 5-2. Interface

图 5-2. 接口

Other languages have native support for interfaces which may enforce the requirements of an interface. You might know them as abstract base classes, or pure virtual functions. In JavaScript, there is no distinction between a class, interface, or object instance. There are only object instances, and that simplification is a good thing. You may be wondering, if there's no native support for interfaces in JavaScript, why bother to write one at all?

其它语言对接口特性提供了原生支持，可以指定一个接口的依赖条件。你可能知道这种支持称作抽象基类，或纯虚函数。在 JavaScript 中，在一个类、接口、对象实例之间并没有区别。它们都只是对象实例，而且这种简化也是一种好事。你可能会感到奇怪，既然 JavaScript 并没有对接口特性提供原生支持，我们又何必要写一个出来呢？

When you need multiple implementations of the same interface, it's good to have a canonical reference in the code that explicitly spells out exactly what that interface is. It's important to write code that is self-documenting. When it's time to add another concrete instance, simply call a predefined factory function and pass in the methods you need to override.

当你需要为相同的接口编写多个实现时，在代码中制定一个严格的参照、明确地拼写出接口到底是什么，是一个良好的实践。编写可以自我描述的代码是很重要的。当你需要增加另一个具体实例时，只需要简单地调用一个预定义的工厂函数并传入你需要覆盖的方法就可以了。（译法：本节提到的“实现”指的是对接口的实现。可以为相同的接口编写不同的实现，比如……）

For example, using `O.js` to define the factory:

比如说，我们使用 `O.js` 来定义工厂函数：

```js
(function (exports, o) {
  'use strict';

  // Make sure local storage is supported.
  // 确认是否支持本地存储。
  var ns = 'post',
    supportsLocalStorage =
      (typeof localStorage !== 'undefined')
        &amp;&amp; localStorage !== null,

    stream,

    // Define the stream interface.
    // 定义数据流（stream）接口。
    streamInterface = o.factory({
      sharedProperties: {
        save: function saveStream() {
          // Save the post stream.
        }
      }
    }),

    localStorageProvider = streamInterface({
      save: function saveStreamLocal() {
        localStorage.stream = JSON.stringify(stream);
      }
    }),

    cookieProvider = streamInterface({
      save: function saveStreamCookie() {
        $.cookie('stream', JSON.stringify(stream));
      }
    }),

    // Define the post interface.
    // 定义数据发送（post）接口。
    post = o.factory({
      sharedProperties: {
        save: function save() {
          stream[this.id] = this.data;
          stream.save();
          return this;
        },
        set: function set(name, value) {
          this.data[name] = value;
          return this;
        }
      },
      defaultProperties: {
        data: {
          message: '',
          published: false
        }
      },
      initFunction: function initFunction() {
        this.id = generateUUID();
        return this;
      }
    }),

    api = post;

  // If localStorage is supported, use it.
  // 如果支持本地存储，就用。
  stream = (supportsLocalStorage)
    ? localStorageProvider
    : cookieProvider; // Fall back on cookies.
                      // 否则就降级到 Cookie。

  exports[ns] = api;

}((typeof exports === 'undefined')
    ? window
    : exports,
  odotjs
));

$(function () {
  'use strict';

  var myPost = post().set('message', 'Hello, world!');

  test('Interface example', function () {
    var storedMessage,
      stream;

    myPost.save();
    stream = JSON.parse(localStorage.stream);
    storedMessage = stream[myPost.id].message;

    equal(storedMessage, 'Hello, world!',
      '.save() method should save post.');
  });
});
```

The important part here is the stream interface. First you create the factory (using `O.js` in this case, but it can be any function that returns an object that demonstrates the interface). This example has only one method: `.save()`

这里有一个重要部分是数据流（stream）接口。首先你创建了工厂函数（在这个例子中使用了 `O.js`，但也可以是任何可以返回对象的函数，并且这个对象可以演示接口）。这个例子中只有一个方法：`.save()`

```js
        // Define the stream interface.
        streamInterface = o.factory({
          sharedProperties: {
            save: function saveStream() {
              // Save the post stream.
            }
          }
        }),
```

Create a concrete implementation by calling the factory function, and passing in the concrete methods. If the interface is especially large, you might want to put each implementation in a separate file. In this case, it's a single one-line method. Notice that the concrete implementations are using named function expressions. During debugging, you'll be able to see which concrete implementation you're using by looking at the function name in the call stack:

通过调用工厂函数来创建一个具体实现，然后把具体方法传进去。如果接口的代码特别大，你可能会想把每个实现分别放进独立的文件中。在这种情况下，每种实现就是一个单个的单行方法。请注意这些具体实现都在使用具名的函数表达式。这样在调试期间，你可以通过观察调用堆栈中的函数名来判断你正在使用哪种具体实现：

```js
        localStorageProvider = streamInterface({
          save: function saveStreamLocal() {
            localStorage.stream = JSON.stringify(stream);
          }
        }),

        cookieProvider = streamInterface({
          save: function saveStreamCookie() {
            $.cookie('stream', JSON.stringify(stream));
          }
        }),
```

The final step is to decide which implementation to use:

最后一步决定了要使用哪种实现：

```js
      // If localStorage is supported, use it.
      stream = (supportsLocalStorage)
        ? localStorageProvider
        : cookieProvider; // Fall back on cookies.
```

There are alternative ways to define interfaces in JavaScript. For example, if your concrete implementations are large or resource intensive you may not want to instantiate them until you know they're needed. One way is to move the calls to the factory into the ternary expression. Another is to turn the concrete definitions into factories themselves, and only call the factory corresponding to the implementation you need.

在 JavaScript 中，还有一些候选方法可以定义接口。比如说，如果你的具体接口代码量很大，或者需要消耗大量资源，在你真正用到它们之前，你可能都不想把它们实例化。一种方法就是把对工厂函数的调用移到三元表达式中。另一种方法是将具体定义转变为工厂函数本身，然后只调用与你需要的实现相关的工厂函数。

You could also define the concrete implementations as prototype objects, and then pass the appropriate prototype into O.js or `Object.create()` during the final step. I prefer the factory approach, because it gives you a lot of flexibility.

你也可以将具体实现定义为原型对象，然后在最后一步将合适的原型传递给 `O.js` 或 `Object.create()`  方法。我更倾向于使用工厂函数方法，因为这种方法赋予你更多的灵活性。

Erich Gamma, one of the Gang of Four authors who created "Design Patterns", shared some interesting thoughts about interfaces in an interview with Bill Venners: ["Leading-Edge Java Design Principles from Design Patterns: A Conversation with Erich Gamma, Part III"][9].

Erich Gamma，“四人帮”中创造了“设计模式”概念的那位，在接受 Bill Venners 采访时，分享了一些关于接口的有趣想法：[“源自《设计模式》的前沿 Java 设计原则：与 Erich Gamma 对话（第三部分）”][9]。

[6]: http://orm-chimera-prod.s3.amazonaws.com/1234000000262/figs/0501.png
[8]: http://orm-chimera-prod.s3.amazonaws.com/1234000000262/figs/0502.png
[9]: http://www.artima.com/lejava/articles/designprinciples.html

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
