# [译] [PJA] [100] 第一章 JavaScript 革命

> * Original: [Chapter 1. The JavaScript Revolution - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch01.html)
> * Translated by: [cssmagic](https://github.com/cssmagic)

# Chapter 1. The JavaScript Revolution

# 第一章 JavaScript 革命

In case you haven’t heard, JavaScript is arguably the most important programming language on Earth. Once thought of as a toy, JavaScript is now the most widely deployed programming language in history. Almost everyone with a computer or a smart phone has all the tools they need to execute JavaScript programs, and create their own. All you need is a browser and a text editor.

你可能还不知道，JavaScript 可以说是地球上最重要的程序语言。虽然曾经被看作“玩具语言”，但 JavaScript 目前已经成为史上应用最广的程序语言。拥有一台电脑或智能手机，就相当于拥有了执行（甚至创建）JavaScript 程序的必备工具。你所需要的一切，就是一款网页浏览器，加上文本编辑器。

JavaScript, HTML and CSS have become so prevalent that many operating systems have adopted the open web standards as the presentation layer for native apps. Even Microsoft announced that the Windows 8 UI will be driven by open web standards.

JavaScript、HTML 和 CSS 已经变得如此流行，以至于许多操作系统已经采用了这些开放的 Web 标准作为原生应用的表现层。甚至微软公司也声称 Windows 8 的 UI 层将由这些开放的 Web 标准技术来驱动。

Creating a JavaScript program is as simple as editing a text file and opening it in the browser. No complex development environments to download and install. No complex IDE to learn. JavaScript is easy to learn, too. The basic syntax is immediately familiar to any programmer who has been exposed to the C family syntax. No other language can boast a barrier to entry as low as JavaScript.

创建一个 JavaScript 程序十分简单，编辑一个文本文件并在浏览器中打开它就行了。并不需要下载安装复杂的开发环境；不需要学习复杂的 IDE 工具；JavaScript 语言本身也十分易学。对于任何一个接触过类 C 语法的程序员来说，JavaScript 的基本语法可以立即上手。没有其它语言可以像 JavaScript 这样拥有如此之低的学习门槛。

That low barrier to entry is probably the main reason that JavaScript was once widely (perhaps rightly) shunned as a toy. It was mainly used to create UI effects in the browser. That situation has changed.

“低门槛”可能就是当年 JavaScript 被普遍地贬低为一门玩具语言的主要原因（或许人们并没说错）。那时它主要用于在浏览器中创建 UI 特效。但是，时局已变。

For a long time, there was no way to save data with JavaScript. If you wanted data to persist, you had to submit a form to a web server and wait for a page refresh. That hindered the process of creating responsive and dynamic web applications. However, in 2000, Microsoft started shipping Ajax technology in Internet Explorer. Soon after, other browsers added support for the XMLHttpRequest object.

在相当长的时间内，JavaScript 无法保存数据。如果你想把数据持久保存，就只能通过表单把数据提交到一台 Web 服务器，并等待页面刷新。这阻碍了创建快速响应的、动态的 Web 应用的步伐。然而在 2000 年，微软开始在 IE 浏览器中搭载 Ajax 技术。此后不久，其它浏览器也纷纷增加了对 XMLHttpRequest 对象的支持。

In 2004, Google launched Gmail. Initially applauded because it promised users nearly infinite storage for their email, Gmail also brought a major revolution. Gone were the page refreshes. Under the hood, Gmail was taking advantage of the new Ajax technology, creating a single page, fast, responsive web application that would forever change the way that web applications are designed.

2004 年，Google 发布 Gmail。这项服务首先通过近乎无限的存储空间赢得了用户的喝彩，同时它也引发了一场重大变革。“页面刷新”一去不复返了。在程序内部，Gmail 得益于全新的 Ajax 技术，打造了一款单页的、快速响应的 Web 应用程序，它永久改变了 Web 应用程序的设计方式。

Since that time, web developers have produced nearly every type of application, including full blown cloud-based office suites (see [Zoho.com][5]), social APIs like Facebook’s JavaScript SDK, even graphically intensive video games.

从那时开始，Web 开发者们实现了各种类型的应用程序，这其中包括完全成熟的基于云端的办公套件（参见 [Zoho.com][5]）、以 Facebook 的 JavaScript SDK 为代表的社会化 API、甚至是图形密集的电子游戏等等。

JavaScript didn’t just luck into its position as the dominant client side language on the web. It is actually very well suited to be the language that took over the world. It is one of the most advanced and expressive programming languages developed to date. Here are some of the features you may or may not be familiar with:

JavaScript 并没有固步于 Web 客户端的垄断地位，它实际上也十分适合成为那种掌管一切的语言。它是人类迄今为止创造出的最先进、最具表现力的程序语言之一。以下是一些你可能熟悉（或不熟悉）的关于它的特征。

[5]: http://zoho.com/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/18)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
