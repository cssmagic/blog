# [译] [PJA] [400] 第四章 对象

> * Original: [Chapter 4. Objects](http://chimera.labs.oreilly.com/books/1234000000262/ch04.html)
> * Translated by: [cssmagic](https://github.com/cssmagic)

# Chapter 4. Objects

With the combination of prototypal inheritance, the ability to add properties to objects dynamically, and closures, JavaScript has one of the most flexible and expressive object systems available in any popular programing language.

In JavaScript, all types of functions, arrays, key/value pairs, and data structures in general are really objects. Even primitive types get the object treatment when you refer to them with the property access notations. They get automatically wrapped with an object so that you can call their prototype methods. For example:

    'tonya@example.com'.split('@')[1]; // =&gt; example.com

Primitive types behave like objects when you use the property access notations, but you can't assign new properties to them. Primitives get wrapped with an object temporarily, and then that object is immediately thrown away. Any attempt to assign values to properties will seem to succeed, but subsequent attempts to access that new property will fail.

JavaScript's object system is so powerful and expressive that most of the complexity in common OO patterns melts away when you reproduce them in JavaScript. You simply don't need all of the common cruft to accomplish the stated goals. For instance, because JavaScript is classless, and it's possible to create an object on-demand at the precise moment it's needed (lazy instantiation), the singleton is reduced to an object literal:

    var highlander = {
        name: 'McLeod',
        catchphrase: 'There can be only one.'
      };

As you continue through this chapter, you'll see that a lot of the overhead associated with several other GoF design patterns melts away when you understand how to take advantage of JavaScript's native object capabilities.

You may be aware that JavaScript is not a classical OO language. It's a prototypal language. However, most JavaScript training materials ignore some of the implications of that paradigm shift. It's time to get a firmer handle on exactly what _prototypal_ means, and how you can take advantage of it to write better, faster, more readable, and more efficient code. It might help to get a better sense of the shortcomings of classical inheritance, first.

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
