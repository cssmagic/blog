# [译] [PJA] [502] 接口

> * Original: [Interfaces - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#interfaces)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Interfaces

&nbsp;

"Program to an interface, not an implementation."

&nbsp;

&nbsp;
\--The Gang of Four, "Design Patterns"

_Interfaces_ are one of the primary tools of modular software design. Interfaces define a contract that an implementing module will fulfill. For instance, a common problem in JavaScript applications is that the application stops functioning if the Internet connection is lost. In order to solve that problem, you could use local storage and sync changes periodically with the server. Unfortunately, some browsers don't support local storage, so you may have to fall back to cookies or even flash (depending on how much data you need to store).

That will be difficult if your business logic depends directly on your the localStorage features. For example, in [Figure&nbsp;5-1][5]:

![Direct Dependency][6]

Figure&nbsp;5-1.&nbsp;Direct Dependency

A better alternative is to create a standard interface to provide data access for the post module. That way, the module can retrieve data using the same interface, regardless of where the data is being stored. See [Figure&nbsp;5-2][7]:

![Interface][8]

Figure&nbsp;5-2.&nbsp;Interface

Other languages have native support for interfaces which may enforce the requirements of an interface. You might know them as abstract base classes, or pure virtual functions. In JavaScript, there is no distinction between a class, interface, or object instance. There are only object instances, and that simplification is a good thing. You may be wondering, if there's no native support for interfaces in JavaScript, why bother to write one at all?

When you need multiple implementations of the same interface, it's good to have a canonical reference in the code that explicitly spells out exactly what that interface is. It's important to write code that is self-documenting. When it's time to add another concrete instance, simply call a predefined factory function and pass in the methods you need to override.

For example, using O.js to define the factory:

    (function (exports, o) {
      'use strict';

      // Make sure local storage is supported.
      var ns = 'post',
        supportsLocalStorage =
          (typeof localStorage !== 'undefined')
            &amp;&amp; localStorage !== null,

        stream,

        // Define the stream interface.
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
      stream = (supportsLocalStorage)
        ? localStorageProvider
        : cookieProvider; // Fall back on cookies.

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

The important part here is the stream interface. First you create the factory (using O.js in this case, but it can be any function that returns an object that demonstrates the interface). This example has only one method: `.save()`

        // Define the stream interface.
        streamInterface = o.factory({
          sharedProperties: {
            save: function saveStream() {
              // Save the post stream.
            }
          }
        }),

Create a concrete implementation by calling the factory function, and passing in the concrete methods. If the interface is especially large, you might want to put each implementation in a separate file. In this case, it's a single one-line method. Notice that the concrete implementations are using named function expressions. During debugging, you'll be able to see which concrete implementation you're using by looking at the function name in the call stack:

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

The final step is to decide which implementation to use:

      // If localStorage is supported, use it.
      stream = (supportsLocalStorage)
        ? localStorageProvider
        : cookieProvider; // Fall back on cookies.

There are alternative ways to define interfaces in JavaScript. For example, if your concrete implementations are large or resource intensive you may not want to instantiate them until you know they're needed. One way is to move the calls to the factory into the ternary expression. Another is to turn the concrete definitions into factories themselves, and only call the factory corresponding to the implementation you need.

You could also define the concrete implementations as prototype objects, and then pass the appropriate prototype into O.js or `Object.create()` during the final step. I prefer the factory approach, because it gives you a lot of flexibility.

Erich Gamma, one of the Gang of Four authors who created "Design Patterns", shared some interesting thoughts about interfaces in an interview with Bill Venners: ["Leading-Edge Java Design Principles from Design Patterns: A Conversation with Erich Gamma, Part III"][9].

[5]: http://chimera.labs.oreilly.com/ch05.html#fig0501 (Figure&nbsp;5-1.&nbsp;Direct Dependency)
[6]: http://orm-chimera-prod.s3.amazonaws.com/1234000000262/figs/0501.png
[7]: http://chimera.labs.oreilly.com/ch05.html#fig0502 (Figure&nbsp;5-2.&nbsp;Interface)
[8]: http://orm-chimera-prod.s3.amazonaws.com/1234000000262/figs/0502.png
[9]: http://www.artima.com/lejava/articles/designprinciples.html

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
