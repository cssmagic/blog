# [译] [PJA] [202] 代码质量

> * Original: [Code Quality](http://chimera.labs.oreilly.com/books/1234000000262/ch02.html#code_quality)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Code Quality

When you approach JavaScript for application development, style becomes more important than it is in the context of one-off scripts. Code is more readable if code style and conventions are used consistently. That's not to say that your style shouldn't evolve as you hone your skills: rather, it should converge on a set of standards that other developers can learn and use.

Some style choices are arbitrary, but many best practices have emerged in the JavaScript community. In this section we'll cover a few of the more important style considerations you should be aware of.

Perhaps the easiest way to start converging on a code quality standard is to begin _linting_ your code with a tool like _JSLint_. Linters scan your source code for syntax errors (and in the case of JSLint, some stylistic errors, as well).

The most obvious reason to use a lint tool is that it can be an extremely valuable debugging asset. Most bugs are the result of syntax errors. If a tool can help you eliminate all syntax-related bugs from your code quickly, you'll see huge gains in productivity.

A few of the style choices enforced in JSLint have raised controversy in the JavaScript community, but even if you don't agree with every point that the author, [Douglas Crockford][7] makes about JavaScript style, it's important to have a thorough understanding of why those choices were made.

More importantly, using an automated tool like JSLint is the best way to enforce a minimum level of code quality and style throughout the code base. In my experience, getting all the developers on the same page is worth a little sacrifice in style.

If you can make the trade offs, what you gain in increased productivity is extraordinary. However, if you can't make one or two of the trade-offs prescribed by JSLint, there is a tool for you, as well: JSHint. In JSHint, the rules are more flexible.

Think carefully before choosing to ignore the recommendations of JSLint. While some of them may seem arbitrary at first, they're all there for a reason. Deviate at your own risk.

### Best Practices Quick Reference

#### Indentation - Be Consistent

Tabs or Spaces? It's common for people to have very strong feelings, but both have their merits. Tab users say it saves them keystrokes, there are never any hidden whitespace characters, and tabs allow users to set their own indentation display preferences. Space users say that most editors will automatically substitute spaces for tabs, and support "soft tabs" that have similar display characteristics to tabs. And you can line up your code better with more fine-grained control of your whitespace. All good points. Too close to call a clear winner.

What's important is that you pick one and stick with it. If you're working as a team, all members of the team should use the same standard.

#### Use Semicolons

There is a recent and growing movement in JavaScript circles to use a style that leaves off optional semicolons. When the JavaScript runtime encounters a condition where there should be a semicolon to make sense of the code, but can't find one, it inserts a semicolon automatically. This behavior is called Automatic Semicolon Insertion (ASI).

The new style depends on ASI to produce valid JavaScript code for the engine to execute. In my opinion, this style should be avoided in the short term, but maybe embraced in the long term.

The problem with the new style is not in the style itself. Provided you follow a few simple rules, it is a readable and viable. Less syntax is sometimes more.

The problem is that there are currently no automated lint tools that I am aware of which can ignore your missing semicolons when they're not required, but highlight the error cases where leaving out the semicolon will cause ambiguities that ASI can't resolve. Lint tools are highly valuable, and missing semicolons are among the most common sources of bugs. I can't recommend a style that would require you to abandon useful lint rules.

Whatever you do, don't just leave all semicolons out. Sometimes, semicolons are required. The following tests all fail due to missing semicolons:

    test('Missing semicolon before %2B', function () {
      var a = 1 %2B 1
      %2B'3';

      equal(
        a, 2,
        'a should be 2.'
      ); // Fails. a = 23
    });

    test('Missing semicolon before [', function () {
      var b = 1 %2B 1
      [1, 1].forEach(function (num) {
        b %2B= num;
      });

      // Error: Cannot call method forEach of undefined.
      equal(
        b, 4,
        'b should be 4.'
      );
    });

    test('Missing semicolon before (', function () {
      var x = 1,
        f = function f () {
          ok(false, 'This test should not run.'); // Fails.
        }

      (function () {
        /* Do something interesting. */
      }());
    });

#### Bracket Placement - Right Side

Due to automatic semicolon insertion, brackets should always be placed to the right, on the line that begins the statement.

Wrong:

    var getLocation = function getLocation()
    {
      return
      {
        x: 20,
        y: 20
      };
    };

Right:

    var getLocation = function getLocation() {
      return  {
        x: 20,
        y: 20
      };
    };

#### Avoid Name Collisions

The default scope for variables in JavaScript is global. Every other script on the page has to share that namespace. In order to prevent name collisions, your application should use as few global names as possible. Typically, that would be a single name for the application, and possibly a handful of others for supporting libraries, such as jQuery and Underscore.

Wrong: x is Global

    var x;
    // Do stuff with x...

Right: x is Local to the Wrapping Function

    (function myScript() {
      var x;
      // Do stuff with x...
    }());

This type of function wrapper is called an Immediately Invoked Function Expression (IIFE, pronounced "iffy"). For details, see the IIFE section.

#### Always Use Var

If you forget to use the `var` keyword, your variable assignments will pollute the global scope.

Wrong: x is Global

    var add = function add(number) {
      x = 2;
      return number %2B x;
    };

    test('Without var.', function () {
      equal(
        add(2), 4,
        'add() should add 2 to whatever you pass in.'
      );

      // Fails
      ok(!x, 'x should not pollute the global scope.');
    });

Right: Function Scope

    var add = function add(number) {
      var x = 2;
      return number %2B x;
    }

    test('With var.', function () {
      equal(
        add(2), 4,
        'add() should add 2 to whatever you pass in.'
      );
      ok(!x, 'x should not pollute the global scope.');
    });

#### Use One var Statement per Function

Because of the peculiarities with hoisting (see the sections on function and variable hoisting), you should only use one `var` statement per function, at the top of your function.

Wrong: Multiple var Statements

    (function myScript() {
      var x = true;
      var y = true;
      /* do some stuff with x and y */
      var z = true;
      /* do some stuff with z */
    }());

Right: One `var` per Function

    (function myScript() {
      var x = true,
        y = true,
        z = true;
      /* do some stuff with x, y, and z */
    }());

#### Avoid Constants

Constants are a class of types that allow you to specify names for values that never change. They're used to express things like mathematical constants (like pi), fixed ratios (the number of seconds in a minute), etc... If a constant is able to change, it will likely break the code that relies on it. It is for this reason that in most languages, constants are immutable.

That is not the case in JavaScript. There is a `const` keyword coming to JavaScript, but it will likely be a few years before you can safely use it for cross-browser code. This hasn't stopped people from faking constants in JavaScript, however.

The general convention is to use all caps to name constants. This convention is much older than JavaScript, and the well understood meaning is, "you can count on this value to never change, and to never need changing." Because you can't count on fake constants to be constant, it's probably better not to fake it at all. If you really need the value to be safe, store it in a closure and use a getter function to access it. (See the section on closures to learn more.)

More often than not in practice, I've seen fake constants abused for things that never should have been considered constant in the first place. It's very common to confuse constants and configuration.

Code that employs constants instead of configuration tends to get more rigid and brittle as it grows. If you can't configure a module, the same code tends to get rewritten with few differences, except for variable names and a few differences in constant declarations.

Employing configuration instead of constants allows you to re-use the code, instead of writing new code.

Wrong: Abusing constants

    var createUserBox = function createUserBox() {
      var WIDTH = '80px',
        HEIGHT = '80px';
      return {
        width: WIDTH,
        height: HEIGHT
      };
    },
    createMenuBox = function createMenuBox() {
      var WIDTH = '200px',
        HEIGHT = '400px';
      return {
        width: WIDTH,
        height: HEIGHT
      };
    };

    test('Avoid Constants', function () {
      var userBox = createUserBox(),
        menuBox = createMenuBox();
      equal(
        userBox.width, '80px',
        'userBox should be 80px wide.'
      );
      equal(
        menuBox.width, '200px',
        'menuBox should be 200px wide.'
      );
    });

Right: Favor configuration

    var createBox = function createBox(options) {
      var defaults = {
          width: '80px',
          height: '80px'
        };
      return $.extend({}, defaults, options);
    };

    test('Avoid Constants', function () {
      var userBox = createBox({
          width: '80px',
          height: '80px'
        }),
        menuBox = createBox({
          width: '200px',
          height: '400px'
        });

      equal(
        userBox.width, '80px',
        'userBox should be 80px wide.'
      );
      equal(
        menuBox.width, '200px',
        'menuBox should be 200px wide.'
      );
    });

#### Use Functional Iterators When Possible

JavaScript offers the capability to program in a functional style, meaning that you can pass functions as arguments to other functions, and apply the passed in function to each element in a data resource (array or object). The technique is handy because it can make your code more readable, it can abstract away the looping mechanics, and keep the loop control variables isolated from your main function. That said, loops can give you finer grained control, and there can be performance considerations when you're looping over very large data sets.

Good:

    var getCount = function getCount() {
        var i,
          count = [1, 2, 3],
          length = count.length,
          text = '';

        for (i = 0; i &lt; length; i %2B= 1) {
          text %2B= count[i] %2B ' ';
        }
        return text;
      };

    test('Looping over an array:', function () {
      var text = getCount();

      equal(
        text, '1 2 3 ',
        'getCount() should count to three.'
      );
    });

Better for Most Situations:

    var getCount = function getCount() {
      var count = [1, 2, 3],
        text = '';

      count.forEach(function (number) {
        text %2B= number %2B ' ';
      });
      return text;
    };

    test('Functional iterator:', function () {
      var text = getCount();

      equal(
        text, '1 2 3 ',
        'getCount() should count to three.'
      );
    });

Note that you don't need to worry about the `i` or `length` variables in this version.

Functional iterators were added in ECMAScript 5, meaning that you'll need to shim them to prevent throwing errors in older browsers. Here's the MDN `.forEach()` shim:

    // Shim .forEach()
    if ( !Array.prototype.forEach ) {
      Array.prototype.forEach = function(fn, scope) {
        for (var i = 0, len = this.length; i &lt; len; %2B%2Bi) {
          fn.call(scope || this, this[i], i, this);
        }
      }
    }

A _shim_ is any code that wraps a standard API call in order to abstract it. Shims can be used to normalize cross browser behavior. A _polyfill_ is a shim that implements new standard features from JavaScript, HTML5 and related technologies using existing capabilities in order to deliver that new functionality to older browsers. To learn more about using JavaScript polyfills, search for any new feature in the JavaScript method descriptions on the _Mozilla Developer Network_ (MDN). Most of the new features include a complete or partial polyfill in the description.

Incidentally, the MDN documentation is perhaps the most thorough and authoritative online JavaScript reference. If you add the keyword "MDN" when you search google for JavaScript features, it should pull up the MDN documentation, instead of other much less reliable and much less complete references online.

#### Be Careful With if Statements

The results of type coercion in JavaScript can be difficult to predict, even when you know the rules. Here you'll see how subtle differences in comparisons can lead to significant changes in the results.

Be careful when relying on truthiness for logic. Whenever possible, use comparison operators to clarify your meaning. Falsy values include:

  * `0`

  * `undefined`

  * `null`

  * empty strings

  * `false`

Boolean objects instantiated with `new Boolean(false)` are _truthy_ because they're objects, and objects are truthy. In order to test against them you need to use the `.valueOf()` method. It's better in almost all cases to use `true` and `false`, instead.

Empty objects and empty arrays are truthy, including objects created with `new Boolean(false)`.

Wrong: Don't use `new Boolean()`

    var myBool = new Boolean(false);

    test('Boolean object', function () {
      ok(!myBool, 'Should be falsy'); // Fails
      ok(!myBool.valueOf(),
        'Should be falsy.'); // Passes
    });

Right: Use `true` or `false` in your boolean declarations

    var myBool = false;

    test('Boolean object', function () {
      ok(!myBool, '!myBool should be false.');
    });

The following series of tests are meant to demonstrate how different types of comparisons in JavaScript that look similar can deliver very different results. Always be careful that you're using the correct test for your situation:

    function truthy(x) {
      if (x) {
        return true;
      } else {
        return false;
      }
    }

    test('Truthy', function () {
      // Falsy
      equal(truthy(0), true, 'truthy(0)'); // Fail
      equal(truthy(''), true, "truthy('')");  // Fail
      equal(truthy(null), true, 'truthy(null)'); // Fail
      equal(truthy(undefined), true,
        'truthy(undefined)'); // Fail
      equal(truthy(false), true, 'truthy(false)'); // Fail

      // Truthy
      equal(truthy('0'), true, "truthy('0')"); // Pass
      equal(truthy(new Boolean(false)), true,
        'truthy(new Boolean(false))'); // Pass
      equal(truthy({}), true, 'truthy({})'); // Pass
      equal(truthy([]), true, 'truthy([])'); // Pass
      equal(truthy([0]), true, 'truthy([0])'); // Pass
      equal(truthy([1]), true, 'truthy([1])'); // Pass
      equal(truthy(['0']), true, "truthy(['0'])"); // Pass
      equal(truthy(['1']), true, "truthy(['1'])"); // Pass
    });

These are the falsy and truthy values we'll use as a baseline for a series of other comparisons.

Often, developers will use `if (x)` when they really want to see if the value has been set at all. This is especially problematic when `0`, empty strings, or `false` are valid values. In that case, you want the test to pass as long as the expression evaluates to anything other than `null` or `undefined`. A good rule of thumb is to only use `if (x)` to evaluate booleans or the existence of objects or arrays.

    function exists(x) {
      if (x !== undefined &amp;&amp; x !== null) {
        return true;
      } else {
        return false;
      }
    }

    test('exists', function () {
      // Falsy
      equal(exists(0), true, 'exists(0)'); // Pass
      equal(exists(''), true, "exists('')"); // Pass
      equal(exists(null), true, 'exists(null)');
      equal(exists(undefined), true, 'exists(undefined)');
      equal(exists(false), true, 'exists(false)'); // Pass

      // Truthy
      equal(exists('0'), true, "exists('0')"); // Pass
      equal(exists(new Boolean(false)), true,
        'exists(new Boolean(false))'); // Pass
      equal(exists({}), true, 'exists({})'); // Pass
      equal(exists([]), true, 'exists([])'); // Pass
      equal(exists([0]), true, 'exists([0])'); // Pass
      equal(exists([1]), true, 'exists([1])'); // Pass
      equal(exists(['0']), true, "exists(['0'])"); // Pass
      equal(exists(['1']), true, "exists(['1'])"); // Pass
    });

Of course, a shorter version of that function will return the same results:

    function exists(x) {
      return (x !== undefined &amp;&amp; x !== null);
    }

The `==` operator can return some problematic results due to type coercion. For example, say you're checking an array to be sure it contains a valid price, and some items cost $0.00. Your code could fail because `['0.00'] == false`. Use `===` instead.

    var isFalse = function isFalse(x) {
      return (x == false);
    };

    test('isFalse using ==', function () {
      // Falsy
      equal(isFalse(0), true, 'isFalse(0)'); // Pass
      equal(isFalse(''), true, "isFalse('')"); // Pass
      equal(isFalse(null), true, 'isFalse(null)'); // Fail
      equal(isFalse(undefined), true, 'isFalse(undefined)'); // Fail
      equal(isFalse(false), true, 'isFalse(false)'); // Pass

      // Truthy
      equal(isFalse('0'), true, "isFalse('0')"); // Pass
      equal(isFalse(new Boolean(false)), true,
        'isFalse(new Boolean(false))'); // Pass
      equal(isFalse({}), true, 'isFalse({})'); // Fail
      equal(isFalse([]), true, 'isFalse([])'); // Pass
      equal(isFalse([0]), true, 'isFalse([0])'); // Pass
      equal(isFalse([1]), true, 'isFalse([1])'); // Fail
      equal(isFalse(['0']), true, "isFalse(['0'])"); // Pass
      equal(isFalse(['1']), true, "isFalse(['1'])"); // Fail
    });

The following code will only return true if `x` is actually set to `false`:

    var isFalse = function isFalse(x) {
      return (x === false);
    };

The following function is dangerous, because it will return `true` for `1`, `[1]`, and `['1']`.

    var isTrue = function isTrue(x) {
      return (x == true);
    };

Use `===` instead to eliminate the possibility of false positives.

Remember that `if (x)` will always return true when `x` is an object -- even if the object is empty. It's common to use _ducktyping_ when you examine objects. (If it walks like a duck and talks like a duck, treat it like a duck.) The idea is similar to using feature detection in browsers, instead of running checks against the browser string. Ducktyping is feature detection for objects.

    if (typeof foo.bar === 'function') {
      foo.bar();
    }

#### Avoid Side Effects

In order to avoid unintended side effects, only assign to variables declared inside your function, or declared as formal parameters.

Wrong: Has Side Effects

    var x = 0;

    function increment() {
      x %2B= 1;
      return x;
    }

    test('increment() with side effects.', function () {
      var val = increment();

      equal(
        val, 1,
        'increment() should add one.'
      );
      equal(
        x, 0,
        'x should be unchanged outside the function.'
      ); // Fails
    });

Right: No Side Effects

    var x = 0;

    function increment(x) {
      x %2B= 1;
      return x;
    }

    test('increment() without side effects.', function () {
      var val = increment(x);

      equal(
        val, 1,
        'increment() adds one.'
      );
      equal(
        x, 0,
        'x is unchanged.'
      );
    });

Wherever possible, you should avoid mutating objects declared outside your function. This isn't always practical, but where it is, your code will be more reusable and less _brittle_ (likely to break when unrelated code changes).

Use With Caution: Object Mutation Side Effects

    var obj = {
        value: 2
      };

    function setValue(obj, value) {
      obj.value = value;
      return obj;
    }

    test('setValue() with side effects', function () {
      var myObj = setValue(obj, 3);

      equal(
        myObj.value, 3,
        'setValue() returns new value.'
      );
      equal(
        obj.value, 2,
        'The original should be unchanged.'
      ); // Fails
    });

Better: Return a new object

    var obj = {
        value: 2
      };

    function setValue(obj, value) {
      var instance = Object.create(obj);

      instance.value = value;
      return instance;
    }

    test('setValue() without side effects', function () {
      var myObj = setValue(obj, 3);

      equal(
        myObj.value, 3,
        'setValue() should return new value.'
      );
      equal(
        obj.value, 2,
        'The original is should be unchanged!'
      ); // Passes
    });

#### Don't Use Switch

JavaScript has pretty normal control flow statements that use blocks delineated by curly braces. There is an exception to this: The `switch ... case` statement. The strange thing about `switch ... case` is that you must include the keyword `break` at the end of each `case` to prevent control from falling through to the next `case`. _Fall through_ is a trick that allows you to let more than one case be executed. Control will fall through automatically to the next case unless you explicitly tell it not to with `break`. However, like the optional semi colons and curly braces, it's possible to forget `break` when you really should have used it. When that happens, the bug is difficult to find because the code _looks correct_. For that reason, the `break` statement should never be left off of a `case`, even by design.

With that said, JavaScript has an elegant object literal syntax and first class functions, which makes it simple to create a keyed _method lookup_. The object you create for your method lookup is called an _action object_ or _command object_, and is used in many software _design patterns_, including the useful and versatile _command pattern_. See the section on the command pattern for more details.

Say you're creating a game where the non-player fight actions are selected based on an algorithm defined elsewhere and passed in to doAction as a string. The `switch ... case` form looks like this:

    function doAction(action) {
      switch (action) {
        case 'hack':
          return 'hack';
        break;

        case 'slash':
          return 'slash';
        break;

        case 'run':
          return 'run';
        break;

        default:
          throw new Error('Invalid action.');
        break;
      }
    }

The method lookup version looks like this:

    function doAction(action) {
      var actions = {
        'hack': function () {
          return 'hack';
        },

        'slash': function () {
          return 'slash';
        },

        'run': function () {
          return 'run';
        }
      };

      if (typeof actions[action] !== 'function') {
        throw new Error('Invalid action.');
      }

      return actions[action]();
    }

Or, for input grouping (a frequent use case for the fall through feature): Say you're writing a programming language parser, and you want to perform one action whenever you encounter a _token_ that opens an object or array, and another whenever you encounter a token that closes them. Assume the following functions exist:

    function handleOpen(token) {
      return 'Open object / array.';
    }

    function handleClose(token) {
      return 'Close object / array';
    }

The `switch ... case` form is:

    function processToken (token) {
      switch (token) {
        case '{':
        case '[':
          handleOpen(token);
        break;

        case ']':
        case '}':
          handleClose(token);
        break;

        default:
          throw new Error('Invalid token.');
        break;
      }
    }

The method lookup version looks like this:

    var tokenActions = {
      '{': handleOpen,
      '[': handleOpen,
      ']': handleClose,
      '}': handleClose
    }

    function processToken(token) {
      if (typeof tokenActions[token] !== 'function') {
        throw new Error('Invalid token.');
      }
      return tokenActions[token](token);
    }

At first glance, it might seem like this is more complicated syntax, but it has a few advantages:

  * It uses the standard curly-bracket blocks used everywhere else in JavaScript.

  * You never have to worry about remembering the `break`.

  * Method lookup is much more flexible. Using an action object allows you to alter the cases dynamically at runtime. For example, to allow dynamically loaded modules to extend cases, or even swap out some or all of the cases for modal context switching.

  * Method lookup is object-oriented by definition. With `switch ... case`, your code is more procedural.

The last point is perhaps the most important. The `switch` statement is a close relative of the `goto` statement, which computer science giants argued for twenty years to eradicate from modern programming languages. It has the same serious drawback: Almost everywhere I've seen `switch ... case` used, I've seen it abused. Developers group unrelated functionality into overly-clever branching logic. In other words, `switch ... case` tends to encourage _spaghetti code_, while method lookup tends to encourage well-organized, object-oriented code. It's far too common to find implementations of `switch ... case` which break the principles of _high cohesion_ and _separation of concerns_.

I was once a fan of `switch ... case` as a better alternative to `if ... else`, but after becoming more familiar with JavaScript, I naturally fell into using method lookup, instead. I haven't used `switch ... case` in my code for several years. I don't miss it at all.

If you ever find yourself writing a `switch` statement, stop and ask yourself the following questions:

  * Will you ever need to add more cases? (Queue, Stack, Plugin Architecture)

  * Would it be useful to modify the list of cases at run-time? For example, to change the list of enabled options based on context? (Mode switching)

  * Would it be useful to log the cases that get executed? For example, to create an undo / redo stack, or log user actions to your servers for analysis? (_Command Manager_)

  * Are you referencing your cases by incrementing numbers? E.g. `case 1:`, `case: 2`, etc... (_Iterator_ target)

  * Are you trying to group related inputs together with the fall through feature so that they can share code?

If you answered yes to any of these questions, there is almost certainly a better implementation that doesn't utilize `switch`, or its slippery fall through feature.

#### Don't Use Eval

The desire to use `eval()` should be considered a _code smell_ (as in, "something smells fishy"). It's a good indication that there is probably a better way to accomplish what you're after.

You'll often hear programmers say things like, "this smells", or "something smells funny here." They're not talking about the garlic burger you had for lunch. They're expressing that something doesn't feel right about the code. Maybe a function is too long, or it's doing too much. Maybe there's a better, smarter, faster, or easier way to accomplish the same goal. Things like large inheritance trees and deeply nested logic send up red flags for experienced programmers. Those are code smells.

JavaScript is a very flexible, expressive, dynamic language with a lot less need for ` `eval()` ` than most other languages.

Most of what `eval()` can do for you can be done with code that is faster and easier to read. Using eval forces the JavaScript engine to delay compilation of the `eval()` code, bypassing the usual code compilation flow.

Eval can also poke holes in your security and allow Cross Site Scripting (XSS) attacks.

Some good alternatives to `eval()` and its dopplegangers include _square bracket notation_ (covered in the section on invoking functions), runtime object mutation, and _function currying_ (see the section on function currying in the functions chapter). The `eval()` function should only be used with great caution, and only if it is absolutely the only way to accomplish your goal.

The `eval()` function has dopplegangers you should also avoid:

  * Passing a string to the `Function()` constructor.

  * Passing a string value to `setTimeout()` or `setInterval()` (as opposed to a function reference or lambda expression).

[7]: http://www.crockford.com/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
