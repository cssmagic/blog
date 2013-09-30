# [译] [PJA] [500] 第五章 模块

> * Original: [Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html)
> * Translated by: [cssmagic](https://github.com/cssmagic)

# Chapter 5. Modules

# 第五章 模块

Modules are reusable software components which form the building blocks of applications. Modularity satisfies some very important design goals, perhaps the most important of which is simplicity.

模块是指可重用的软件组件，它们就像是组成应用程序的积木。而 **模块化** 是为了满足一些非常重要的设计目标，这其中最重要的目标可能就是“简单性”。

When you design an application with a lot of interdependencies between different parts of the application, it becomes more difficult to fully understand the impact of your changes across the whole system.

如果你正在设计一款应用程序，而且它由一堆相互依赖的不同部分所组成，那么，要想完全理解某些修改会在整个系统中造成什么样的影响，恐怕是非常困难的。

When you design part of a system to a modular interface contract, you can safely make changes without having a deep understanding of all of the related modules.

而当你把系统中的某个部分设计为一个模块化的接口约定时，你可以安全地进行修改，而不需要对所有相关模块进行深入了解。

Another important goal of modularity is the ability to reuse your module in other applications. Well designed modules built on similar frameworks should be easy to transplant into new applications with few (if any) changes. By defining a standard interface for application extensions, and then building your functionality on top of that interface, you'll go a long way toward building an application that is easy to extend and maintain, and easy to reassemble into different forms in the future.

模块化的另一个重要目标是令模块在其它应用程序中得到重用。模块只要基于相似的框架进行良好的设计，就可以很容易地移植到新的应用程序中，而几乎不需要（即使需要也很少）修改。在对应用程序进行扩展的时候，我们可以先为扩展部分定义一套标准接口，然后在这个接口之上构建新功能——这种方式对于构建一个易于扩展和维护的应用程序来说大有裨益，这样的应用程序也易于在未来重新组装为不同的形态。

JavaScript modules are encapsulated, meaning that they keep implementation details private, and expose a public API. That way, you can change how a module behaves under the hood without changing code that relies on it. Encapsulation also provides _protection_, meaning that it prevents outside code from interfering with the functionality of the module.

JavaScript 模块是封装式的，这意味着它们将实现细节保留在内部，仅向外暴露一套公开 API。这样一来，你就可以随时改变一个模块的内部行为，而不需要同时去修改那些依赖它的外部代码。封装同样提供了一种“保护”，这意味着它会防止外部代码干涉模块内部的功能。

There are several ways to define modules in JavaScript. The most popular and common are the module pattern, CommonJS modules (used in Node), and AMD (Asynchronous Module Definition).

在 JavaScript 中，有多种方法可以定义模块。这其中最流行、最普遍的方法分别是模块模式、CommonJS 模块规范（主要用于 Node）和 AMD（Asynchronous Module Definition，异步模块定义）规范。

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/30)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
