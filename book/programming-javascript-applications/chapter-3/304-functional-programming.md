# [译] [PJA] [304] 函数式编程

> * Original: [Functional Programming - Chapter 3. Functions - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch03.html#functional_programming)
> * Translated by: [cssmagic](https://github.com/cssmagic)

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

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
