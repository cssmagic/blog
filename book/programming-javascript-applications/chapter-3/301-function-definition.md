# [译] [PJA] [301] 函数定义

> * Original: [Function Definition](http://chimera.labs.oreilly.com/books/1234000000262/ch03.html#function_definition)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Function Definition

There are several ways to define functions in JavaScript. Each has its own advantages and disadvantages.

    function foo() {

        /* Warning: arguments.callee is deprecated.
           Use with caution. Used here strictly for
           illustration. */

        return arguments.callee;
    }

    foo(); //=&gt; [Function: foo]

In the code above, `foo()` is a _function declaration_. As mentioned in the section on hoisting, it's important to be aware that you can't declare a function conditionally. For example, the following code will fail:

    var score = 6;

    if (score &gt; 5) {
      function grade() {
        return 'pass';
      }
    } else {
      function grade() {
        return 'fail';
      }
    }

    module('Pass or Fail');

    test('Conditional function declaration.', function () {
      // Firefox: Pass
      // Chrome, Safari, IE, Opera: Fail
      equal(
        grade(), 'pass',
        'Grade should pass.'
      );
    });

What's worse, this pattern fails inconsistently across browsers. It's best to avoid conditional function declarations entirely. For more detail, refer to the section on function hoisting.

Function declaration tends to encourage large piles of loosely related functions to grow in your module, with no real hints about what goes where, whether its public or private, or how the functions work together.

    var bar = function () {
        return arguments.callee;
    };

    bar(); //=&gt; [Function] (Note: It's anonymous.)

The `bar()` example assigns a function body to the variable, `bar`. This implementation is called a _function expression_.

The advantage of function expressions is you can assign functions to variables the same way you would assign values to variables. You can count on function expressions to follow your application logic reliably. If you want to do a conditional assignment, it will work as expected.

The disadvantage is function expressions create _anonymous functions_. Anonymous functions are used in JavaScript frequently. With reckless abandon, perhaps. Imagine that you've declared all of your functions this way, and you have a pile of functions that call functions that call even more functions. This is a common scenario in a well architected, event-driven application. Now imagine that you're twelve function calls deep and something goes wrong. You need to debug and view your call stack, but it looks something like this:

    (Anonymous function)
    (Anonymous function)
    (Anonymous function)
    (Anonymous function)
    (Anonymous function)
    (Anonymous function)
    (Anonymous function)
    (Anonymous function)
    (Anonymous function)
    (Anonymous function)
    (Anonymous function)
    (Anonymous function)

Obviously, this is not very helpful.

    var baz = {
        f: function () {
            return arguments.callee;
        }
    };

    baz.f(); // =&gt; [Function] (Note: Also anonymous.)

The `baz` example exhibits the same behavior. It's another anonymous function assigned to a property in an object literal. Function expressions assigned to object literals are sometimes called _method literals_. Methods are functions attached to objects.

The advantage is that method literals make it very easy to group related functions using object literals. For example, say you have a group of functions that control the state of a lightbulb:

    var lightBulbAPI = {
            toggle: function () {},
            getState: function () {},
            off: function () {},
            on: function () {},
            blink: function () {}
        };

You gain a lot when you group related functions together. Your code is more organized and readable. Code in context is easier to understand and maintain.

Another advantage is that you can more easily rearrange code if you notice that your module is growing too large. For example, if your smart house module is too bulky with APIs for lightbulbs, TV, music, and the garage door, and they're all grouped like this, it's much easier to split them into individual modules in separate files.

Do not use the `Function()` constructor to declare a function. Passing it a string is equivalent to passing a string to `eval()`. It comes with the same drawbacks and security implications discussed in chapter two.

### Named Function Expressions

As you can see, all of the function definition techniques have weaknesses, but it's possible to get the benefits of code organization and conditional function definition without littering your stack traces with anonymous functions. Let's take another look at the lightbulb API:

    lightbulbAPI = {
      toggle: function toggle() {},
      getState: function getState() {},
      off: function off() {},
      on: function on() {},
      blink: function blink() {}
    };

Named function expressions are like anonymous function expressions in every way, except that they have a name that you can use from inside the function (for recursion). That name also conveniently appears in the function call stack.

As with anonymous function expressions, you can use them anywhere you would use a variable -- not just inside method literals. Named function expressions are _not_ the same as function declarations. Unlike function declarations, the name you assign is only available from within the function (covered in more detail in the section on function scope). From outside the function, you must access the function through the variable it's assigned to or the parameter it's passed in on:

    test('Named function expressions.', function () {
      var a = function x () {
        ok(x, 'x() is usable inside the function.');
      };

      a();

      try {
        x(); // Error
      } catch (e) {
        ok(true, 'x() is undefined outside the function.');
      }
    });

Internet Explorer 8 and older treat named function expressions like function declarations, so be careful that the names you chose won't collide with the names of other functions or variables in the same scope. This bug is fixed in IE9. All other major browsers treat named function expressions correctly.

If you name your function expressions the same as the variable you assign them to, and declare all of your variables at the top of your function, this will rarely be a problem.

    test('Function Scope', function () {
      var testDeclaration = false,
        foo;

      // This function gets erroneously overridden in IE8.
      function bar(arg1, bleed) {
        if (bleed) {

          ok(false,
           'Declaration bar() should NOT be callable from'
           %2B ' inside the expression.');

        } else {

          ok(true,
            'Declaration bar() should be called outside the'
            %2B ' expression.');

        }
        testDeclaration = true;
      }

      foo = function bar(declaration, recurse) {
        if (recurse) {

          ok(true,
            'Expression bar() should support scope safe'
            %2B ' recursion');

        } else if (declaration === true) {

          ok(true,
            'Expression bar() should be callable via foo()');
            bar(false, true);

        } else {

          // Fails in IE8 and older
          ok(false,
          'Expression bar() should NOT be callable outside'
          %2B ' the expression');

        }
      };

      bar();
      foo(true);

      // Fails in IE8 and older
      ok(testDeclaration,
        'The bar() declaration should NOT get overridden by'
        %2B ' the expression bar()');
    });

### Lambdas

A _lambda_ is a function that is used as data. As such, they can be used the same way any other expression can: as a parameter for another function, the return value of a function, or anywhere you might use a literal value.

For example:

    var sum = function sum() {
      var result = 0;

      [5, 5, 5].forEach(**function addTo(number) { result %2B= number; }**);

      return result;
    };

    test('Lambdas.', function () {
      equal(
        sum(), 15,
        'result should be 15.'
      );
    });

The `.addTo()` function passed into `.forEach()` is a lambda. The `.forEach()`method calls `.addTo()` for each number in the array. Note that `.addTo()` has access to the `result` variable from the containing function scope's closure (covered in more detail in the section on closures). The `.forEach()` method is one of several _functional enumerators_ added to JavaScript in the ECMAScript 5 specification. More detail on that can be found in the Functional Programming section.

In JavaScript, lambdas are commonly used to:

  * Perform operations on the other arguments passed in. (As demonstrated above).

  * Attach event handlers for DOM interactions.

  * Pass in a _callback function_ to be executed when the current function is complete.

  * Wrap existing functions with additional functionality (often used to implement cross-cutting concerns, such as logging). A function that adds functionality to another function is called a _function decorator_.

  * Take a function that requires multiple parameters, and return a function that requires fewer parameters, (for example, by fixing one or more of the parameters to specific values). (See Partial Application)

  * Return a function from another function. For example, you might have a function that takes an argument, and returns a curried function that applies that argument in a predetermined calculation. (See Function Currying)

Lambdas are frequently confused with anonymous functions, closures, first class functions, and higher order functions. The concepts are all similar, but they mean different things.

Some languages use a character (such as "`\`"), or the keyword, `lambda` to denote lambdas and leave off the function name. Don't let that fool you. Function anonymity is merely syntactical sugar for lambdas, designed to make them less verbose and easier to work with. The important point is that lambdas are treated like data that can be passed around as inputs and outputs between other functions, regardless of whether or not they are named.

Closure refers to the mechanism that exposes variables in the containing scope to access from nested functions (lambdas), even after the containing function has returned. Functions passed as arguments or return values create closures, and it is a common to confuse the words "closure" and "lambda" as synonyms. That is not accurate. The word "closure" refers to the mechanism that allows access to variables from the containing scope, not to the nested functions. Some languages support lambdas, but do not support closures.

All functions in JavaScript are first class, meaning that you can use them anywhere you would use a value, so it's possible to create a first class function that is not also a lambda. You can pass any function as data, but when we talk about lambdas in JavaScript, we're talking about actually taking advantage of that capability by treating the function like a value.

Higher order functions are functions that consume or return functions as data. Lambdas get passed to and/or returned from higher order functions, and a function might be both a lambda and a higher order function, but not all higher order functions are lambdas.

If a function is used as an argument or return value, it's a lambda.

#### Lambda Calculus

To add to the confusion, the mathematical inspiration for lambdas (lambda calculus) has special rules that don't apply to JavaScript. For example, in lambda calculus, a lambda can't have more than one argument. Multi-argument operations are computed by fixing an argument and returning a new function that takes the next argument, which is also fixed and returned in a function that takes the next argument and so on. Lambda calculus takes this concept so far that there is even a system for representing numerals using layers of nested lambda expressions (called Church encoding after its discovery by Alonzo Church, the formulator of lambda calculus).

While similar behavior is sometimes useful in JavaScript, it's hardly efficient, and rarely required. In short, the definition of lambdas from lambda calculus is a little too limited to apply directly to practical, real-world JavaScript applications.

What's really interesting about Lambda calculus is that it has been proved computationally equivalent to a Turing machine: meaning that if an algorithm exists to complete a calculation, that algorithm can be expressed using lambdas.

Lambda calculus was the inspiration for Lisp, a language virtually made up of pure lambda expressions. Interestingly, the expressive power of lambdas makes it very easy to define your own domain specific language inside your Lisp code -- and you can do similar things with JavaScript (see Fluent APIs).

### Immediately Invoked Function Expressions

It's possible in JavaScript to immediately invoke a function as soon as it's defined. A popular name for the technique is a _self invoked anonymous function_. That name is not accurate because it incorrectly implies that the function is recursive. Ben Alman posted a better suggestion on his blog: _Immediately Invoked Function Expression_ (IIFE, pronounced _"iffy"_). The name is a lot more fun to say in the abbreviated form, and clearly describes what it is. Thankfully, the name IIFE seems to be taking root.

This technique is often used to create a new scope to encapsulate modules. jQuery uses IIFEs to isolate its variables from the global scope. Before the IIFE became popular, a common technique was to assign names to the object prototype:

    var Lightbulb = function () {
        this.isOn = false;
      },
      lightbulb = new Lightbulb();

    Lightbulb.prototype.toggle = function () {
      this.isOn = !this.isOn;
      return this.isOn;
    };

    Lightbulb.prototype.getState = function getState() {
      // Implementation...
    };

    Lightbulb.prototype.off = function off() {
      // Implementation...
    };

    Lightbulb.prototype.on = function on() {
      // Implementation...
    };

    Lightbulb.prototype.blink = function blink() {
      // Implementation...
    };

    test('Prototypes without IIFE.', function () {
      equal(lightbulb.toggle(), true, 'Lightbulb turns on.');
      equal(lightbulb.toggle(), false, 'Lightbulb turns off.');
    });

As you can see, this method leads to a lot of repetition as you have to specifically address `lightbulb.prototype` for every property definition. The IIFE lets you encapsulate scope, so you can assign to regular variables, instead of just the prototype. This gives you more flexibility, and the ability to hide state inside the function closure:

    (function () {
      var isOn = false,
        toggle = function toggle() {
          isOn = !isOn;
          return isOn;
        },
        getState = function getState() {
          // Implementation...
        },
        off = function off() {
          // Implementation...
        },
        on = function on() {
          // Implementation...
        },
        blink = function blink() {
          // Implementation...
        },

        lightbulb = {
          toggle: toggle,
          getState: getState,
          off: off,
          on: on,
          blink: blink
        };

      test('Prototypes with IIFE.', function () {
        equal(lightbulb.toggle(), true, 'Lightbulb turns on.');
        equal(lightbulb.toggle(), false, 'Lightbulb turns off.');
      });
    }());

### Method Context

Functions are invoked by appending parentheses to the end of the function reference. For these examples, we'll use a slightly altered `highPass()` function:

    function highPass(number, cutoff) {
        cutoff = cutoff || this.cutoff;
        return (number &gt;= cutoff);
    }

    var filter1 = {
            highPass: highPass,
            cutoff: 5
        },
        filter2 = {
            // No highPass here!
            cutoff: 3
        };

The `highPass()` function takes one required parameter for the `number` to be tested, and one optional parameter for the `cutoff`. If the optional parameter is not supplied, the function assumes that it's being called as a method of a valid filter object, and uses the `cutoff` property of the object, instead.

Function invocation is simple:

    test('Invoking a function.', function () {
        var result = highPass(6, 5);

        equal(
            result, true,
            '6 &gt; 5 should be true.'
        );
    });

Unless you use _method invocation_ (_dot notation_ or _square bracket notation_), `this` generally refers to the global object. Assignments to properties on `this` will pollute the global namespace. It's better to make sure you have a valid object before trying to use `this` in your function if you expect it might be invoked on its own.

Method invocation applies the function to the object to which it is attached. It takes the form, `object.methodName()` (dot notation) or `object['methodName']` (square bracket notation).

    test('Invoking a method.', function () {
      var result1 = filter1.highPass(3),
        result2 = highPass.call(filter2, 3),
        result3 = filter1.highPass(6);

      equal(
        result1, false,
        '3 &gt;= filter1.cutoff should be false.'
      );

      equal(
        result2, true,
        '3 &gt;= filter2.cutoff should be true.'
      );

      equal(
        result3, true,
        '6 &gt;= filter1.cutoff should be true.'
      );
    });

When you invoke a method with dot notation, you have access to the object's properties using `this`. The `number` parameter is compared to `filter1.cutoff`. The method returns `false` because `3` is less than the value stored in `this.cutoff`, which refers to `filter1.cutoff`. Remember, `this` refers to the object that the method is called on.

In the second example, the `call` method (inherited from `Function.prototype`) delegates to the method on `filter2`, instead. Because filter2.cutoff is 3 instead of 5, the same test passes this time.

To clarify, the `.call()` method shared by all functions allows you to call any method or function on any object. In other words, it sets `this` inside the method to refer to the object of your choosing. The signature is:

    someMethod.call(_context, argument1, argument2, ..._);

Where `context` is the object you want `this` to refer to. If you need to pass an array of arguments, use apply, instead:

    someMethad.apply(_context, someArray_);

#### Function.prototype.bind()

As useful as `.call()` and `.apply()` can be, they have one serious drawback: They impermanently bind the context to the target method. You have to remember to use them every time you invoke the method, and you have to have a access to the context object in scope. That's not always easy, particularly in event handlers.

The `.bind()` method is used to permanently set the value of `this` inside the target function to the passed in context object. `The .bind()` method is a recent addition to the language. It was first popularized by Prototype and adopted in many other libraries, and was standardized in ECMAScript 5. If you want to use it in older browsers, you'll need to shim it or use one of many available library implementations.

Let's take a look at a common use case for bind. An event handler:

    var lightbulb = {
        toggle: function toggle() {
          this.isOn = !this.isOn;
          return this.isOn;
        },
        isOn: false
      },
      toggle = lightbulb.toggle.bind(lightbulb),
      lightswitch = document.getElementById('lightswitch');

    lightswitch = document.getElementById('lightswitch');
    lightswitch.addEventListener('click', toggle, false);

Glancing over this code, it looks simple enough. An event listener gets attached to the lightswitch DOM with `.addEventListener()`. There's just one problem: This code will fail, because the context inside an event listener is not the object that the method was assigned to at design time. Instead, it's a reference to the element that was clicked.

Even after you click the switch element, `lightbulb.isOn` will be `false`. You can fix this mess with `.bind()`. You only need to alter the toggle assignment:

    toggle = lightbulb.toggle.bind(lightbulb);

Now, when the user clicks the lightswitch, the lightbulb will turn on or off as expected.

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
