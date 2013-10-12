# [译] [PJA] [600] 第六章 关注点分离

> * Original: [Chapter 6. Separation of Concerns - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch06.html)
> * Translated by: [cssmagic](https://github.com/cssmagic)

# Chapter 6. Separation of Concerns

# 第六章 关注点分离

_Separation of concerns_ is the idea that each module or layer in an application should only be responsible for one thing, and should not contain code that deals with other things. Separating concerns reduces code complexity by breaking a large application down into many smaller units of encapsulated functionality.

**关注点分离** 的意思是，应用程序中的每个模块或逻辑层只应该对一件事情负责，而不应该包含处理其它事情的代码。分离关注点的做法就是把一个大型的应用程序打散为多个稍小一些的封闭功能单元，从而减少代码的复杂度。

It's easy to confuse separation of concerns with employing modules for the construction of your application, but separation of concerns also implies the layering of functionality in your application. For example, n-tier architecture and MVC architectures are the result of separating concerns across the entire application, rather than at the individual module level. The goal of MVC and related patterns is to separate data management and presentation.

我们很容易误以为关注点分离就是对应用程序的结构进行模块划分，其实关注点分离同时也意味着在你的应用程序中对功能进行分层。比如说，多层架构和 MVC 架构就是在整个应用程序范围内进行关注点分离的结果，远远超出了“模块划分”这个层面。MVC 以及类似模式的目标就是为了将数据管理和表现层分离。

Separation of concerns can be expressed as functions, modules, controls, widgets, layers, tiers, services, and so on. The various units of concern vary from one app to the next, and each different app may use a different combination. Functions and modules have already been discussed.

关注点分离可以表现为函数、模块、控件、组件、功能层、逻辑层、服务等等形式。不同的应用程序在实现关注点分离时，可能会采用不同的表现形式和组合形式。其中函数和模块已经在前面的章节讨论过了。（译注：分别是第三章和 [第五章](https://github.com/cssmagic/blog/issues/30)。）

A _control_ is a reusable GUI input that enables user interaction with your application. For example, combo boxes, calendar inputs, sliders, buttons, switches, and knobs are all controls.

**控件** 是一种可重用的 GUI 输入元素，使得用户可以与你的应用程序进行互动。比如说，组合框（译注：即输入框与下拉列表的组合体）、日历选择框、滑动条、按钮、开关和旋钮都属于控件。

A _widget_ is a small application which is intended to be embedded in other applications. For example, WordPress allows developers to offer embeddable units of functionality to blog owners through its plugin ecosystem. There are many widgets to manage calendars, comments, maps, and all sorts of services from third party providers.

**组件**（译注：widget 往往也被译为“窗口小部件”、“挂件”等等）是一种小型应用程序，被设计为可以嵌入到其它应用程序中。比如 WordPress 就允许开发者基于其插件系统向博客站长提供可嵌入的功能单元。这些组件可以管理日历事件、用户评论、地图标记以及各种来自第三方的服务（译注：比如微博挂件等等）。

> `[$]`

> The word widget is historically used to mean the same thing as controls. To avoid confusion, this book will always refer to interactive form inputs as controls (or inputs), and widgets will always refer to embeddable mini applications.

> 一直以来，“组件”这个名词往往也被用作“控件”的同义词。为了避免混淆，在本书中，“控件”仅指那些可交互的表单输入元素，而“组件”仅指那些可嵌入的小型应用程序。

_Layers_ are logical groupings of functionality. For example, a data layer might encapsulate functionality related to data and state, while a presentation layer handles display concerns, such as rendering to the DOM and binding UI behaviors.

**功能层** 是程序功能的逻辑分组。举例来说，“数据层”可能封装了与数据和状态相关的功能；而“表现层”用于处理显示方面的问题，比如渲染 DOM 结构并绑定 UI 交互行为。

_Tiers_ are the runtime environments that layers get deployed to. A runtime environment usually consists of at least one physical computer, an operating system, the runtime engine (e.g., Node, Java, or Ruby) and any configuration needed to express how the application should interact with its environment.

**逻辑层** 是用于部署功能层的各种运行时环境。一个运行时环境至少要包含一台实体计算机、一个操作系统、一种运行时引擎（比如 Node、Java 或 Ruby）以及一些必不可少的配置信息——这些配置信息描述了应用程序应该如何与其所处的环境进行交互。

It's possible to run multiple layers on the same tier, but tiers should be kept independent enough that they can easily be deployed to separate machines, or even separate data centers. For large scale applications, it's usually also necessary that tiers can scale horizontally, meaning that as demand increases, you can add machines to a tier in order to improve its capacity to meet that demand.

在同一个逻辑层内运行多个功能层是可能的，但逻辑层必须保持足够独立，以便部署到分离的多台机器、甚至是分离的多个数据中心中（译注：互联网数据中心，IDC，指的是专业的服务器机房及相关设施）。对于大规模应用程序来说，逻辑层还必须具备水平伸缩能力。这意味着一旦需求增长，你只需要为逻辑层增加机器就可以提升整个应用的处理能力，从而满足这些新增需求。

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
