# [译] [PJA] [600] 第六章 关注点分离

> * Original: [Chapter 6. Separation of Concerns - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch06.html)
> * Translated by: [cssmagic](https://github.com/cssmagic)

# Chapter 6. Separation of Concerns

# 第六章 关注点分离

_Separation of concerns_ is the idea that each module or layer in an application should only be responsible for one thing, and should not contain code that deals with other things. Separating concerns reduces code complexity by breaking a large application down into many smaller units of encapsulated functionality.

*关注点分离* 的意思是，一个应用程序中的每个模块或逻辑层只应该对一件事情负责，而不应该包含处理其它事件的代码。分离关注点将减少代码的复杂度，通过把一个大型的应用程序打散为多个稍小一些的封闭功能单元。

It's easy to confuse separation of concerns with employing modules for the construction of your application, but separation of concerns also implies the layering of functionality in your application. For example, n-tier architecture and MVC architectures are the result of separating concerns across the entire application, rather than at the individual module level. The goal of MVC and related patterns is to separate data management and presentation.

我们很容易误以为关注点分离就是对应用程序的结构进行模块划分，但关注点分离同时也意味着在你的应用程序中对功能进行分层。举个例子，多层架构和 mvc 架构就是在整个应用程序中进行关注点分离的结果，而不仅仅是在单独的模型层面。mvc 和相关模式的目标就是为了将数据管理和表现层分离。

Separation of concerns can be expressed as functions, modules, controls, widgets, layers, tiers, services, and so on. The various units of concern vary from one app to the next, and each different app may use a different combination. Functions and modules have already been discussed.

关注点分离可以表现为函数、模块、控件、组件、功能层、逻辑层、服务等等。

A _control_ is a reusable GUI input that enables user interaction with your application. For example, combo boxes, calendar inputs, sliders, buttons, switches, and knobs are all controls.

A _widget_ is a small application which is intended to be embedded in other applications. For example, [WordPress][4] allows developers to offer embeddable units of functionality to blog owners through its plugin ecosystem. There are many widgets to manage calendars, comments, maps, and all sorts of services from third party providers.

The word widget is historically used to mean the same thing as controls. To avoid confusion, this book will always refer to interactive form inputs as controls (or inputs), and widgets will always refer to embeddable mini applications.

_Layers_ are logical groupings of functionality. For example, a data layer might encapsulate functionality related to data and state, while a presentation layer handles display concerns, such as rendering to the DOM and binding UI behaviors.

_Tiers_ are the runtime environments that layers get deployed to. A runtime environment usually consists of at least one physical computer, an operating system, the runtime engine (e.g., Node, Java, or Ruby) and any configuration needed to express how the application should interact with its environment.

It's possible to run multiple layers on the same tier, but tiers should be kept independent enough that they can easily be deployed to separate machines, or even separate data centers. For large scale applications, it's usually also necessary that tiers can scale horizontally, meaning that as demand increases, you can add machines to a tier in order to improve its capacity to meet that demand.

[4]: http://wordpress.org/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
