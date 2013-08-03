# [译] [PJA] [300] 第三章 函数

> * Original: [Chapter 3. Functions - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch03.html)
> * Translated by: [cssmagic](https://github.com/cssmagic)

# Chapter 3. Functions

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

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
