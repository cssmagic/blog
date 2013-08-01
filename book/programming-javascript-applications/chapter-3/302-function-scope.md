# [译] [PJA] [302] 函数作用域

> * Original: [Function Scope](http://chimera.labs.oreilly.com/books/1234000000262/ch03.html#function_scope)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Function Scope

Variable _scope_ is the section of code in which the identifier refers to the expected value. Outside a variable's scope, the variable is undefined, or replaced by another variable with the same name. Most C-family languages have _block scope_: meaning that you can create blocks arbitrarily to contain variables. JavaScript does not have block scope. This is a common source of confusion among people who are new to JavaScript, but familiar with other languages.

JavaScript has _function scope_, instead. This has been a point of debate in the JavaScript community, and block scope has been discussed for future versions of the language.

The desire to use block scope can be a good code smell that indicates that it may be time to break a function into smaller pieces in order to encourage readability, organization, and code reuse. It's a good idea to keep functions small.

### Hoisting

_Hoisting_ is the word most commonly used to describe the illusion that all variable declarations are "hoisted" to the top of the containing function. Technically, that's not exactly how it happens, but the effect is the same.

JavaScript builds its execution environment in two passes. The declaration pass sets up the run time environment, where it scans for all variable and function declarations and creates the identifiers. The second pass is the execution pass. After the first pass, all declared functions are available, but variables are still undefined. Consider the following code:

    var x = 1;

    (function () {
      console.log(x);
      var x = 2;
    }());

If you guessed that the value of `x` at the `console.log()` statement is `1`, you're not alone. This is a common source of bugs in JavaScript. In the first pass, the function declarations occur, and `x` is `undefined` in both the inner and outer scope. When it gets to the `console.log()` statement in the execution pass, the inner scoped `x` has been declared, but is still `undefined`, because it hasn't hit the initialization in the next statement yet. In effect, this is how JavaScript interprets the code:

    var x = 1;

    (function () {
      var x; // Declarition is hoisted and x is undefined.
      console.log(x);
      x = 2; // Initialization is still down here.
    }());

Functions behave a little differently. Remember that declarations get hoisted:

    test('Function Declaration Hoisting', function () {
      function number() {
        return 1;
      }

      (function () {
        equal(number(), 2, 'Inner scope wins.');

        function number() {
          return 2;
        }
      }());

      equal(number(), 1, 'Outer scope still works.');
    });

This code is equivalent to:

    test('Function declaration hoisted.', function () {
      function number() {
        return 1;
      }

      (function () {
        function number() {
          return 2;
        }

        equal(number(), 2, 'Inner scope wins.');
      }());

      equal(number(), 1, 'Outer scope still works.');
    });

Function expressions do not share this behavior, because they do not declare a function. Instead, they declare a variable, and are subject to the same variable hoisting behavior.

    test('Function expression hoisting', function () {
      function number() {
        return 1;
      }

      (function () {
        try {
          number();
        } catch (e) {
          ok(true, 'number() is undefined.');
        }

        var number = function number() {
          return 2;
        }

        equal(number(), 2, 'number() is defined now.');
      }());

      equal(number(), 1, 'Outer scope still works.');
    });

In the declaration pass, the `number` variable is hoisted, but the function body is _not hoisted_, because it is a named function expression, not a function declaration. The value of number is not defined until run time. This code is equivalent to:

    test('Function Expression Hoisted', function () {
      function number() {
        return 1;
      }

      (function () {
        var number; // Declaration initialized to undefined.

        try {
          number();
        } catch (e) {
          ok(true, 'number() is undefined.');
        }

        number = function number() {
          return 2;
        }

        equal(number(), 2, 'number() is defined now.');
      }());

      equal(number(), 1, 'Outer scope still works.');
    });

If you declare all of your variables at the top of your function, and define your functions before you try to use them, you'll never need to worry about any of this. This practice can completely eliminate scope related bugs from your code.

### Closures

Closures are critical to successful application development.

In a nutshell, a _closure_ stores function state, even after the function has returned. To create a closure, simply define a function inside another function and return it. The inner function will have access to the variables declared in the outer function. This technique is commonly used to give objects data privacy.

Because the closure variables in the outer function are only in scope within the containing function, you can't get at the data except through its _privileged methods_. In other languages, a privileged method is an exposed method that has access to private data. In JavaScript, any exposed method defined within the closure scope is privileged. For example:

    var o = function o () {
      var data = 1,
        get;

      get = function get() {
        return data;
      };

      return {
        get: get
      };
    };

    test('Closure for object privacy.', function () {
      var obj = o(); // Get an object with the .get() method.

      try {
        ok(data, 'This throws an error.');
      } catch (e) {
        ok(true,'The data var is only available'
          %2B ' to privileged methods.');
      }

      equal(
        obj.get(), 1,
        '.get() should have access to the closure.'
      );
    });

In the above example, o is an object factory that defines the private variable `data`, and a privileged method, `.get()` that has access to it. The factory exposes `.get()` in the object literal that it returns.

In the test, the return value from `o` is assigned to the `obj` variable. In the `try` block, the attempt to access the private data `var` throws an error because it is undeclared outside the closure scope.

In addition to the data privacy benefits, closures are an essential ingredient in languages that support first class functions, because they give you access to outer scope variables from inside your lambdas.

Closures are commonly used to feed data to event handlers or callbacks, which might get triggered long after the containing function has finished. For example:

    (function () {
      var arr = [],
        count = 1,
        delay = 20,
        timer,
        complete;

      timer = function timer() {
        setTimeout(function inner() {
          arr.push(count);

          if (count &lt; 3) {
            count %2B= 1;
            timer();
          } else {
            complete();
          }
        }, delay);
      };

      asyncTest('Closure with setTimeout.', function () {
        complete = function complete() {
          equal(
            arr.join(','), '1,2,3',
            'arr should be [1,2,3]'
          );
          start();
        };

        timer();

        equal(
          arr.length, 0,
          'array should be empty until the first timout.'
        );
      });
    }());

In this example, the `inner()` lambda has access to `arr`, `complete()`, and `count` from the containing function. Its job is to add the current `count` to the array each time it's called. If the array isn't full yet, it calls the `timer()` function to set a new timeout so it will be invoked again after the delay has expired.

This is an example of asynchronous recursion, and the pattern is sometimes used to retry Ajax requests when they fail. There is usually a retry limit and a delay so that the server doesn't get hammered with endless retries from millions of users.

The `asyncTest()` function is provided by QUnit as a shortcut for running asynchronous tests. Normally, you need to call `stop()` at the top of your test to tell QUnit that you're expecting assertions to fire asynchronously. The `stop()` function suspends the completion of the test until `start()` gets called.

When the test runs, the `complete()` function gets defined. It will later be called from `inner()` after all of the timeouts have expired. The complete function defines an `equal()` assertion to verify the contents of `arr`.

As you can see, the final `equal()` assertion in the program listing is actually the first one that gets run. That's because the first timeout has not had time to expire before the JavaScript engine gets to that line in the code. At that point, any attempt to read `arr` or `count` will return the initial values. The values don't get modified until the first timeout expires, and `inner()` has had a chance to run.

Each time `inner()` gets called, `count` is incremented, and pushed onto the array, `arr`. Since `count` and `arr` were defined inside the closure, it's possible to access them from other functions in the same scope, which is why we can test them in the `asyncTest()` call.

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
