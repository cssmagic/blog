# [译] [PJA] [402] 语流风格的 JavaScript

> * Original: [Fluent Style JavaScript - Chapter 4. Objects - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch04.html#fluent_style_javascript)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Fluent Style JavaScript

&nbsp;

I have not invented a "new style," composite, modified or otherwise that is set within distinct form as apart from "this" method or "that" method. On the contrary, I hope to free my followers from clinging to styles, patterns, or molds.

...The extraordinary part of it lies in its simplicity.

&nbsp;

&nbsp;
\--Bruce Lee

_Programmers with a background in other languages are like immigrants to JavaScript_. They often code with an accent -- preconceived notions about how to solve problems. For example, programmers with a background in classical OO tend to have a hard time letting constructors go. Using constructor functions is a clear and strong _accent_, because they are completely unnecessary in JavaScript. They are a waste of time and energy.

Unfortunately, most JavaScript learning materials will teach you that you build objects using constructor functions.

There are serious limitations to this way of doing things. The first is that you must always call a constructor using the `new` keyword. Failing to do so will pass the global object in as `this`. Your properties and methods will clobber the global namespace. If you instantiate more than one object, they will not be instance safe. In strict mode, the situation when you forget `new` is a little different: `this` is useless, and trying to assign properties to it will throw an error.

There's a simple workaround that requires a boilerplate check inside every constructor function, but since any function can return an object in JavaScript, you end up using `new` sometimes, but not all the time. A convention has sprung up in JavaScript to help you remember when to use `new` and when not to. Constructor functions always begin with a capital letter. If you're calling a function that begins with a capital letter, use `new`. Unfortunately, lots of library developers uppercase the first letter of their library namespace, regardless of whether or not it's a constructor function, so even that solution is less than ideal. And that's not even the real problem.

The real problem is that using constructors will almost always get you stuck thinking in classical OO mode. The constructor becomes analogous to a class. You might start to think, "I want to subclass x..." and that's where you get into trouble. You're ignoring two of the best features of JavaScript: Dynamic object extension (you can add properties to any object in JavaScript after instantiation) and prototypal inheritance. The two combined are a much more powerful and flexible way to reuse code than classical inheritance.

Programmers who learned JavaScript as a first language are far less likely to be attached to classical OO and inheritance, because it's harder to implement in JavaScript than the simpler, _native_ alternatives. You can learn a lot about JavaScript by reading the code of experienced JavaScript natives who have not been influenced by classical inheritance.

Most languages have many different accents and grammars. In fact, almost all disciplines spin off into different schools of thought. Martial arts has kick boxing, jui jitsu, boxing, karate, kung fu, and so on. Each emphasize different philosophies, mechanics and core techniques. In his famous book, "The Tao of Jeet Kun Do", Bruce Lee advised that you develop your own style. Study and take in all the good things about different styles, pick what you like and discard the rest to create your own unique style. It's good advice that lies at the heart of Mixed Martial Arts - a discipline that combines the best features of several fighting styles.

The same is true with programming languages. JavaScript itself is a fusion of the best ideas from Scheme (lambda), Self (prototypal inheritance), and Java (syntax).

To coin a phrase, _fluent style_ simply discards inefficient constructs and takes advantage of JavaScript's strengths and efficiencies:

  * _Lambdas and closures_ are a language unto themselves. Lambda expressions are simple, elegant, and powerful. Literally anything that can be expressed as a computable algorithm can be expressed through lambdas.

  * _Object literal notation_ is the fastest route to a working object instance from scratch. Also take advantage of its sibling, _array literal notation_.

  * _Dynamic object extension_ allows you to easily use mixins, composition, and aggregation for code reuse, even after object instantiation. Subclassing requires a lot of extra typing, and gains you nothing. It makes your code brittle and inflexible.

  * _Prototypes_ allow you to clone instances of existing objects to create new ones, and share generic methods on the `prototype` property.

  * _Factories_ are a more flexible and less verbose alternative to constructor functions in JavaScript. You don't need constructors to create new objects. Any function in JavaScript can return a new object, and with less code than a constructor requires. Unlike constructors, factories hide instantiation details from the caller, and there's no need to use the awkward and superfluous `new` keyword when invoking a factory. Factories make it easy to combine any of the above techniques, and even change implementations at run-time without changing the way that objects are instantiated from outside the factory.

  * _Fluent APIs_. (Not to be confused with fluent style JavaScript). Prototypes make it easy to build fluent APIs. A fluent API is an interface that reads a bit like natural language. Fluent APIs are usually chainable, but don't need to be chained to be fluent. The defining characteristic is that each method returns an object which has additional methods on it which make sense as a next step. In this way, methods can be strung together in short sentences, each method acting on the return value of the last. jQuery and Jasmine are good examples of popular fluent APIs in JavaScript.

Some of the techniques used in fluent style JavaScript were popularized by the Prototype and jQuery libraries, but fluent style wasn't invented by anybody in particular. There isn't anything new or unique about it that hasn't been said a thousand times before. It's simply the natural evolution of a language with this particular blend of features at its core. It is not a style in itself so much as the shedding of unnecessary styles and techniques which are often habits left over from other languages.

Even giving "fluent style" a name seems a bit silly. It's necessary only to distinguish it from the cumbersome styles taught in most JavaScript books and tutorials. In short, fluent style isn't really a particular formal style -- _It's just what fluent JavaScripter's do_.

As time marches on, it's natural to assume that fluent style JavaScript will and should evolve beyond this definition, as new efficiencies are discovered and popularized.

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
