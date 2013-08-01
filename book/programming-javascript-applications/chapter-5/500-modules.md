# [译] [PJA] [500] 第五章 模块

> * Original: [Chapter 5. Modules](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html)
> * Translated by: [cssmagic](https://github.com/cssmagic)

# Chapter 5. Modules

Modules are reusable software components which form the building blocks of applications. Modularity satisfies some very important design goals, perhaps the most important of which is simplicity.

When you design an application with a lot of interdependencies between different parts of the application, it becomes more difficult to fully understand the impact of your changes across the whole system.

When you design part of a system to a modular interface contract, you can safely make changes without having a deep understanding of all of the related modules.

Another important goal of modularity is the ability to reuse your module in other applications. Well designed modules built on similar frameworks should be easy to transplant into new applications with few (if any) changes. By defining a standard interface for application extensions, and then building your functionality on top of that interface, you'll go a long way toward building an application that is easy to extend and maintain, and easy to reassemble into different forms in the future.

JavaScript modules are encapsulated, meaning that they keep implementation details private, and expose a public API. That way, you can change how a module behaves under the hood without changing code that relies on it. Encapsulation also provides _protection_, meaning that it prevents outside code from interfering with the functionality of the module.

There are several ways to define modules in JavaScript. The most popular and common are the module pattern, CommonJS modules (used in Node), and AMD (Asynchronous Module Definition).

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
