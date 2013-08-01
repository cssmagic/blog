# [译] [PJA] [305] 异步操作

> * Original: [Asynchronous Operations](http://chimera.labs.oreilly.com/books/1234000000262/ch03.html#asynchronous_operations)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Asynchronous Operations

_Asynchronous operations_ are operations that happen outside the linear flow of program execution. Normally, the JavaScript engine will execute code line by line, in order from top to bottom, following the normal flow of your program (such as function calls, conditional logic, etc...).

Asynchronous operations are broken up into two phases: call and response. By definition, it's impossible to know at what point in the program flow you'll be when you receive an asynchronous response. There are a couple of popular ways to manage that uncertainty.

### Callbacks

_Callbacks_ are functions that you pass as arguments to be invoked when the callee has finished its job. Callbacks are commonly passed into event handlers, Ajax requests, and timers. You should already be familiar with passing callbacks to event listeners and timers:

    var $button = $('Click')
      .appendTo('body');

    asyncTest('Async callback event listener.', function () {
      $button.on('click', function clicked() {
        ok(true, 'Button clicked.');
        start();
      });

      setTimeout(function timedOut() {
        $button.click();
        $button.remove();
      }, 20);
    });

In the above code, the `clicked()` callback gets passed into into jQuery's `.on()` method. When `$button` receives a `click` event, it invokes `clicked()` which runs the `ok()` assertion and then `start()`, which tells QUnit that it's finished waiting for asynchronous operations, so it can continue to run tests.

Next, the `timedOut()` callback is passed into `setTimeout()`, which triggers the `click` event on `$button` and removes the button from the DOM.

Callbacks work great when you're only waiting for one operation at a time, or when you only have one job to do when the response comes back, but what if you need to manage multiple asynchronous dependencies, or you have several unrelated tasks waiting on the same data (such as a provider authorization)? That's where promises can be very useful.

### Promises and Deferreds

_Promises_ are objects that allow you to add callback functions to success or failure queues. Instead of calling a callback function in response to the completion of an asynchronous (or synchronous) operation, you return a promise which allows you to register any number of callbacks.

The promise provides access to the state of the operation: whether it's waiting or finished, and in some cases, what the progress is. You can add callbacks to a promise at any time, which will trigger after the operation is complete and the promise is resolved. If the promise has already resolved, the callback will be invoked immediately.

Promises were popularized in JavaScript by jQuery, inspired by the CommonJS Promises/A design. jQuery uses them internally to manage asynchronous operations, such as Ajax. In fact, as of jQuery 1.5, all jQuery Ajax methods return a promise. Here's how they work:

    var whenDataFetched = $.getJSON(
      'https://graph.facebook.com/jsapplications'
    );

    asyncTest('Ajax promise API', function () {
      whenDataFetched
        .done(function (response) {
          ok(response,
            'The server returned data.');
          start();
        })
        .fail(function () {
          ok(true,
            'There was an error.');
          start();
        });
    });

The example above demonstrates an Ajax request to get the page metadata for the "Programming JavaScript Applications" page. Since all of jQuery's Ajax helper functions return a promise, you can add a success callback with `whenDataFetched.done()`.

The difference between a promise and a callback is that a promise is an object that gets returned from the callee, instead of a function that gets passed into and invoked by the callee. With promises, it's much easier to add additional callbacks if you need them, and to isolate those callbacks from each other so that the callback code can be organized independently of the initiating call.

A _deferred_ is the object that controls the promise, with a few extra methods. jQuery's `.Deferred()` returns a new object that does everything a promise does, but also provides `.resolve()` and `.reject()` methods that trigger the corresponding callbacks. For example, say you want to make `setTimeout()` a little more flexible, so that you can add callbacks to it at any time, and break the need to attach a callback at the timer start time. You could implement it like this:

    var timer = function timer(delay) {
      var whenTimedOut = $.Deferred(),
        promise = whenTimedOut.promise();

      promise.cancel = function (payload) {
        whenTimedOut.reject(payload);
      };

      setTimeout(function () {
        whenTimedOut.resolve();
      }, delay);

      return promise;
    };

    asyncTest('Deferred', function () {
      var startTime = new Date(),
        delay = 30,
        afterTimeout = 50,
        cancelTime = 100,
        myTimer = timer(delay),
        cancelTimer = timer(cancelTime);

      expect(4);

      myTimer.done(function () {
        ok(true,
          'First callback fired.');
      });

      myTimer.done(function () {
        var now = new Date();
        ok((now - startTime) &gt; delay,
          'Delay works.'
          );
      });

      setTimeout(function () {
        ok(true,
          'Fires after timeout expires.');
      }, afterTimeout);

      setTimeout(function () {
        start();
      }, afterTimeout %2B 20);

      cancelTimer
        .done(function () {
          ok(false,
            'A canceled timer should NOT run .done().');
        })
        .fail(function () {
          ok(true,
            'A canceled timer calls .fail().');
        })
        .cancel();
    });

Promises really shine when you need to orchestrate a complex sequence of events. It's sometimes necessary to gather data from multiple sources prior to getting data from yet another source.

With callbacks, that can be complicated. With promises, it's easy to wait for any number of promises to resolve before moving on to the next step. Using the `timer()` function defined above:

    var a = timer(60),
      b = timer(70),
      c = timer(40),
      tasks = [a, b, c];

    asyncTest('Multiple dependencies.', function () {
      $.when(a, b, c).done(function () {
        ok(true, 'Runs when all promises resolve');
        start();
      });
    });

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
