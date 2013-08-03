# [译] [PJA] [104] 一等公民——函数

> * Original: [First Class Functions - Chapter 1. The JavaScript Revolution - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch01.html#first_class_functions)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## First Class Functions

In JavaScript, objects were not a tacked-on afterthought. Nearly everything in JavaScript is an object, including functions. Because of that feature, functions can be used anywhere you might use a variable, including the parameters in function calls. That feature is often used to define anonymous callback functions for asynchronous operations, or to create _higher order functions_ (functions that take other functions as parameters, return a function, or both). Higher order functions are used in the _functional programming_ style to abstract away commonly repeated coding patterns such as iteration loops, or other instruction sets that differ mostly in the variables or data they consume.

Good examples of functional programing include functions like `.map()`, `.reduce()`, and `.forEach()`. The [Underscore.js][7] library contains many useful functional utilities. For simplicity, we'll be making use of Underscore.js in this book.

[7]: http://documentcloud.github.com/underscore/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
