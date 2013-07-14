# [译] 博文标题

> * Original: [XXXXXXXXXXXXXXXXXXXX](xxxxxxxxxxxxxxxxxxx)
> * Translated by: [cssmagic](https://github.com/cssmagic)



# Chapter&nbsp;3.&nbsp;Functions

Functions are the building blocks of applications. They are particularly important in JavaScript because JavaScript supports first class functions, functions as objects, run time function definition, and so on. It's important to have a thorough understanding of how functions work in JavaScript so you can leverage them to full advantage in your applications.

Here are some guidelines that will help you write better functions:

Don't Repeat Yourself (DRY)

Good programmers are both lazy, and very productive. They express a lot of functionality in very little code. Once you have established a pattern for something that gets repeated again in the code, it's time to write a function, object, or module that encapsulates that pattern so that it can be easily reused.

Doing so also quarantines that functionality to a single spot in the code base, so that if you later find something wrong with the code or the algorithm, you only have to fix it in one place.

Writing a reusable function also forces you to isolate the pattern from the problem, which helps you keep related functionality grouped together.

Do One Thing (DOT)

Each function should do only one thing, and do that one thing as well as it can. Following this principle will make your function more reusable, more readable, and easier to debug.

Keep it Simple Stupid (KISS)

Programmers are often tempted to come up with clever solutions to problems. That's a good thing, of course, but sometimes programmers are _too clever,_ and the solutions are cryptic. This tends to happen when a single line of code is used to accomplish more than a single atomic goal.

Less is More

In order to aid readability, and reduce the temptation to do more than one thing, functions should be as short as possible: Just enough code to do the one thing they were made to do, and no more. In most cases, functions should be just a handful of lines long. If they run much longer, consider breaking out sub tasks and data into separate functions and objects.

Minimize Side Effects

There are two classes of bugs that are extremely common and easily avoidable. The first is syntax errors (covered in the section on linting). The second is unintentional side effects.

Unintentional side effects are the bane of code reuse. They occur when multiple functions depend on and manipulate the values of the same variables or object properties. In this situation, it becomes much more difficult to refactor code, because your functions assume too much about the state of the program. For example, imagine your app includes a shopping cart, and your users can save cart contents between sessions.

Now the user wants to change the order _for the current session, only:_


    test('Order WITH unintentional side effect.', function () {
      var cartProto = {
          items: [],

          addItem: function addItem(item) {
            this.items.push(item);
          }
        },

        createCart = function (items) {
          var cart = Object.create(cartProto);
          cart.items = items;
          return cart;
        },

        // Hydrate cart with stored items.
        savedCart = createCart(["apple", "pear", "orange"]),

        session = {
          get: function get() {
            return this.cart;
          },

          // Grab the saved cart.
          cart: createCart(savedCart.items)
        };

      // addItem gets triggered by an event handler somewhere:
      session.cart.addItem('grapefruit');

      ok(session.cart.items.indexOf('grapefruit')
        !== -1, 'Passes: Session cart has grapefruit.');

      ok(savedCart.items.indexOf('grapefruit') === -1,
        'Fails: The stored cart is unchanged.');
    });

Sadly, when the user adds or removes items from his session cart, those changes will destroy the settings for the stored cart that she wants to use later. The trouble with this code is here:


    createCart = function (items) {
      var cart = Object.create(cartProto);
      cart.items = items;
      return cart;
    },

At this point, `cart.items` is a reference to the prototype `items` attribute. This is improved with one small change to the code:


    cart.items = Object.create(items);

Now the new cart will have its own copy of the item data, so changes will not be destructive to `storedCart`.

The best way to ensure that your `program` contains few unintentional side effects is to avoid them in your _functions_. If your function operates on outside variables, return a _copy_ instead of the original.

A _pure function_ has no side effects. It does not alter existing variables or program state in any way, and always returns the same value given the same inputs.

Wherever possible, make sure that your functions don't change anything outside the function itself. Return amended copies rather than originals. Note that you can still alter program state. REST works this way: You get a _copy_ of the data resource (called a _representation_), manipulate it, and send the copy back to the server.

Writing most of your functions in a similar fashion can help you separate concerns and reduce code duplication. For example, if you need to implement data validation months after you wrote the original code, and you have a single function that is responsible for writing data to your data resource, it will be trivial to add the needed validation. If hundreds of functions throughout the codebase are accessing the data directly, it will be a much more difficult task.

Keeping things isolated in this way can also enhance your ability to implement state management. If the functions that manipulate data don't have to worry about the state of the program and account for side effects created by other functions, they can do their job with much less code. All they have to know how to do is the _one task_ they were designed to do.

Likewise, the only functions that should be manipulating the DOM should be the methods dedicated to DOM manipulation, such as a DOM plugin or a view's `.render()` method.

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

## Method Design

Several techniques exist in JavaScript to design method APIs. JavaScript supports named parameter lists, function polymorphism, method chaining, and lambda expressions. You should be familiar with all of these techniques so that you can choose the right tool for the job.

Some principles to keep in mind when you design your methods. This bears repeating:

  * Keep It Simple, Stupid (KISS)

  * Do One Thing, and do it well (DOT)

  * Don't Repeat Yourself (DRY)

### Named Parameters

The number of variables you pass into a function is called its _arity_. Generally speaking, function arity should be kept small, but sometimes you need a wide range of parameters (for example, to initialize the configuration of a module, or create a new object instance). The trouble with a large arity is that each parameter must be passed into the function in the right order, even if several parameters are not needed. It can be difficult to remember what order is required, and it doesn't make sense to require a parameter that isn't really required for the function to do its job properly.

This example is designed to set up a new user account. Each user account has some default settings that get honored unless an override value is passed in:


    var userProto = {
        name: '',
        email: '',
        alias: '',
        showInSearch: true,
        colorScheme: 'light'
      };

    function createUser(name, email, alias, showInSearch,
      colorScheme) {

      return {
        name: name || userProto.name,
        name: email || userProto.email,
        alias: alias || userProto.alias,
        showInSearch: showInSearch,
        colorScheme: colorScheme || userProto.colorScheme
      };
    }

    test('User account creation', function () {
      var newUser = createUser('Tonya', '', '', '', 'dark');

      equal(newUser.colorScheme, 'dark',
        'colorScheme stored correctly.');
    });

In this case, the `createUser()` function takes five optional parameters. The userProto object is a prototype (not to be confused with the `prototype` property). The trouble with this implementation becomes obvious when you look at the usage in isolation:


    var newUser = createUser('Tonya', '', '', '', 'dark');

What jumps out immediately is that it's impossible to know what the second, third, or fourth parameter is without looking at the `createUser()` implementation. It's also impossible to set the last parameter without passing in values for _all parameters_. What's more, if you want to add more parameters later, or change the order of the parameters, it's going to be difficult if the function is used frequently.

Here is a better alternative:


    var newUser = createUser({
      name: 'Mike',
      showInSearch: false
    });

You can implement this easily using the extend method that comes with most popular libraries (including jQuery and Underscore). Here's how it's done with jQuery:


    function createUser(options) {
      return $.extend({}, userProto, options);
    }

`$.extend()` takes objects as its parameters. The first is the object to be extended. In this case, we want to return a new object so that we don't alter the userProto or options objects. The other objects (as many as you like) hold the properties and methods you wish to extend the first object with. This is a simple, elegant way to reuse code.

### Function Polymorphism

In computer science, _polymorphism_ means that something behaves differently based on context, like words that have different meanings based on how they're used:

  * "Watch out for that sharp turn in the road!"

  * "That knife is sharp!"

  * "John Resig is sharp! Making the jQuery function polymorphic was a stroke of genius."

_Polymorphic functions_ behave differently based on the parameters you pass into them. In JavaScript, those parameters are stored in the array-like `arguments` object, but it’s missing useful array methods.

`Array.prototype.slice()` is an easy way to shallow copy some or all of an array (or an array-like object).

You can borrow the `.slice()` method from the `Array` prototype using a technique called _method delegation_. You delegate the `.slice()`call to the `Array.prototype` object. The method call looks like this:


    var args = Array.prototype.slice.call(arguments, 0);

Slice starts at index `0` and returns everything from that index on as a new array. That syntax is a little long winded, though. It's easier and faster to write:


    var args = [].slice.call(arguments, 0);

The square bracket notation creates a new empty array to delegate the slice call to. That sounds like it might be slow, but creating an empty array is actually a very fast operation. Faster, in fact, than delegating to `Array.prototype`.

You could use this technique to create a function that sorts parameters:


    function sort() {
      var args = [].slice.call(arguments, 0);
      return args.sort();
    }

    test('Sort', function () {
      var result = sort('b', 'a', 'c');
      ok(result, ['a', 'b', 'c'], 'Sort works!');
    });

Because `arguments` is not a real array, it doesn't have the `.sort()` method. However, since a real array is returned from the `.slice()` call, you have access to all of the array methods on the `args` array. The `.sort()` method returns a sorted version of the array.

Polymorphic functions frequently need to examine the first argument in order to decide how to respond. Now that `args` is a real array, you can use the `.shift()` method to get the first argument:


    var first = args.shift();

Now you can branch if a string is passed as the first parameter:


    function morph(options) {
      var args = [].slice.call(arguments, 0),
        animals = 'turtles'; // Set a default

      if (typeof options === 'string') {
        animals = options;
        args.shift();
      }

      return('The pet store has ' %2B args %2B ' ' %2B animals
        %2B '.');
    }

    test('Polymorphic branching.', function () {
      var test1 = morph('cats', 3),
        test2 = morph('dogs', 4),
        test3 = morph(2);

      equal(test1, 'The pet store has 3 cats.', '3 Cats.');
      equal(test2, 'The pet store has 4 dogs.', '4 Dogs.');
      equal(test3, 'The pet store has 2 turtles.',
        'The pet store has 2 turtles.');
    });

#### Method Dispatch

_Method dispatch_ is the mechanism that determines what to do when an object receives a message. JavaScript does this by checking to see if the method exists on the object. If it doesn't, the JavaScript engine checks the prototype object. If the method isn't there, it checks the prototype's prototype, and so on. When it finds a matching method, it calls the method and passes the parameters in.

_Dynamic dispatch_ enables polymorphism by selecting the appropriate method to run based on the parameters that get passed into the method at run time. Some languages have special syntax to support dynamic dispatch. In JavaScript, you can check the parameters from within the called method, and call another method in response:


    var methods = {
        init: function (args) {
          return 'initializing...';
        },
        hello: function (args) {
          return 'Hello, ' %2B args;
        },
        goodbye: function (args) {
          return 'Goodbye, cruel ' %2B args;
        }
      },
      greet = function greet(options) {
        var args = [].slice.call(arguments, 0),
          initialized = false,
          action = 'init'; // init will run by default

        if (typeof options === 'string' &amp;&amp;
            typeof methods[options] === 'function') {

          action = options;
          args.shift();
        }

        return methods[action](args);
      };

    test('Dynamic dispatch', function () {
      var test1 = greet(),
        test2 = greet('hello', 'world!'),
        test3 = greet('goodbye', 'world!');

      equal(test2, 'Hello, world!',
        'Dispatched to hello method.');

      equal(test3, 'Goodbye, cruel world!',
        'Dispatched to goodbye method.');
    });

Dynamic dispatch is a common technique in chainable modules, such as jQuery plugins (see the section on Method Chaining), and functional programming.

### Generics and Collection Polymorphism

_Generic programming_ is a style that attempts to express algorithms and data structures in a way that is type-agnostic. The idea is that most algorithms can be employed across a variety of different types. Generic programming typically starts with one or more type-specific implementations, which then get _lifted_ (abstracted) to create a more generic version that will work with a new set of types.

Generics do not require conditional logic branching to implement an algorithm differently based on the type of data passed in. Rather, the data types passed in must support the required features that the algorithm needs in order to work. Those features are called _requirements_, which in turn get collected into sets called _concepts_.

Generics employ _parametric polymorphism,_ which uses a single branch of logic applied to generic type parameters. In contrast, _ad-hoc polymorphism,_ relies on conditional branching to handle the treatment of different parameter types (either built-in to the language with features like dynamic dispatch, or introduced at program design time).

Generic programming is particularly relevant to functional programming because functional programming works best when a simple function vocabulary can express a wide range of functionality, regardless of type.

In most languages, generic programming is concerned with making algorithms work for different types of lists. In JavaScript, any collection (array or object) can contain any type (or mix of types), and most programmers rely on duck typing to accomplish similar goals. That said, stronger typing is coming as an optional feature to JavaScript, and proposals have been introduced to add specific support for generics to the language. Many of the built in object methods are generics, meaning that they can operate on multiple types of objects.

JavaScript supports two types of collections: Objects, and arrays. The principle difference between an object and an array is that one is keyed with names, and the other sequentially with numbers. Objects don't guarantee any particular order. Arrays do. Other than that, both behave pretty much the same. It often makes sense to implement functions that work regardless of which type of collection gets passed in.

A lot of the functions you might apply to an array would also work for an object and vise verse. For example, say you want to select a random member from an object or array.

The easiest way to select a random element is to use a numbered index, so if the collection is an object, it could be converted to an array using ad-hoc polymorphism. The following function will do that:


    var toArray = function toArray(obj) {
      var arr = [],
        prop;

      for (prop in obj) {
        if (obj.hasOwnProperty(prop)) {
          arr.push(prop);
        }
      }
      return arr;
    };

The `randomItem()` function is easy now. First, you test the type of collection that gets passed in, and convert it to an array if it's not one already, and then return a random item from the array using the built-in `Math.random()` method:


    var randomItem = function randomItem(collection) {
      var arr = ({}.toString.call(collection) !==
        '[object Array]')
          ? toArray(collection)
          : collection;
      return arr[Math.floor(arr.length * Math.random())];
    };

    test('randomItem()', function () {
      var obj = {
          a: 'a',
          b: 'b',
          c: 'c'
        },
        arr = ['a', 'b', 'c'];

      ok(obj.hasOwnProperty(randomItem(obj)),
        'randomItem works on Objects.');

      ok(obj.hasOwnProperty(randomItem(arr)),
        'randomItem works on Arrays.');
    });

The tests above check to see if the returned value exists in the test object.

Unlike true generics, the code above relies on conditional branching internally to handle objects as a special case. Since arrays are already objects in JavaScript, a lot of what you might do with an object will work for arrays without any conversion. In other words, a lot of functions designed to act on JavaScript objects are truly generic in that they will also work for arrays without any specialized logic (assuming that the array has all of the required features).

Collection polymorphism is a very useful tool for code reuse and API consistency. Many library methods in both jQuery and Underscore work on both objects and arrays.

JavaScript 1.6 introduced a number of new built-in array and string generics. With 1.6 compatible JavaScript engines, you can use array methods such as `.every()` on strings:


    var validString = 'abc',
      invalidString = 'abcd',

      validArray = ['a', 'b', 'c'],
      invalidArray = ['a', 'b', 'c', 'd'],

      isValid = function isValid(char) {
        return validString.indexOf(char) &gt;= 0;
      };

    test('Array String generics', function () {
      ok(![].every.call(invalidString, isValid),
        'invalidString is rejected.');
      ok([].every.call(validString, isValid),
        'validString passes.');
      ok(![].every.call(invalidArray, isValid),
        'invalidArray is rejected.');
      ok([].every.call(validArray, isValid),
        'validArray passes.');
    });

You can also use string methods on numbers:


    var num = 303;

    test('String number generics', function () {
      var i = ''.indexOf.call(num, 0);
      ok(i === 1,
        'String methods work on numbers.');
    });

### Method Chaining and Fluent APIs

_Method chaining_ is using the output of one method call as the context of the next method call. For example, in jQuery you often see things like:


    $('.friend').hide().filter('.active').show();

Perhaps better:


    $('.friend')
      .hide()
      .filter('.active')
      .show();

Which translates, "find all elements with the `friend` class and hide them, then find the friends with the `active` class and show them."

On page load, our friends list looks like this:


    * Mick
    * Hunz (active)
    * Yannis

After running the code above, it looks like this:


    * Hunz (active)

One of the primary benefits of method chaining is that it can be used to support fluent APIs. In short, a _fluent API_ is one that reads like natural language. That doesn't mean that it has to look like English, but fluent APIs often use real verbs as method calls (like hide and show).

jQuery is a great example of a fluent API. In fact, jQuery's fluency makes it one of the easiest libraries to learn and use. Almost everything that jQuery does was already available in other libraries when jQuery was first released. What made jQuery stand out was the easy-to-learn vocabulary. Almost every jQuery statement reads something like this:

Find all the elements matching a selector, then do x, then y, then z. Selection, verb, verb...

Chaining has it's disadvantages. It can encourage you to do too much in a single line of code, it can encourage you to write too much procedural code, and it can be difficult to debug. It's tough to set a breakpoint in the middle of a chain.

If you get into a tight spot debugging a chain, remember you can always capture the output at any step in the chain with a variable assignment, and resume the chain by calling the next method on that variable. In fact, you don't have to chain at all to use a fluent API.

There's more to fluency than chaining. The salient point is that fluent methods are made to work together to express functionality in the same way that words in a language work together to express ideas. That means that they output objects with methods that make sense to call on the resulting data.

Building a fluent API is a lot like building a miniature Domain Specific Language (DSL). We'll go into detail about how to build a fluent API in the next chapter.

It's easy to go too far with fluent APIs. In other languages, fluent APIs are frequently used to configure a new object. Since JavaScript supports object literal notation, this is almost certainly a bad use of fluency in JavaScript.

Fluency can also lead to unnecessary verbosity. For example, the Should.js API encourages you to write long sentences with strung-together dot notation access. Keep it simple.

## Functional Programming

Functional programming is a style of programming that uses _higher order functions_ (as opposed to objects and data) to facilitate code organization and reuse. A higher order function treats functions as data, either taking a function as an argument, or returning a function as a result. Higher order functions are very powerful code reuse tools which are commonly used in JavaScript for a variety of purposes. Here are a few examples:

They can be used to _abstract algorithms from datatypes._ This is important because it reduces the amount of code you need in order to support various datatypes in your reusable algorithms. Without this, you might create a special function to operate on a collection of one type, and a similar, but slightly different function to operate on another. This is a very common problem in most applications.

A series of functions which do essentially the same thing and differ only in the type of data they operate on is a serious code smell. You're violating the DRY principle, one of the most valuable guidelines available to you in software design.

For example, imagine you have to sort two lists of items by price, but one is a list of concerts where the price is called `ticketPrice`, another is a list of books where the price is just `price`.

Of course, you could attempt to squeeze both into a single, more generic type and create a generic function that will work with both (using duck typing), but that might require an unjustifiable refactor.

Instead, you could pass in a function to handle the comparison for the sort:


    var shows = [
        {
          artist: 'Kreap',
          city: 'Melbourne',
          ticketPrice: '40'
        },
        {
          artist: 'DJ EQ',
          city: 'Paris',
          ticketPrice: '38'
        },
        {
          artist: 'Treasure Fingers',
          city: 'London',
          ticketPrice: '60'
        }
      ],
      books = [
        {
          title: 'How to DJ Proper',
          price: '18'
        },
        {
          title: 'Music Marketing for Dummies',
          price: '26'
        },
        {
          title: 'Turntablism for Beginners',
          price: '15'
        }
      ];

    test('Datatype abstraction', function () {
      var sortedShows = shows.sort(function (a, b) {
          return a.ticketPrice &lt; b.ticketPrice;
        }),
        sortedBooks = books.sort(function (a, b) {
          return a.price &lt; b.price;
        });
      ok(sortedShows[0].ticketPrice &gt;
        sortedShows[2].ticketPrice,
        'Shows sorted correctly.');
      ok(sortedBooks[0].price &gt;
        sortedBooks[1].price,
        'Books sorted correctly.');
    });

Higher order functions are very commonly used to _abstract list iteration boilerplate from algorithm implementation_. You may have noticed in the previous example that the built-in `Array.sort()` method handles the iteration details internally, so you don't even have to think about writing a for loop. You frequently see a pattern like this repeated in most code:


    test('Traditional for loop', function () {
      var i,
        length = books.length;

      for (i = 0; i &lt; length; i%2B%2B) {
        books[i].category = 'music';
      }

      ok(books[0].category === 'music',
        'Books have categories.');
    });

There are several functional methods available whose sole purpose is to iterate through a collection in order to process it with a passed in function of your choice. The most basic of the Array iterators is `.forEach()`. For example, imagine you want to add a category field to each of the book items. Using `.forEach()`, you don't have to worry about writing the loop, or keeping track of the iteration index. You simply write the function that you'll use to process the list:


    test('Iterator abstraction', function () {
      books.forEach(function (book) {
        book.category = 'music';
      });

      ok(books[0].category === 'music',
        'Books have categories.');
    });

Another common use of higher order functions is to support _partial application and currying_ (discussed in the section of the same name).

### Stateless Functions (aka Pure Functions)

_Pure_ functions are stateless. This means that they do not use or modify variables, objects, or arrays that were defined outside the function. Given the same inputs, stateless functions will always return the same output. Stateless functions won't break if you call them at different times.

That feature is particularly useful in JavaScript applications, because you often need to manage a lot of asynchronous events, so time becomes a major factor in code organization.

Because you don't have to worry about clobbering shared data, stateless functions can often be run in parallel, meaning that it's much easier to scale computation horizontally across a large number worker nodes. In other words, stateless functions are great for high-concurrency applications.

Stateless functions can be chained together for stream processing. (Enumerator, processor, [processor], [processor], ...., collector).

Stateless functions can be abstracted and shared as context-agnostic modules.

To maximize code reuse, try to make as many functions as possible both stateless and generic (or polymorphic). Many jQuery methods satisfy both requirements. Such functions tend to be very useful library methods.

### Partial Application and Currying

_Partial Application_ wraps a function that takes multiple arguments, and returns a function that takes fewer arguments. It uses closures to _fix_ one or more arguments so that you only need to supply the arguments that are unknown. Imagine you have a function multiply() which takes two arguments, x and y, and you notice that you often call the function to multiply by specific numbers. You could create a generic function that will fix one parameter:


    var multiply = function multiply(x, y) {
        return x * y;
      },

      partial = function partial(fn) {
        // Drop the function from the arguments list and
        // fix arguments in the closure.
        var args = [].slice.call(arguments, 1);

        // Return a new function with fixed arguments.
        return function() {
          // Combine fixed arguments with new arguments
          // and call fn with them.
          var combinedArgs = args.concat(
            [].slice.call(arguments));
          return fn.apply(this, combinedArgs);
        };
      },

      double = partial(multiply, 2);

    test('Partial application', function () {
      equal(double(4), 8,
        'partial() works.');
    });

As of JavaScript 1.8.5, you can also use `Function.prototype.bind()` for partial application. The only disadvantage is that you won't be able to override the value of `this` with `.call()` or `.apply()`. If your function uses `this`, you shouldn't use `.bind()`. This is how you use `.bind()` for partial application, using the same multiply function:


    var boundDouble = multiply.bind(null, 2); // null context

    test('Partial application with bind', function () {
      equal(boundDouble(4), 8,
          '.bind() should allow partial application.');
    });

You may have heard this process described as _currying_. The two are commonly confused, but there is a difference. Currying is the process of transforming a function which takes multiple arguments into a chain of functions which take no more than one argument, each.

An add function, `add(1, 2, 3)` would become `add(1)(2)(3)` in curried form, where the first call returns a function that returns another function, and so on. This concept is important in lambda calculus (the inspiration for the lisp family of languages, from which JavaScript borrows heavily). However, since JavaScript supports multiple arguments, it's not common to see true currying in JavaScript applications.

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



   [1]: http://chimera.labs.oreilly.com/books/1234000000262
   [2]: http://chimera.labs.oreilly.com#
   [3]: http://chimera.labs.oreilly.com/ch02.html
   [4]: http://chimera.labs.oreilly.com/ch04.html
   [5]: http://chimera.labs.oreilly.com/index.html
   [6]: http://oreilly.com/terms/
   [7]: http://oreilly.com/oreilly/privacy.csp
   [8]: mailto:scordesse%40oreilly.com
  

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
