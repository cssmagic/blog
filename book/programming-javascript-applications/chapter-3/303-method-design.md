# [译] [PJA] [303] 方法的设计

> * Original: [Method Design](http://chimera.labs.oreilly.com/books/1234000000262/ch03.html#method_design)
> * Translated by: [cssmagic](https://github.com/cssmagic)

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

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
