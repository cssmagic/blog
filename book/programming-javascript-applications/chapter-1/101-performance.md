# [译] [PJA] [101] 性能

> * Original: [Performance - Chapter 1. The JavaScript Revolution - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch01.html#performance)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Performance

## 性能

Just in Time compiling -- In modern browsers, most JavaScript is compiled, highly optimized and executed like native code, so run-time performance is close to software written in C or C\+\+. Of course, there is still the overhead of garbage collection and dynamic binding, so it is possible to do certain things faster -- however, the difference is generally not worth sweating over until you’ve optimized everything else. With [Node.js][6] (a high-performance, evented, server-side JavaScript environment built on Google's highly optimized V8 JavaScript engine), JavaScript apps are event-driven and non-blocking, which generally more than makes up the code execution difference between JavaScript and less dynamic languages.

JIT 编译——在现代浏览器中，大多数 JavaScript 代码会被编译和高度优化、并以接近原生代码的方式执行，所以运行时性能非常接近以 C 或 C++ 编写的软件。当然，仍然会存在由垃圾回收和动态绑定所产生的额外消耗，所以在某些场景下后者可能要明显快些——不过这种差异并不值得纠结，除非你已经把其它所有事情都优化到位了。随着 [Node.js][6]（一个基于 Google 高度优化的 V8 引擎的、高性能的、事件化的、服务器端的 JavaScript 环境）的到来，JavaScript 应用开始采用事件驱动、无阻塞的方式。相对于试图弥补 JavaScript 和静态语言之间的代码执行差异，这个思路总体上要强得多。

[6]: http://nodejs.com/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/19)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
